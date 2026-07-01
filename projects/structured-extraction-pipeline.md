# 🧾 Structured extraction pipeline — the pragmatic anti-hallucination flex

> Turn messy documents (invoices, resumes, contracts, receipts) into **validated, schema-locked JSON** — with confidence scores, validation, and repair. It's the most *immediately useful* project here and it proves the pragmatic, production-minded skill of making LLM output *trustworthy*.

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-structured%20output%20+%20validation-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Pick one document type — **invoices, resumes, receipts, contracts, medical forms** — and build a pipeline that extracts it into a strict, validated schema, **every time, with confidence and retry.** The value is reliability: downstream systems get clean typed data, and the pipeline *knows when it's unsure* instead of hallucinating a plausible-looking number. This is the "boring" project that quietly wins because it looks like *real production work*, not a demo.

> [!TIP]
> Schema design is accuracy engineering, not just shape. A field named `total_amount_usd: number` with a description and an enum-constrained `currency` extracts more accurately than a loose `data: object`. Constrained decoding makes the *shape* free; your job is to design a schema that makes the *wrong value* hard to produce.

## Who it impresses

**AI Engineers, Forward-Deployed Engineers, AI Product Engineers**, and any team automating a document workflow (fintech, legal, healthcare, ops). It signals you build things that *plug into a pipeline*, not toys.

## Skills proven

**Structured outputs / constrained decoding** · schema design (Pydantic / Zod) · **function/tool schemas** · **validation, repair, and retry** · confidence scoring · handling the failure modes (partial extraction, wrong types, missing fields) · batch processing.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Schema + extraction | [Instructor](https://github.com/567-labs/instructor) / [Outlines](https://github.com/dottxt-ai/outlines) | Pydantic-native, retry-on-validation built in |
| Constrained decoding | provider structured-output mode / Outlines grammars | guarantees valid JSON, near-free quality |
| Parsing | `pypdf` / `unstructured` / a vision model for scans | garbage parse → garbage extract |
| Vision (for scans) | GPT-4o / Claude / a VLM | receipts/invoices are often images |
| Store | Postgres (typed columns) | proves the output is *usable* downstream |

## Milestones

- **v0 — Naive extract.** Prompt → JSON → `json.loads`. Watch it break on the 10th document. That's your motivation.
- **v1 — Schema-lock it.** Define a Pydantic model; use Instructor/Outlines so output is *guaranteed* to match the schema. Add field descriptions — they're accuracy levers.
- **v2 — Validate + repair + retry.** Business-rule validation (`line_items` sum to `total`, dates are plausible), and a repair loop that feeds validation errors back for one bounded retry.
- **v3 — Confidence + human-in-the-loop.** Attach per-field confidence; route low-confidence extractions to a review queue instead of trusting them. *Knowing when it doesn't know* is the senior move.
- **v4 — Vision for scans.** Handle photographed/scanned documents with a VLM; compare accuracy vs OCR-then-extract.
- **v5 — Batch + throughput.** Process a folder, handle rate limits with backoff, report throughput and cost.

## Evals

- **Field-level accuracy** on a hand-labeled set (50–200 docs): exact-match per field, plus a "close enough" metric for messy fields (dates, amounts).
- **Schema-valid rate:** % of outputs that pass validation on the first try vs after repair.
- **Hallucination rate:** % of fields invented when the source doesn't contain them (this is the metric that matters most).
- **Confidence calibration:** are low-confidence flags actually the wrong ones? (Report precision of the "needs review" flag.)
- **Cost & throughput:** $/document, docs/min.

  | Config | field accuracy | schema-valid (1st try) | hallucination rate |
  |--------|----------------|------------------------|--------------------|
  | naive prompt+parse | 0.71 | 0.63 | 0.14 |
  | + Instructor schema | 0.86 | 0.99 | 0.06 |
  | + validate/repair/confidence | **0.93** | **1.00** | **0.02** |

## Deploy

- **API:** FastAPI `/extract` endpoint on **Modal** / **Railway** with a Postgres for results.
- **UI:** an upload → JSON preview page (Next.js/Vercel or Streamlit/HF Spaces) showing confidence highlights.
- Ship a GIF: drop an invoice → clean validated JSON with a low-confidence field flagged.

## Stretch goals

- **Entity disambiguation** (link extracted companies/people to canonical IDs).
- **Text → knowledge graph:** extract triples and load into Neo4j (bridges to GraphRAG).
- **Active learning:** feed corrected low-confidence rows back to improve prompts/schema.
- **Streaming extraction** for very large documents.

## 📚 Reference resources

- 💻 [567-labs/instructor](https://github.com/567-labs/instructor) — MIT, ~9k★ — Pydantic-based structured outputs with retries; the workhorse.
- 💻 [dottxt-ai/outlines](https://github.com/dottxt-ai/outlines) — Apache-2.0, ~11k★ — grammar-constrained decoding; guaranteed-valid JSON.
- 💻 [Unstructured-IO/unstructured](https://github.com/Unstructured-IO/unstructured) — Apache-2.0, ~11k★ — parse messy PDFs/docs before extraction.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — Apache-2.0, ~116k★ — extraction app examples.
- 📘 [Instructor docs — validation & retries](https://python.useinstructor.com) — the repair-loop patterns.
- 📄 [OpenAI — Structured Outputs guide](https://platform.openai.com/docs/guides/structured-outputs) — constrained decoding, function schemas.

> Want more extraction variants (resume parser, invoice, graph extractor)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
