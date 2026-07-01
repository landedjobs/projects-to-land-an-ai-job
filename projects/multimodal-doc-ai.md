# 🖼️ Multimodal document AI — vision + language, end to end

> An app that *sees*: parse charts, tables, receipts, or scanned PDFs with a vision LLM and turn them into answers or structured data — with retrieval over images+text (ColPali-style) where it fits. Multimodal is where a lot of real 2026 document work lives, and few portfolios show a *measured* multimodal pipeline.

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-vision%20LLM%20+%20multimodal%20RAG-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Pick a **visually-rich document domain** — financial reports with charts+tables, invoices/receipts, scientific papers with figures, slide decks, forms — and build an app that answers questions or extracts data *from the visuals*, not just the text layer. The killer feature: it works on documents where **text extraction alone loses the answer** (a number that's only in a chart, a value in a merged table cell, a stamped scan). That's the multimodal edge, and you prove it by comparing to a text-only baseline.

> [!TIP]
> The winning demo is a question whose answer is *only in an image* — a value in a bar chart, a total in a scanned receipt, a cell in a rendered table. Text-only RAG returns nothing; your multimodal pipeline nails it. That contrast is the entire pitch.

## Who it impresses

**AI Engineers, Forward-Deployed Engineers, AI Product Engineers**, and teams in fintech, insurance, research, and enterprise document processing. Multimodal document AI is a fast-growing, under-supplied skill.

## Skills proven

**Vision LLMs** · document parsing (layout, tables, charts) · **multimodal RAG** (retrieve over page-images, ColPali/ColQwen-style) · structured output from images · grounding on visual evidence · eval design for multimodal.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Vision LLM | GPT-4o / Claude / Gemini / a VLM (Qwen-VL) | strong OCR-free chart/table reading |
| Multimodal retrieval | ColPali / ColQwen (page-image embeddings) | retrieve pages *as images* — skips brittle parsing |
| Parsing (baseline) | `unstructured` / `docling` | the text-only baseline you'll beat |
| Structured output | Instructor + Pydantic | typed, validated extraction |
| Store + API | pgvector + FastAPI | serve it |

## Milestones

- **v0 — Text-only baseline.** Parse to text, do normal RAG. Note the questions it *can't* answer (the chart/table ones). That gap is your reason to exist.
- **v1 — Vision QA.** Send the relevant page image to a VLM and answer the visual questions directly. Ground the answer in the page it came from.
- **v2 — Structured extraction from images.** Pull chart/table values into validated JSON (reuse the [extraction brief](structured-extraction-pipeline.md) patterns) with confidence.
- **v3 — Multimodal retrieval (ColPali-style).** Embed *page images* and retrieve by visual similarity — no fragile text parsing. Compare retrieval quality to text-chunk RAG.
- **v4 — Hybrid pipeline.** Route: text-answerable → cheap text path; visual → VLM path. Optimize cost (VLM calls are expensive).
- **v5 — Front-end.** Upload a PDF, ask a question, get an answer *with the page/region highlighted.*

## Evals

- **Text-only vs multimodal accuracy** on a labelled set — split questions into "text-answerable" and "visual-only" and show the multimodal win on the latter:

  | Question type | text-only RAG | multimodal |
  |---------------|---------------|------------|
  | text-answerable | 0.88 | 0.89 |
  | chart/figure | 0.12 | **0.79** |
  | table (complex) | 0.41 | **0.83** |
  | scanned/image PDF | 0.05 | **0.86** |

- **Extraction accuracy** (field-level) for the structured-output path.
- **Retrieval quality** for ColPali-style vs text-chunk retrieval (recall@k).
- **Cost & latency:** VLM calls are pricey — report $/document and the savings from routing.

## Deploy

- **API + worker:** FastAPI on **Modal** (GPU for local VLMs / ColPali) or API-based VLMs on **Railway**.
- **UI:** upload → ask → answer-with-highlight (Next.js/Vercel or Gradio/HF Spaces).
- Lead the README with the "answer only in the chart" GIF and the accuracy-by-question-type table.

## Stretch goals

- **Whole-report agent:** an agent that navigates a long report, deciding when to look at a figure vs read text.
- **Chart → data reconstruction:** rebuild the underlying data table from a chart image and validate.
- **Diagram/architecture understanding** (answer questions about a system diagram).
- **Video frames:** extend to slides/video for meeting-summary use cases.

## 📚 Reference resources

- 💻 [illuin-tech/colpali](https://github.com/illuin-tech/colpali) — MIT, ~2k★ — retrieve documents as page-images; the multimodal-RAG core.
- 💻 [DS4SD/docling](https://github.com/DS4SD/docling) — MIT, ~20k★ — strong document parsing (layout, tables) for the text baseline.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — Apache-2.0, ~116k★ — multimodal app examples.
- 💻 [567-labs/instructor](https://github.com/567-labs/instructor) — MIT, ~9k★ — structured output from vision responses.
- 📄 [ColPali paper](https://arxiv.org/abs/2407.01449) — efficient document retrieval via vision-language embeddings.
- 📘 [Anthropic vision docs](https://docs.anthropic.com/en/docs/build-with-claude/vision) — practical VLM prompting for documents.

> Want more multimodal ideas (Whisper diarization, LLaVA chat, CLIP search, receipt digitizer)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
