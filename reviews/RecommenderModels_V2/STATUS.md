# Recommender Models Review V2 — Status

## Goal
V2 review focusing on industrial recommender model papers we’ve reviewed together:
- OneRec (V1/V2)
- HSTU (+ Ultra-HSTU)
- PLUM
- OneTrans
- plus additional industry papers and their references

Focus: enrich model categorization + discover new challenges emphasized in industry papers.

## Progress
- Located existing paper folders and existing per-paper LaTeX summaries:
  - papers/Kuaishou_OneRec_V1/summary.tex
  - papers/OneRecTeam_OneRecV2/summary.tex
  - papers/meta_hstu/summary.tex
  - papers/meta_ultra_hstu/summary.pdf
  - papers/google_plum/summary.tex
  - papers/bytedance_onetrans/summary.tex
- Started expanding with additional industry long-history papers; downloaded + parsed:
  - DV365 (Instagram): arXiv:2506.00450
  - LONGER (industrial long sequence): arXiv:2505.04421
  - TransAct V2 (Pinterest): arXiv:2506.02267
  - Monolith (ByteDance infra): arXiv:2209.07663

## Next
- Extract key quotes + key experimental numbers from the 4 new papers (tables + headline claims).
- Draft V2 taxonomy updates:
  - add a dedicated category for **extremely-long-history embeddings + offline feature pipelines** (DV365-style)
  - add **ranking architecture unification** (OneTrans-style) vs **retrieval generative** (PLUM/OneRec/HSTU)
  - add **infra-centric embedding/online training systems** (Monolith-style)
- Write V2 review draft in markdown + LaTeX and push to research_openclaw as a PR.

## ETA
- Taxonomy + challenge expansion draft: ~2–3 hours.
- Full V2 write-up + PDF + PR: ~4–6 hours.
