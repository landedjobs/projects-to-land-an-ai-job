# 🔎 Chat with your docs — production RAG with evals + observability

> **The flagship.** If you build only one project from this repo, build this one. It's the single most-cited "must-have" for AI/RAG engineering loops in 2026 — *because it's the one people fake most often.* You win by doing the parts they skip: hybrid retrieval, a reranker, citations, and a **real eval report**.

[![Difficulty](https://img.shields.io/badge/difficulty-🔴%20advanced-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-RAG%20+%20evals%20+%20observability-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Pick a **real corpus you actually care about** and make an LLM answer questions about it *with citations you can click* — and *prove* the answers are grounded. Good corpora: a company's public docs, a regulation/handbook, a book series, your own Notion/Obsidian vault, a set of research papers. The bar is not "it returns something plausible." The bar is: **when the answer isn't in the docs, it says so**, and **every claim links to the source chunk.**

> [!WARNING]
> The tutorial-clone trap kills this project. `LangChain quickstart → Streamlit → "chat with PDF"` with no eval and no reranker is what *every* applicant submits. It reads as "GPT wrapper, no original work." The eval layer and the retrieval pipeline are what make it a portfolio piece.

## Who it impresses

**AI Engineers, RAG Engineers, Forward-Deployed Engineers, LLM Engineers.** Every company with an AI feature that has to be *right* — support bots, internal search, legal/finance copilots — is hiring for exactly this. Enterprise RAG is the FDE bread-and-butter.

## Skills proven

Chunking strategy · embeddings & vector DBs · **hybrid (dense + BM25) retrieval** · **reranking** · citation/grounding · **retrieval evals (recall@k, MRR, nDCG)** · **generation evals (faithfulness, answer-relevance, hallucination rate)** · observability (tracing, cost, latency) · deployment.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Orchestration | LlamaIndex **or** plain Python | plain Python reads as "I understand the pipeline," not "I glued a framework" |
| Embeddings | `text-embedding-3-large` / `voyage-3` / `bge-large` | benchmark 2–3 on *your* corpus, don't guess |
| Vector store | pgvector / Qdrant / Chroma | pgvector = one Postgres for app + vectors; cheap deploy |
| Lexical | BM25 (`rank_bm25` / Postgres FTS / Qdrant sparse) | catches exact codes, names, IDs dense misses |
| Reranker | Cohere Rerank / `bge-reranker-v2-m3` | buys precision@5 on a wide shortlist |
| LLM | Claude / GPT-4-class + a cheap model for synth data | multi-provider so a key never blocks a reviewer |
| Evals | RAGAS + a small custom harness | named metrics reviewers recognize |
| Observability | Langfuse / Phoenix | traces every prompt, token, ms, $ |

## Milestones

Build in stages — each stage is independently demoable.

- **v0 — Naive baseline (day 1).** Load → fixed-size chunks → embed → dense top-k → stuff into prompt → answer. *Ship this, then never trust it again.* It's your baseline number.
- **v1 — Make it honest.** Add **citations** (return chunk IDs, render clickable sources) and a **"not in the docs" refusal**. Force the model to ground every claim.
- **v2 — Hybrid recall.** Add BM25, fuse with **Reciprocal Rank Fusion**. Wide, cheap recall stage:
  ```python
  def hybrid_recall(query, k_each=50):
      bm25_ids  = bm25_search(query, k=k_each)     # exact tokens, codes, names
      dense_ids = vector_search(query, k=k_each)   # paraphrase, intent
      return rrf([bm25_ids, dense_ids], k=60)[:k_each]  # rank-based fuse
  ```
- **v3 — Precision with a reranker.** Recall wide (top-50), rerank to top-5. Two stages, two jobs: recall maximizes *the answer is present*; the reranker maximizes *the right 5 are at the top*.
- **v4 — Chunking sweep.** Stop guessing chunk size — **sweep it against your eval set** (below) and pick on the recall@20 / precision@5 tradeoff curve. Add [Anthropic-style contextual retrieval](https://www.anthropic.com/news/contextual-retrieval) if precision@5 is still < 0.7.
- **v5 — Observability + cost.** Wire Langfuse: trace every query's retrieved chunks, tokens, latency, cost. Add a cache and a cost cap.

## Evals

**This is the section that gets you hired.** No eval → it's a tutorial.

1. **Build a labelled set from your own chunks**, then hand-verify ~20%:
   ```python
   def synth_eval_set(chunks, llm, n_per_chunk=1):
       rows = []
       for c in chunks:
           q = llm("Write one realistic question this passage answers. "
                   "Question only:\n" + c["text"])
           rows.append({"query": q, "gold_chunk_ids": [c["chunk_id"]]})
       return rows  # then human-verify ~20%, prune leaky/duplicate Qs
   ```
2. **Retrieval metrics** — the two every RAG eval starts with:
   ```python
   def recall_at_k(eval_set, retrieve, k=20):
       hits = sum(bool({r["chunk_id"] for r in retrieve(q, k=k)} & set(gold))
                  for q, gold in eval_set)
       return hits / len(eval_set)     # did ANY gold chunk make top-k?
   # precision@5, MRR, nDCG similarly. Maximize recall@50-100, buy precision@5 with the reranker.
   ```
3. **Generation metrics** (RAGAS or a calibrated LLM-judge): **faithfulness** (is every claim supported by retrieved context?), **answer-relevance**, **context-precision**, **hallucination rate**.
4. **Report a table** — baseline vs each upgrade — in your README:

   | Config | recall@20 | faithfulness | p95 latency | $/1k queries |
   |--------|-----------|--------------|-------------|--------------|
   | v0 dense-only | 0.78 | 0.82 | 1.9 s | $0.41 |
   | + hybrid (RRF) | 0.89 | 0.85 | 2.1 s | $0.44 |
   | + reranker | **0.91** | **0.93** | 2.4 s | $0.52 |

> [!TIP]
> Calibrate your LLM-judge against a human-labeled sample (Cohen's κ ≥ 0.6) and *say so* in the README. "I trusted GPT to grade GPT" is a red flag; "judge agreed with humans at κ = 0.71" is a green one.

## Deploy

- **Backend:** FastAPI + pgvector on **Railway** (app + Postgres in one project) or **Modal** (serverless, cron for re-indexing).
- **Front-end:** a small Next.js chat UI on **Vercel**, or Streamlit/Gradio on **HF Spaces** for a fast demo.
- Ship `.env.example` + a `Dockerfile`; put a **rate limit + cost cap** on the public URL. Add the live link and a 90-sec GIF to the README top.

## Stretch goals

- **GraphRAG** over an entity-rich corpus (Microsoft GraphRAG) and compare answer quality to vanilla RAG on multi-hop questions.
- **Corrective / self-RAG**: a hallucination checker that re-retrieves when faithfulness is low.
- **Eval-gated CI**: a GitHub Action that fails the PR if recall@20 or faithfulness regresses (see the [production eval suite](production-eval-suite.md) brief).
- **Multi-modal**: retrieve over PDFs with tables/images via ColPali (see [multimodal-doc-ai](multimodal-doc-ai.md)).

## 📚 Reference resources

- 💻 [NirDiamant/RAG_Techniques](https://github.com/NirDiamant/RAG_Techniques) — **non-commercial license**, ~28k★ — the single best catalog of RAG techniques (contextual chunk headers, HyDE, corrective RAG). **Re-implement the ideas yourself; do not redistribute the notebooks.**
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — Apache-2.0, ~116k★ — 100+ runnable RAG/agent apps to compare architectures against.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — MIT, ~36k★ — in-depth RAG/eval/observability tutorials.
- 💻 [explodinggradients/ragas](https://github.com/explodinggradients/ragas) — Apache-2.0, ~14k★ — faithfulness / context-precision / answer-relevance metrics.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — MIT, ~30k★ — tracing, cost, latency for every query.
- 📄 [Anthropic — Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval) — the fix for orphaned chunks; ~35% retrieval-error reduction.
- 📄 [Microsoft — GraphRAG](https://arxiv.org/abs/2404.16130) — graph-structured retrieval for multi-hop questions.
- 📘 [pgvector docs](https://github.com/pgvector/pgvector) — MIT, ~13k★ — vectors in Postgres, the cheap-deploy path.

> Want more RAG variants (HyDE, GraphRAG, agentic RAG, multimodal RAG)? See the **60+ catalog** in the sibling repo → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
