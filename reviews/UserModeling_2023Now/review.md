# User Modeling in Recommender Systems (2023–present)

**Scope note (evidence-backed):** The “Representative models and results” section below includes **verbatim quotes** and **reported experimental numbers** only for papers whose PDFs were downloaded and parsed locally (curl → pdftotext). I avoided adding any numbers I couldn’t locate in the paper text.

## 1) Backgrounds and challenges
User modeling in modern recommender systems is no longer just “encode the last N clicks”. Since 2023, several pressures have shaped new approaches:

- **Non-stationarity / interest drift:** user intent shifts across minutes→months; continual updates risk catastrophic forgetting.
- **Very long histories:** production systems want hundreds to thousands of actions; naive Transformer attention scales poorly.
- **Heterogeneous and multi-surface behavior:** the same user expresses intent across feeds, search, live, short video, product cards, etc.
- **Representation limits:** fixed embeddings may under-capture multi-aspect semantics and uncertainty.
- **Objective mismatch:** offline ranking improvements don’t always translate to online gains; bridging retrieval↔ranking remains hard.
- **Industrial constraints:** latency, memory (embedding tables), and training cost often dominate architecture choice.

## 2) Category of approaches (taxonomy)
A practical categorization by how user state is represented and updated:

A. **Scaled sequential transducers / “generative recommenders”** (recommendation as sequence transduction; scaling laws + long history).

B. **Efficient long-sequence encoders**
- selective SSMs (e.g., Mamba-family)
- sparsity / stochastic length / compression

C. **LLM-based user modeling for SR (sequential recommendation)**
- C1: LLM as SR model via instruction/prompt/PEFT
- C2: distill sequential knowledge from strong CF-SR into LLMs
- C3: LLM as embedding model with CF awareness (transfer + OOD)
- C4: distillation/pruning to small LMs for deployability

D. **Diffusion-based SR** (denoise/generate next-item embedding with history guidance; address uncertainty / collapse).

E. **Tokenization / Semantic-ID / multi-token item representations** (items as tokens; list-wise generation and/or cross-domain generalization).

F. **Industrial unified multi-scenario end-to-end systems** (single family across surfaces + RL alignment between retrieval and ranking).

## 3) Representative models and results (quotes + numbers)

### A) Scaled sequential transducers / generative recommenders

**(1) Zhai et al., 2024 — HSTU / Generative Recommenders**
- Paper: “Actions Speak Louder than Words: Trillion-Parameter Sequential Transducers for Generative Recommendations” (ICML’24)
- arXiv: https://arxiv.org/abs/2402.17152
- Quote (abstract): “We reformulate recommendation problems as sequential transduction tasks within a generative modeling framework (“Generative Recommenders”), and propose a new architecture, HSTU…”
- Quote (abstract): “HSTU outperforms baselines over synthetic and public datasets by up to 65.8% in NDCG, and is 5.3x to 15.2x faster…”
- Public benchmarks (Table 4):
  - ML-1M: SASRec(2023) HR@10 **0.2853** → HSTU **0.3097** → HSTU-large **0.3294**; NDCG@10 **0.1603** → **0.1720** → **0.1893**.
  - ML-20M: SASRec(2023) HR@10 **0.2906** → HSTU **0.3252** → HSTU-large **0.3567**; NDCG@10 **0.1621** → **0.1878** → **0.2106**.

### B) Efficient long-sequence encoders (SSMs)

**(2) Liu et al., 2024 — Mamba4Rec**
- Paper: “Mamba4Rec: Towards Efficient Sequential Recommendation with Selective State Space Models”
- arXiv: https://arxiv.org/abs/2403.03900
- Quote (abstract): “Although Transformer-based models have proven to be effective for sequential recommendation, they suffer from the inference inefficiency problem stemming from the quadratic computational complexity…”
- Quote (abstract): “we propose Mamba4Rec, which is the first work to explore the potential of selective SSMs for efficient sequential recommendation.”
- Numbers (Table 1):
  - MovieLens-1M: SASRec HR@10 **0.2977**, NDCG@10 **0.1687**, MRR@10 **0.1294** vs Mamba4Rec HR@10 **0.3121**, NDCG@10 **0.1822**, MRR@10 **0.1425**.
  - Amazon-Video-Games: SASRec NDCG@10 **0.0571**, MRR@10 **0.0390** vs Mamba4Rec NDCG@10 **0.0603**, MRR@10 **0.0438**.

### C) LLM-based SR user modeling (capability, distillation, efficiency)

**(3) Kim et al., 2025 — Lost in Sequence (LLM-SRec)**
- Paper: “Lost in Sequence: Do Large Language Models Understand Sequential Recommendation?”
- arXiv: https://arxiv.org/abs/2502.13909
- Quote (abstract): “existing LLM4Rec models do not fully capture sequential information both during training and inference.”
- Quote (text near Table 6): “LLM-SRec consistently outperforms existing LLM4Rec models. This result highlights the importance of distilling the sequential knowledge extracted from CF-SRec into LLMs.”
- Numbers (Table 6, first dataset block in extracted table):
  - SASRec: NDCG@10 **0.3459**, HR@10 **0.5180**
  - LLM-SRec: NDCG@10 **0.3560**, HR@10 **0.5569**

**(4) Li et al., 2024 — E4SRec**
- Paper: “E4SRec: An elegant effective efficient extensible solution of large language models for sequential recommendation” (WWW’24)
- arXiv: https://arxiv.org/abs/2312.02443
- Quote (Table 3 caption): “all the results of E4SRec are statistically significant with p < 0.01 compared to the best baseline models.”
- Numbers (Table 3, Beauty):
  - ICLRec HR@10 **0.0653** vs E4SRec HR@10 **0.0758**
  - ICLRec nDCG@10 **0.0338** vs E4SRec nDCG@10 **0.0435**
  - Table’s Improv column (Beauty): HR@10 **16.08%**, nDCG@10 **28.70%**.

**(5) Xu et al., 2024 — SLMRec**
- Paper: “SLMRec: Empowering Small Language Models for Sequential Recommendation”
- arXiv: https://arxiv.org/abs/2405.17890
- Quote (abstract): “Surprisingly, we discover that most intermediate layers of LLMs are redundant.”
- Quote (abstract): “the proposed SLMRec model attains the best performance using only 13% of the parameters… while… achieving up to 6.6x and 8.0x speedups…”
- Numbers (Table 2 snippet, Cloth dataset; reported as %): SASRec HR@10 **20.26** vs SLMRec (8←32) HR@10 **21.33**.

**(6) He et al., 2025 — LLM2Rec**
- Paper: “LLM2Rec: Large Language Models Are Powerful Embedding Models for Sequential Recommendation”
- arXiv: https://arxiv.org/abs/2506.21579
- Quote (text preceding Table 3): “LLM2Rec consistently outperforms all baseline models…” and “These findings highlight the potential of LLMs as generalizable embedding models for recommendation.”
- Numbers (Table 3, Games in-domain): best baseline R@10 **0.0544** vs LLM2Rec **0.0624**; best baseline N@10 **0.0298** vs LLM2Rec **0.0344**; %Improv row shows **14.76%** (R@10) and **15.46%** (N@10).

### D) Diffusion-based SR

**(7) Huang et al., 2024 — DCRec**
- Paper: “Dual Conditional Diffusion Models for Sequential Recommendation”
- arXiv: https://arxiv.org/abs/2410.21967
- Quote (intro): “existing models represent items with fixed vectors, which limit their ability to capture… the diverse and uncertain nature of user preferences …”
- Numbers (Table 1, Beauty): SdifRec HR@10 **0.0819**, NDCG@10 **0.0507** vs DCRec HR@10 **0.0859**, NDCG@10 **0.0523**; table reports relative improvements including **3.16%** on NDCG@10.

**(8) Chen et al., 2025 — ADRec**
- Paper: “Unlocking the Power of Diffusion Models in Sequential Recommendation: A Simple and Effective Approach”
- arXiv: https://arxiv.org/abs/2505.19544
- Quote (abstract): “we focus on the often-overlooked issue of embedding collapse in existing diffusion-based sequential recommendation models and propose ADRec…”
- Numbers (Table 2):
  - Beauty: SASRec+ HR@20 **15.2038**, NDCG@20 **7.6509**; ADRec HR@20 **16.8246**, NDCG@20 **8.3214** (table shows Improv **10.66% / 8.76%**).
  - Sports: DiffuRec HR@20 **6.2174**, NDCG@20 **3.2067**; ADRec HR@20 **8.1639**, NDCG@20 **3.6389**.

### E) Tokenization and generative decoding for recommendation lists

**(9) Petrov & Macdonald, 2023 — GPTRec**
- Paper: “Generative Sequential Recommendation with GPTRec”
- arXiv: https://arxiv.org/abs/2306.11114
- Quote (abstract): “This paper presents the GPTRec sequential recommendation model, which is based on the GPT-2 architecture.”
- Quote (abstract): “using sub-item tokenisation GPTRec can match the quality of SASRec while reducing the embedding table by 40%.”
- Numbers (Table 3, MovieLens-1M): SASRec NDCG@10 **0.108**; GPTRec-TopK NDCG@10 **0.146**; GPTRec-NextK NDCG@10 **0.105**.

### F) Industrial multi-scenario unified systems (user modeling + RL alignment)

**(10) Zhang et al., 2026 — OneMall**
- Paper: “One Architecture, More Scenarios — End-to-End Generative Recommender Family at Kuaishou E-Commerce”
- arXiv: https://arxiv.org/abs/2601.21770v2
- Quote (abstract): “an end-to-end generative recommendation framework … unifies … Product-card, short-video and live-streaming.”
- Quote (abstract): “Extensive experiments demonstrate … +13.01% GMV … +15.32% Orders … +2.78% Orders …”

## 4) Full list of references (evidence-backed set used above)
- Zhai, J. et al. (2024). *Actions Speak Louder than Words: Trillion-Parameter Sequential Transducers for Generative Recommendations.* arXiv:2402.17152. https://arxiv.org/abs/2402.17152
- Liu, C. et al. (2024). *Mamba4Rec: Towards Efficient Sequential Recommendation with Selective State Space Models.* arXiv:2403.03900. https://arxiv.org/abs/2403.03900
- Petrov, A.V.; Macdonald, C. (2023). *Generative Sequential Recommendation with GPTRec.* arXiv:2306.11114. https://arxiv.org/abs/2306.11114
- Li, X. et al. (2024). *E4SRec: An elegant effective efficient extensible solution of large language models for sequential recommendation.* arXiv:2312.02443. https://arxiv.org/abs/2312.02443
- Xu, W. et al. (2024). *SLMRec: Empowering Small Language Models for Sequential Recommendation.* arXiv:2405.17890. https://arxiv.org/abs/2405.17890
- Huang, H. et al. (2024). *Dual Conditional Diffusion Models for Sequential Recommendation.* arXiv:2410.21967. https://arxiv.org/abs/2410.21967
- Kim, S. et al. (2025). *Lost in Sequence: Do Large Language Models Understand Sequential Recommendation?* arXiv:2502.13909. https://arxiv.org/abs/2502.13909
- Chen, J. et al. (2025). *Unlocking the Power of Diffusion Models in Sequential Recommendation: A Simple and Effective Approach.* arXiv:2505.19544. https://arxiv.org/abs/2505.19544
- He, Y. et al. (2025). *LLM2Rec: Large Language Models Are Powerful Embedding Models for Sequential Recommendation.* arXiv:2506.21579. https://arxiv.org/abs/2506.21579
- Zhang, K. et al. (2026). *One Architecture, More Scenarios — End-to-End Generative Recommender Family at Kuaishou E-Commerce.* arXiv:2601.21770v2. https://arxiv.org/abs/2601.21770v2
