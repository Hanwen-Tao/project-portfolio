<div align="right"><a href="README.zh-CN.md">中文</a></div>

# Hanwen Tao — Project Portfolio

B.Eng. Computer Science and Technology (expected July 2028), Jilin University · Minor in Finance
Exchange student, National Tsing Hua University, August 2026 – January 2027
📧 taohw0624@mails.jlu.edu.cn

---

## About this repository

A documentation-only portfolio. Each chapter gives the **complete content outline, technical path and layered architecture** of one project, at a level of detail intended for a reader who wants to judge the engineering rather than skim a summary. **No source code is included here.** Where a project's code exists and can be shared, its own private repository is linked from the chapter.

---

## Projects

| # | Project | Period | Role | Scale | Chapter |
|---|---|---|---|---|---|
| 1 | **ARTIFEX** — multi-source data-driven job competency graph system | 2026.06–09 | Team Lead & Core Developer (Algorithms & Backend) | 48 commits, 391 files, ~60k lines added; 4-person team | [→ 01](docs/01-artifex.md) |
| 2 | **DNN Inference Optimization** — structured pruning for edge deployment | 2026.04–07 | Independent | Params and FLOPs −75%; top-1 95.70% → 91.84% | [→ 02](docs/02-dnn-inference-optimization.md) |
| 3 | **Mental Health Digital Twin** — LLM-based multimodal clinical platform | 2025.05–11 | Backend Team Lead | 13-person backend group; deployed in hospital production | [→ 03](docs/03-mental-health-digital-twin.md) |
| 4 | **Gomoku-Swap1 Game AI** — tournament engine on Botzone | 2026.03–05 | Team Lead | ~3.8k lines C++; 800 ms per move; top 5% of course ladder | [→ 04](docs/04-gomoku-swap1-ai.md) |
| 5 | **Hospital Information System** — three-role C++ system | 2025.03–2026.05 | Team Lead | ~6.4k lines C++; graded A+ | [→ 05](docs/05-hospital-information-system.md) |

---

## What connects them

Three concerns recur across every project, and they are what I would want a supervisor to judge me on.

**1 · Making claims measurable.** Every project ends with numbers produced by a harness I wrote, not by inspection: an 18-round iteration log with F1 at each round (ARTIFEX), a four-dimension accuracy/FLOPs/parameter/latency comparison (pruning), a 12-algorithm benchmark with an adversarial hard-case suite (matching), a pattern-classification regression check (Gomoku).

**2 · Separating what is decided from what is computed.** The recurring architectural move is putting a bounded interface between a deterministic core and a probabilistic or external component — rule-based candidate analysis versus LLM generation in ARTIFEX, backend session state versus model services in the clinical platform, tactical layer versus search core in the game engine.

**3 · Being honest about what the numbers mean.** Synthetic results and real-data results stay labelled separately, including when the real-data numbers are much worse — see the [ARTIFEX evaluation section](docs/01-artifex.md#7-evaluation--what-the-numbers-do-and-do-not-mean), where the rule-based line scores F1 0.964 on synthetic data and precision 0.158 on 81 real postings, and both are reported.

---

## Research interests

- **LLM-based systems and agents** — orchestration, observability and cost attribution, evaluation harnesses, regression gating, tool-use safety.
- **Machine learning on text and graph data** — information extraction, skill and competency ontologies, novelty detection, matching and ranking.
- **Efficient deep learning and edge intelligence** — model compression (structured pruning, quantisation), on-device inference, efficiency/accuracy trade-offs.

---

## Technical background

| Area | Detail |
|---|---|
| **Programming** | Python, C/C++, Java, JavaScript, SQL, Verilog |
| **ML / DL** | PyTorch, scikit-learn, NumPy/pandas; structured pruning, TF-IDF + linear models, cross-validation, bootstrap stability, hypothesis testing with multiple-comparison correction |
| **Backend & data** | FastAPI, SQLAlchemy/Alembic, PostgreSQL, Neo4j (Cypher), Qdrant, Redis, MinIO, LangGraph, OpenAPI contract-first design, pytest, Postman |
| **Engineering** | Linux, Git, Docker, CI regression gating, code review, AI-assisted development |
| **Coursework** | Data Structures, Algorithm Design & Analysis, Operating Systems, Computer Organization & Architecture, CPU Design, Digital Logic Circuits, Linux Practice, Probability & Statistics, Linear Algebra, Mathematical Modeling; *(NTHU, in progress)* Machine Learning, Natural Language Processing, Compilers, Computer Networks, Database Systems, Software Engineering |

---

## Honors

- **Honorable Mention**, Mathematical Contest in Modeling (MCM), COMAP — 2026
- **First Prize**, Jilin University Mathematical Contest in Modeling — 2026
- **Third Prize**, Lanqiao Cup Programming Competition, Jilin provincial round (C/C++ Group A) — 2026
- **Outstanding Student**, College of Computer Science and Technology, Jilin University — 2024 & 2025
- **"Excellent"** conclusion rating, JLU Innovation & Entrepreneurship Training Program — 2025

---

## Reading guide

| If you have… | Read |
|---|---|
| 5 minutes | This page, plus the [ARTIFEX summary](docs/01-artifex.md#1-summary) |
| 20 minutes | [ARTIFEX](docs/01-artifex.md) in full — the project with the most original algorithmic work |
| An interest in efficient ML | [DNN Inference Optimization](docs/02-dnn-inference-optimization.md) |
| An interest in systems and teams | [Mental Health Digital Twin](docs/03-mental-health-digital-twin.md) |
| An interest in search and algorithms | [Gomoku-Swap1](docs/04-gomoku-swap1-ai.md) |
