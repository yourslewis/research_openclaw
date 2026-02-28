# Recommender Model Review V2 (Industry-heavy)

**Scope:** Enrich and reorganize our previous recommender-model reviews (OneRec V1/V2, HSTU, PLUM, OneTrans) by incorporating more *industry* papers and extracting the *recurring challenges* they focus on.

**Evidence discipline:** For the papers we already downloaded locally, key claims below are quoted or paraphrased from the PDF text we extracted.

---

## 1) Updated categorization (V2 taxonomy)

### A. End-to-end generative recommendation / unified retrieval+ranking
**Representative:** OneRec (V1/V2), OneMall.
- Core signature: replace multi-stage cascade with a single generative policy producing item semantic identifiers; use RL-style alignment.
- Key knobs: tokenizer/codebook quality, compute allocation (context vs generation), inference sampling/Pass@K/beam.

### B. Generative recommenders as sequential transducers (foundation-style scaling)
**Representative:** HSTU / Generative Recommenders (Meta), Ultra-HSTU.
- Core signature: cast ranking/retrieval as sequential transduction; emphasize scaling laws and long-context efficiency.

### C. Industrial ranking Transformers that unify sequence modeling + feature interaction
**Representative:** OneTrans (ByteDance).
- Core signature: unify “sequence modeling” and “feature crossing” in one Transformer backbone; leverage KV caching and pyramid token pruning.

### D. Ultra-long history modeling via *offline embeddings* (feature pipeline as the product)
**Representative:** DV365 (Instagram).
- Core signature: don’t run attention over 10k–70k events online; instead, train an upstream model to produce extremely long-history user embeddings stored in a KV store, then reuse across many downstream models.

### E. End-to-end ultra-long sequence Transformers with GPU-centric engineering
**Representative:** LONGER (ByteDance).
- Core signature: push end-to-end long sequence modeling (10k) with architectural tricks (global tokens, token merge) *and* heavy engineering (mixed precision, recomputation, KV cache, synchronous train/serve).

### F. Lifelong sequences inside CTR ranking + serving optimization
**Representative:** TransAct V2 (Pinterest).
- Core signature: incorporate lifelong sequences (O(10^4)) into CTR ranker with next-action auxiliary loss; invest in serving optimizations to keep latency/QPS manageable.

### G. Infra-first systems for online training + embedding tables under concept drift
**Representative:** Monolith (ByteDance).
- Core signature: sparse/dynamic features + concept drift require online training and embedding-table engineering (collisionless tables, expiry, frequency filtering), plus reliability/real-time tradeoffs.

---

## 2) What new challenges industry papers emphasize (cross-paper themes)

### 2.1 Compute allocation is the bottleneck (not “model size”)
- **OneRec-V2** explicitly frames encoder-heavy compute as wasteful for the loss-relevant generation, motivating a “lazy decoder-only” shift.
- More generally: the best wins often come from reallocating FLOPs from context encoding → decision-critical modules.

### 2.2 Ultra-long history is valuable, but *serving it is the real constraint*
- **DV365**: chooses offline embedding over end-to-end long-seq optimization to scale histories to ~70k max / ~40k avg, arguing ROI and infra costs make E2E impractical.
- **TransAct V2**: calls out “infrastructure challenges involved in efficiently serving large-scale sequential models.”
- **LONGER**: emphasizes GPU-efficient design + KV cache serving as first-class contributions.

### 2.3 Two-stage retrieval of “top-k relevant history” creates inconsistency
- **LONGER** explicitly lists two-stage retrieval and indirect modeling as common practice that sacrifices full-sequence information and introduces upstream/downstream inconsistency.

### 2.4 Production is multi-surface / multi-model: shared representations matter
- **DV365** argues for computing expensive long-history features once and sharing across 15+ downstream models.
- **OneMall** and **OneRec** push multi-scenario unification at the model level.

### 2.5 Cascades fragment optimizations; unified backbones reuse LLM infra
- **OneTrans** argues module separation increases latency and prevents reuse of LLM-style optimizations (KV caching, memory-efficient attention, mixed precision).
- **HSTU** and **OneRec** similarly pursue modular blocks / unified policies to better scale.

### 2.6 Alignment: reward modeling vs real feedback
- **OneRec** family pushes RL alignment to reconcile many objectives; OneRec-V2 stresses real-feedback shaping (duration-aware) and stability.
- More broadly: industrial work highlights proxy-reward risks and the cost of rollout/scoring.

### 2.7 Concept drift + online training isn’t optional at scale
- **Monolith** foregrounds non-stationarity and sparse/dynamic features; engineering choices trade reliability for real-time learning.

---

## 3) Representative “insight nuggets” (why these papers are complementary)

- **HSTU**: makes the case that recommendation should scale like LLMs (transduction + scaling laws).
- **OneRec**: takes the next step—unify retrieval+ranking into a generative policy + RL alignment; V2 focuses on compute allocation and real-feedback RL.
- **PLUM**: shows how to adapt an LLM to industrial retrieval via better Semantic IDs + continued pretraining.
- **OneTrans**: argues the ranking stack itself should be one Transformer, not “sequence encoder + feature cross net.”
- **DV365**: shows an alternate route—if 70k history can’t be served, package it as an offline embedding product.
- **LONGER / TransAct V2**: demonstrate that long-history modeling is as much a serving/infra problem as an ML problem.
- **Monolith**: reminds that embedding tables + online training systems can be the dominant constraint in real recommenders.

---

## 4) Paper set we’re building V2 around

### Already reviewed together (core)
- OneRec-V1 (Kuaishou): `papers/Kuaishou_OneRec_V1/`
- OneRec-V2: `papers/OneRecTeam_OneRecV2/`
- HSTU (Meta): `papers/meta_hstu/`
- Ultra-HSTU: `papers/meta_ultra_hstu/`
- PLUM (Google/YouTube): `papers/google_plum/`
- OneTrans (ByteDance): `papers/bytedance_onetrans/`

### Additional industry papers (new for V2)
- DV365 (Instagram): arXiv:2506.00450
- LONGER (ByteDance): arXiv:2505.04421
- TransAct V2 (Pinterest): arXiv:2506.02267
- Monolith (ByteDance): arXiv:2209.07663

---

## 5) Next steps for V2 (planned)
1. Extract “References” sections from OneRec/HSTU/PLUM/OneTrans PDFs into a consolidated bib list; cluster references by theme (long-seq, semantic IDs, RL alignment, infra/embedding tables).
2. Add 5–10 more industry papers via the reference graph (e.g., TransAct/TWIN/SIM/DIN/Monolith/DV365 citations).
3. Produce V2 PDF (LaTeX) and open PR.
