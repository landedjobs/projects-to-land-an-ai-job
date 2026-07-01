# 🔦 Semantic + hybrid search engine — retrieval as a product

> A real search engine over a large corpus: **dense + sparse (BM25) hybrid retrieval, fusion, reranking**, and a quality comparison that proves each stage earns its place. This is RAG's retrieval half, isolated and done *right* — the purest proof of the single most in-demand RAG skill.

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-hybrid%20retrieval%20+%20reranking-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Build search over a corpus big enough to be interesting (100k+ docs: a code repo, arXiv abstracts, product catalog, Wikipedia subset, your company's docs). Ship a search box that returns *the right results at the top* — and **prove** it beats keyword-only and dense-only baselines with a labelled eval. No generation needed: this is retrieval as a standalone product, which is exactly what RAG Engineers get graded on.

> [!TIP]
> Retrieval is where most RAG systems actually fail — not generation. A project that isolates retrieval and *measures* the recall→precision pipeline (hybrid recall → reranker) demonstrates the skill that separates RAG Engineers from "I called an embedding API." Dense catches paraphrase and intent; BM25 catches exact codes, names, and IDs. You need both.

## Who it impresses

**RAG Engineers, AI Engineers, Search/ML Engineers, Data Engineers.** Any team with an internal-search or "find the right doc" problem — which is nearly all of them.

## Skills proven

Embeddings & **ANN indexes** · **BM25 / lexical retrieval** · **hybrid fusion (RRF)** · **reranking** (recall→precision pipeline) · **retrieval evals (recall@k, MRR, nDCG, precision@k)** · latency/cost at scale · query understanding.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Vector index | Qdrant / pgvector / FAISS | ANN, not brute force — the index is the engine |
| Lexical | BM25 (`rank_bm25`, Qdrant sparse, Postgres FTS, Elasticsearch) | exact-match power |
| Embeddings | `bge-large` / `voyage-3` / `text-embedding-3` | benchmark 2–3 on *your* corpus |
| Reranker | `bge-reranker-v2-m3` / Cohere Rerank | buys precision@5 on a wide shortlist |
| API/UI | FastAPI + a minimal search UI | make it clickable |

## Milestones

- **v0 — Dense baseline.** Embed corpus → ANN index → top-k. Measure recall@20. This is what you'll beat.
- **v1 — BM25 baseline.** Add lexical search; measure it *separately.* You'll see it wins on exact-match queries and loses on paraphrases — that's the motivation for hybrid.
- **v2 — Hybrid fusion.** Fuse dense + BM25 with **Reciprocal Rank Fusion** (rank-based, no score calibration needed):
  ```python
  def hybrid_recall(query, k_each=50):
      bm25_ids  = bm25_search(query, k=k_each)
      dense_ids = vector_search(query, k=k_each)
      return rrf([bm25_ids, dense_ids], k=60)[:k_each]
  ```
- **v3 — Reranker.** Recall wide (top-50) → rerank to top-5. Two stages: recall maximizes *the answer is present*; the reranker maximizes *the right ones are at the top.*
- **v4 — Tune it.** Sweep BM25's `k1`/`b`, RRF weighting, and embedding model — *against the eval*, not on vibes.
- **v5 — Latency at scale.** Report p50/p95 at your corpus size; add a cache; note the recall/latency tradeoff of shortlist width.

## Evals

**The comparison table across baselines is the whole point:**

| Config | recall@20 | MRR | nDCG@10 | p95 latency |
|--------|-----------|-----|---------|-------------|
| dense-only | 0.79 | 0.61 | 0.58 | 45 ms |
| BM25-only | 0.74 | 0.66 | 0.62 | 30 ms |
| hybrid (RRF) | 0.90 | 0.71 | 0.68 | 60 ms |
| **hybrid + reranker** | **0.90** | **0.83** | **0.81** | 140 ms |

- **Labelled set:** synth-from-corpus + hand-verify ~20% (same recipe as the [RAG brief](chat-with-your-docs-rag.md)), or a public IR set (BEIR/MS MARCO subset).
- Report **recall@k, MRR, nDCG, precision@5** — and *which query types* each stage fixes.
- Note the **latency cost** of the reranker so the tradeoff is explicit.

## Deploy

- **API:** FastAPI `/search` on **Railway** (pgvector) or **Modal** (Qdrant/FAISS).
- **UI:** a clean search page (Next.js/Vercel) with result highlighting.
- Put the baseline-comparison table + a GIF of a tricky query getting the right top-3 at the README top.

## Stretch goals

- **Query rewriting** (HyDE / multi-query) and measure the recall lift.
- **Faceted / metadata filters** (search *within* a date range, author, type).
- **ColBERT / late-interaction** retrieval and compare to bi-encoder + reranker.
- **Learned fusion weights** instead of vanilla RRF.

## 📚 Reference resources

- 💻 [qdrant/qdrant](https://github.com/qdrant/qdrant) — Apache-2.0, ~21k★ — vector DB with native sparse (hybrid) support.
- 💻 [FlagOpen/FlagEmbedding](https://github.com/FlagOpen/FlagEmbedding) — MIT, ~8k★ — `bge` embeddings + rerankers, strong open baselines.
- 💻 [NirDiamant/RAG_Techniques](https://github.com/NirDiamant/RAG_Techniques) — **non-commercial**, ~28k★ — fusion/rerank/query-rewriting techniques (re-implement, don't redistribute).
- 💻 [beir-cellar/beir](https://github.com/beir-cellar/beir) — Apache-2.0, ~2k★ — standard IR benchmarks for a rigorous eval.
- 📄 [Reciprocal Rank Fusion](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf) — the simple, robust fusion method you'll use.
- 📘 [pgvector docs](https://github.com/pgvector/pgvector) — MIT, ~13k★ — vectors + FTS in one Postgres.
- 📄 [ColBERT](https://arxiv.org/abs/2004.12832) — late-interaction retrieval for the stretch goal.

> Want more retrieval variants (GraphRAG, multimodal, agentic RAG)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
