# Hanwen Tao — Project Portfolio / 陶翰文 · 项目作品集

> Computer Science and Technology (B.Eng., expected Jul 2028), Jilin University · Minor in Finance
> 吉林大学 计算机科学与技术（2028 年 7 月毕业）· 金融学辅修
> Exchange student, National Tsing Hua University, Aug 2026 – Jan 2027
> 台湾清华大学交换生 · 2026.08 – 2027.01
> 📧 taohw0624@mails.jlu.edu.cn

---

## About this repository / 关于本仓库

**EN** — A documentation-only portfolio. Each chapter gives the **complete content outline, technical path and layered architecture** of one project, at a level of detail intended for a reader who wants to judge the engineering rather than skim a summary. **No source code is included in this repository.** Where a project's code exists and can be shared, its own private repository is linked from the chapter.

**中文** — 纯文档作品集。每一章给出一个项目的**完整内容大纲、技术路径与分层架构**，详细程度面向"想判断工程质量"而非"快速浏览摘要"的读者。**本仓库不含任何源码。** 若某项目的代码存在且可以分享，会在该章中链接到其独立的私有仓库。

---

## Projects / 项目一览

| # | Project 项目 | Period 时间 | Role 角色 | Scale 规模 | Chapter 章节 |
|---|---|---|---|---|---|
| 1 | **ARTIFEX** — Multi-source data-driven job competency graph system<br>多源异构数据驱动的岗位与能力图谱系统 | 2026.06–09 | Team Lead & Core Developer<br>队长兼核心开发（算法与后端） | 48 commits, 391 files, ~60k lines added<br>4-person team | [→ 01](docs/01-artifex.md) |
| 2 | **DNN Inference Optimization** — structured pruning for edge deployment<br>资源受限设备上的深度神经网络推理优化 | 2026.04–07 | Independent 独立完成 | Params & FLOPs −75%<br>Top-1 95.70% → 91.84% | [→ 02](docs/02-dnn-inference-optimization.md) |
| 3 | **Mental Health Digital Twin** — LLM-based multimodal clinical platform<br>心镜智语 · 基于大语言模型的多模态心理健康数字孪生平台 | 2025.05–11 | Backend Team Lead<br>后端组负责人 | 13-person backend group<br>Deployed in hospital production | [→ 03](docs/03-mental-health-digital-twin.md) |
| 4 | **Gomoku-Swap1 Game AI** — tournament engine on Botzone<br>五子棋 Swap1 博弈 AI | 2026.03–05 | Team Lead 队长 | ~3.8k lines C++<br>800 ms per move · top 5% of course ladder | [→ 04](docs/04-gomoku-swap1-ai.md) |
| 5 | **Hospital Information System** — three-role C++ system<br>轻量级医院信息管理系统 | 2025.03–2026.05 | Team Lead 队长 | ~6.4k lines C++ · graded A+ | [→ 05](docs/05-hospital-information-system.md) |

---

## What connects them / 贯穿的主线

**EN** — Three concerns recur across every project, and they are what I would want a supervisor to judge me on:

1. **Making claims measurable.** Every project ends with numbers produced by a harness I wrote, not by inspection: an 18-round iteration log with F1 at each round (ARTIFEX), a four-dimension accuracy/FLOPs/parameter/latency comparison (pruning), a 12-algorithm benchmark with an adversarial hard-case suite (matching), a pattern-classification regression check (Gomoku).
2. **Separating what is decided from what is computed.** The recurring architectural move is putting a bounded interface between a deterministic core and a probabilistic or external component — rule-based candidate analysis vs. LLM generation in ARTIFEX, backend session state vs. model services in the clinical platform, tactical layer vs. search core in the game engine.
3. **Being honest about what the numbers mean.** I keep synthetic results and real-data results labelled separately, including when the real-data numbers are much worse — see the [ARTIFEX evaluation section](docs/01-artifex.md#7-evaluation--what-the-numbers-do-and-do-not-mean--评测与数字的边界), where the rule-based line scores F1 0.964 on synthetic data and P 0.158 on 81 real postings, and both are reported.

**中文** — 三条关注点贯穿所有项目，也是我希望导师据以判断我的地方：

1. **把结论变成可度量的。** 每个项目最终都有由我自己写的评测工具产出的数字，而不是"看起来不错"：18 轮迭代每轮记录 F1（ARTIFEX）、精度/FLOPs/参数量/延迟四维对比（剪枝）、含极限难例的 12 算法基准（匹配）、棋型判定回归检查（五子棋）。
2. **把"判定"与"计算"分开。** 反复出现的架构动作是：在确定性内核与概率性/外部组件之间放一层有界接口——ARTIFEX 中规则候选分析与 LLM 生成分离、临床平台中后端会话状态与模型服务分离、棋类引擎中战术层与搜索核心分离。
3. **对数字的含义保持诚实。** 合成数据结果与真实数据结果分别标注，即使真实数据上的表现差很多也如实报告——见 [ARTIFEX 评测一节](docs/01-artifex.md#7-evaluation--what-the-numbers-do-and-do-not-mean--评测与数字的边界)：规则线在合成数据上 F1 0.964，在 81 条真实 JD 上精确率仅 0.158，两者都记录在案。

---

## Research interests / 研究兴趣

- **LLM-based systems and agents** — orchestration, observability and cost attribution, evaluation harnesses, regression gating, tool-use safety.
  **大语言模型系统与智能体** —— 编排、观测与成本归因、评测体系、回归门禁、工具调用安全。
- **Machine learning on text and graph data** — information extraction, skill/competency ontologies, novelty detection, matching and ranking.
  **文本与图数据上的机器学习** —— 信息抽取、技能本体、新颖性检测、匹配与排序。
- **Efficient deep learning and edge intelligence** — model compression (structured pruning, quantisation), on-device inference, efficiency/accuracy trade-offs.
  **高效深度学习与边缘智能** —— 模型压缩（结构化剪枝、量化）、端侧推理、效率与精度的权衡。

---

## Technical background / 技术背景

| Area 领域 | Detail 内容 |
|---|---|
| **Programming** | Python, C/C++, Java, JavaScript, SQL, Verilog |
| **ML / DL** | PyTorch, scikit-learn, NumPy/pandas; structured pruning, TF-IDF + linear models, cross-validation, bootstrap stability, hypothesis testing with multiple-comparison correction |
| **Backend & data** | FastAPI, SQLAlchemy/Alembic, PostgreSQL, Neo4j (Cypher), Qdrant, LangGraph, OpenAPI contract-first design, pytest, Postman |
| **Engineering** | Linux, Git, Docker, CI regression gating, code review, AI-assisted development (Claude Code, Cursor, Codex) |
| **Coursework** | Data Structures, Algorithm Design & Analysis, Operating Systems, Computer Organization & Architecture, CPU Design, Digital Logic Circuits, Linux Practice, Probability & Statistics, Linear Algebra, Mathematical Modeling; *(NTHU, in progress)* Machine Learning, Natural Language Processing, Compilers, Computer Networks, Database Systems, Software Engineering |

---

## Honors / 获奖

- **Honorable Mention**, Mathematical Contest in Modeling (MCM), COMAP — 2026
- **First Prize**, Jilin University Mathematical Contest in Modeling — 2026
- **Third Prize**, Lanqiao Cup Programming Competition, Jilin provincial round (C/C++ Group A) — 2026
- **Outstanding Student**, College of Computer Science and Technology, Jilin University — 2024 & 2025
- **"Excellent"** conclusion rating, JLU Innovation & Entrepreneurship Training Program — 2025

---

## Reading guide / 阅读建议

| If you have… 若你有 | Read 建议阅读 |
|---|---|
| 5 minutes | This page, plus the [ARTIFEX summary](docs/01-artifex.md#1-summary--摘要) |
| 20 minutes | [ARTIFEX](docs/01-artifex.md) in full — it is the project with the most original algorithmic work 算法原创性最强 |
| Interest in efficient ML 关注高效 ML | [DNN Inference Optimization](docs/02-dnn-inference-optimization.md) |
| Interest in systems/teams 关注系统与团队 | [Mental Health Digital Twin](docs/03-mental-health-digital-twin.md) |
| Interest in search/algorithms 关注搜索与算法 | [Gomoku-Swap1](docs/04-gomoku-swap1-ai.md) |
