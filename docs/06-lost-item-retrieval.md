<div align="right"><a href="../README.md">← Portfolio</a> · <a href="zh-CN/06-lost-item-retrieval.md">中文</a></div>

# 06 · Multimodal Lost-and-Found Item Retrieval (Image + Text)

| | |
|---|---|
| **Period** | September 2026 – present |
| **Role** | Team Lead & Main Developer (two-person team) |
| **Course** | Introduction to Artificial Intelligence, National Cheng Kung University (cross-university enrollment during the NTHU exchange) |
| **Status** | Mid-term and final experiments done; final report and video due 26 November 2026 |
| **Code** | [`Hanwen-Tao/Lost-Item`](https://github.com/Hanwen-Tao/Lost-Item) (private — access on request) |

---

## Contents

1. [Summary](#1-summary)
2. [Problem](#2-problem)
3. [Benchmark — and its limitation](#3-benchmark--and-its-limitation)
4. [Technical path: the retrieval core](#4-technical-path-the-retrieval-core)
5. [Two trained models](#5-two-trained-models)
6. [Layered view of the system](#6-layered-view-of-the-system)
7. [Results](#7-results)
8. [Measurement discipline](#8-measurement-discipline)
9. [Limitations and next steps](#9-limitations-and-next-steps)

---

## 1. Summary

A retrieval system that helps an owner find a lost item among the records of a campus lost-and-found office, using whatever the owner has: a photo, a description in Chinese or English, and roughly when and where the item was lost. Four cue scores (two image cues, a cross-modal cue and a cross-lingual text cue) are standardised per query, fused, and combined with a time–place prior. Version 2 adds a LoRA-fine-tuned image encoder and a learned confidence model, and wraps the whole thing in a three-tier web system.

With photos only, LoRA fine-tuning raised Recall@1 from 0.735 to **0.949** in 5-fold cross-validation and from 0.731 to **0.943** on the held-out second campus. The benchmark is built from CO3D object videos rather than real lost-and-found photos; §3 explains what that means for these numbers.

---

## 2. Problem

A lost-and-found office logs each found item with a photo, a short description, and where and when it was found. An owner searching later may have a photo from before the loss, a description, both or neither, and only an approximate time window and set of places. The task is to rank the office's open records so that the owner's item appears at the top.

Three things make it harder than plain image search:

- **Modalities are missing in different combinations.** The fusion has to work with any subset of cues, without a separate model per combination.
- **The two photos look different.** The office photographs the item on a desk; the owner's photo shows it in use, from another angle, in another light.
- **Many items look alike.** Black backpacks, white cups and silver laptops are common, so appearance alone is often ambiguous, and time and place have to carry part of the decision.

---

## 3. Benchmark — and its limitation

Real lost-and-found photos with ground-truth pairs are not available, so the two-campus benchmark is constructed from **CO3D** (Reizenstein et al., ICCV 2021; CC BY-NC 4.0, non-commercial use):

| | |
|---|---|
| One item | One CO3D object video |
| Office photo | One frame, cut out with its mask and placed on a plain desk background |
| Owner photos | Two far-apart frames of the same video, in the original scene |
| Time and place | Simulated on real campus places (OpenStreetMap) |
| Size | NCKU 185 items (370 queries) · NTHU 106 items (212 queries), ten everyday categories |

**Limitation.** Office and owner photos come from the same video, so this is a synthetic pairing, and it is stated as such in every report. The training pool for the fine-tuned encoder (2,920 other CO3D objects) is checked to share no sequence with the benchmark — 1,546 benchmark sequences were excluded when it was built.

Office-side descriptions were generated with a local Qwen3.5-4B model (4-bit). Owner-side descriptions are meant to be written by hand; until then, text experiments use descriptions simulated by a different vision-language model from the owner photos, and are labelled as simulated.

---

## 4. Technical path: the retrieval core

```
owner query (photo? description? time window? places?)
  │
  ├─ Grounding DINO crop ──── uses the category word from the description, or the generic prompt "object"
  │
  ├─ cue scores against every open record
  │     img    DINOv2 image ↔ image                  (cropped)
  │     img2   SigLIP 2 image tower ↔ image          (cropped; LoRA-fine-tuned in v2)
  │     cross  SigLIP 2 photo ↔ description, both directions averaged
  │     txt    BGE-M3 description ↔ description      (Chinese / English)
  │
  ├─ per-query z-score of each cue → weighted sum; a missing cue drops out of numerator and denominator
  │
  ├─ time–place prior: hard time filter with 6-hour tolerance
  │                    + exponential decay in distance and time difference
  │
  └─ ranked list with per-cue evidence and a plain-language reason
```

**Why per-query z-scores.** Raw cosine similarities from different encoders live on different scales, and a query with a strong photo produces larger spreads than a query with a vague description. Standardising each cue across the gallery for that one query puts them on a common footing and lets a missing cue simply drop out. It also requires the full score distribution for the query, which is why scoring is exact over the gallery rather than approximate nearest-neighbour (galleries are hundreds to a few thousand records, under 10 ms in NumPy).

**Tuning protocol.** Weights and prior parameters are chosen by item-grouped 5-fold cross-validation on NCKU. NTHU is the external test: parameters are fixed on the full NCKU set and NTHU is run once. Baselines are BM25 keyword search and every single cue on its own.

**A negative result kept on record.** Local-feature re-ranking (SuperPoint + LightGlue) was implemented, cached and cross-validated, did not help, and is off by default.

---

## 5. Two trained models

### Model 1 — image encoder fine-tuned with LoRA

| | |
|---|---|
| Base | `google/siglip2-base-patch16-224`, vision tower only |
| LoRA | r 16, α 32, dropout 0.1; pooling head trained in full; 9,741,312 trainable parameters |
| Loss | Supervised contrastive, τ = 0.05 |
| Batches | 24 objects × 4 views, built from groups of similar items (same category, similar colour) so that every batch contains hard negatives |
| Optimisation | AdamW, lr 1.5e-4, 5% warm-up, 2,500 steps, fp16, gradient checkpointing |
| Hardware | RTX 4070 Laptop, 8 GB — 106.5 minutes |
| Selection | Best development MRR at step 1,900 (0.987; the pretrained encoder scores R@1 0.719 on the same 299 development objects) |

LoRA is applied only to the vision tower that serves the appearance cue. The cross-modal cue keeps the original SigLIP 2, because fine-tuning one tower alone would break its alignment with the text tower.

### Model 2 — learned confidence

A logistic regression over one feature row per (query, candidate): each cue's z-score and raw cosine, missing-cue flags, the prior's time and place terms, and the candidate's rank under each cue. It outputs the probability that a candidate is the same item, and a score for "the item is probably not in the gallery yet". Cross-validated on NCKU with item-grouped folds: absent-item AUC 0.969, Brier score 0.003. The probabilities are attached to results and do not reorder them.

---

## 6. Layered view of the system

```
┌──────────────────┐   REST, JWT    ┌──────────────────────────┐   HTTP    ┌────────────────────────────┐
│ web              │ ─────────────▶ │ api (FastAPI)            │ ────────▶ │ model service (FastAPI)    │
│ React + TS, Vite │                │ accounts and roles       │  /embed   │ stateless: Grounding DINO, │
│ mobile-first,    │ ◀───────────── │ found items, lost reports│  /rerank  │ SigLIP 2 (+LoRA), DINOv2,  │
│ campus map       │    photos      │ scoring = retrieval core │           │ BGE-M3, model 2            │
└──────────────────┘                │ auto-match, claims       │           └────────────────────────────┘
                                    └────────────┬─────────────┘
                                                 │ SQLAlchemy
                                         ┌───────▼────────┐
                                         │ PostgreSQL     │  (SQLite in tests)
                                         │ + photo volume │
                                         └────────────────┘
```

| Decision | Reason |
|---|---|
| The model service is stateless; the backend is the only source of truth | Restarting the model service or swapping the adapter loses no data |
| Online scoring reuses the offline NumPy code and the same configuration | The numbers in the report describe what the demo actually does |
| Every stored vector carries a `model_version` | Vectors from different adapters are never mixed |
| A toy model backend (no PyTorch) | Front end, back end and CI run without downloading about 5 GB of models |
| Photos are stripped of EXIF/GPS on upload | Privacy |

When the office registers a new item, the backend scores it against every open lost report on that campus and notifies an owner if it enters their top 3 above a threshold. Claims go through staff approval. Docker Compose starts four containers (database, model service, API, nginx-served web); CI builds all four with the toy model and runs an end-to-end smoke test.

---

## 7. Results

### Main comparison (Recall@1)

| Condition | NCKU 5-fold CV, v1 → v2 | NTHU external test, v1 → v2 |
|---|---|---|
| Photo only | 0.735 → **0.949** | 0.731 → **0.943** |
| Photo + time and place | 0.857 → **0.951** | 0.901 → **0.967** |
| Photo + simulated owner description + time and place | — → 0.992 | — → 0.981 |

v1 uses the pretrained encoders; v2 swaps in the LoRA encoder. The last row uses simulated owner descriptions (§3).

### Final-stage experiments

| Experiment | Finding |
|---|---|
| **Prior sensitivity** — re-simulate time and place under other rules | If items can be carried anywhere on campus, the prior misleads (photo condition 0.940 → 0.866), so the deployed configuration halves the prior |
| **Larger gallery** — add the other campus and the training pool, up to 3,363 records | Photo-only NCKU R@1 0.949 → 0.876; the last step adds items the encoder was trained on, so it is optimistic; with unseen items only (742 records) R@1 is 0.927 |
| **Failure analysis** — the 11 photo-only queries ranked below 5th, inspected one by one | 8 had the crop box on a background object, 1 owner photo barely showed the item, 2 were genuine appearance errors |
| **Crop + full-image average** — a rule found after looking at those failures | On held-out pool objects 0.948 → 0.955 (within noise), misses beyond rank 5 fell from 8 to 2. Reported as post-hoc and **not deployed** |
| **End-to-end** — 582 benchmark queries sent over HTTP to the running system | 1,745 of 1,746 query × condition ranks identical to the offline evaluation (the remaining one ranks 77th offline, beyond the 50 results returned); about 0.9 s per new photo |

---

## 8. Measurement discipline

- **Frozen run outputs.** The raw outputs behind every reported table are kept in the repository with SHA-256 checksums. A script recomputes Recall@1 and MRR from the saved per-query ranks and compares them with both the run's own summary and the numbers typed into the report.
- **Full re-run.** One script re-runs every offline final-stage experiment with the report's parameters; all 18 outputs reproduced exactly.
- **Online equals offline.** The deployed system is checked query by query against the offline evaluation (above), and a re-run after review fixes reproduced all 2,328 ranks across both campuses.
- **No tuning on the test set.** Folds are split by item, NTHU is run once, and post-hoc ideas are labelled as such.
- **Tests.** Core 44, backend 13, model service 6, front end 9; all run in CI without a GPU.

---

## 9. Limitations and next steps

### Known limitations

- **Synthetic pairing.** Office and owner photos come from the same CO3D video. The main open question is whether the conclusions hold on real photos.
- **Simulated metadata and descriptions.** Time and place are simulated; owner descriptions are simulated until hand-written ones exist. The prior-sensitivity experiment shows how much depends on the simulation rule.
- **Cropping is the main failure.** Most remaining misses come from the detector boxing a background object when there is no description to name the category.

### Next steps

- A small real-object check: photograph 10–20 real items to test whether the CO3D conclusions transfer.
- Hand-written owner descriptions for about 20 items, compared with the simulated ones.
- Harder tests: galleries restricted to same-category, same-colour look-alikes.
