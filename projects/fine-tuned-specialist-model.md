# 🎛️ Fine-tuned specialist model — beat the base model on a narrow task

> Fine-tune a small open model (LoRA/QLoRA) to beat a much bigger base model on **one narrow task** — and *prove it with a head-to-head eval.* This is the project that separates you from the prompt-only crowd and says "I understand training, not just APIs."

[![Difficulty](https://img.shields.io/badge/difficulty-🔴%20advanced-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-fine--tuning%20+%20data%20curation-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Pick a **narrow, well-defined task** where a small specialized model can win: a specific classification/routing task, a domain style (legal clause rewriting, SQL for your schema, structured extraction for one form type), or a reasoning-distillation target. Fine-tune a 1–8B open model with **LoRA/QLoRA** and show it **beats the base model (and ideally a frontier API) on your eval** — cheaper and/or faster. The story is: *dataset curation → PEFT → measured win.*

> [!WARNING]
> **Fine-tuning is the last resort, and interviewers know it.** If prompting + RAG + structured outputs solve the task, fine-tuning is the wrong tool — and saying that shows judgment. Pick a task where fine-tuning genuinely wins: tight latency/cost budgets, a consistent output style, on-prem/privacy needs, or distilling a big model's reasoning into a small one. Frame *why* fine-tuning is justified.

## Who it impresses

**LLM Engineers, ML Engineers, Applied Scientists, Research Engineers.** Very few portfolios show a *measured* fine-tune vs base — it's the clearest "I can train, not just call APIs" signal for LLM-engineering loops.

## Skills proven

**Dataset curation** (the real work) · **PEFT / LoRA / QLoRA** · quantization & VRAM management · training loop / hyperparameters · **eval vs base model** · preference tuning (DPO, stretch) · reasoning distillation (stretch) · serving the adapter.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Trainer | [Unsloth](https://github.com/unslothai/unsloth) / [axolotl](https://github.com/axolotl-ai-cloud/axolotl) / TRL | Unsloth = 2× faster, fits consumer GPU, avoids OOM |
| Base model | Llama 3.x 1–8B / Qwen / Gemma | small enough to train, good enough to win narrow |
| PEFT | LoRA / QLoRA (4-bit) | trains on a single consumer/Colab GPU |
| Data | your curated set + synthetic augmentation | *curation is 80% of the outcome* |
| Compute | Colab / Modal / a rented A100 | Modal for reproducible, cheap bursts |
| Serving | vLLM / Ollama (GGUF) | serve the merged/adapter model |

## Milestones

- **v0 — Baseline the base.** Run the base model *and* a frontier API on your eval. Record the numbers you're trying to beat. No baseline → no story.
- **v1 — Curate the dataset.** This is the project. Build 500–5000 high-quality examples (hand-written + synthetic + filtered). Dedupe, balance, hold out a clean test split. Document *how* you built it.
- **v2 — LoRA/QLoRA fine-tune.** Train with Unsloth/TRL on a consumer GPU. Watch for overfitting; log loss curves.
- **v3 — Eval vs base + API.** Head-to-head on the held-out set. Report task accuracy, cost/1k, and latency for all three.
- **v4 — Iterate on data, not hyperparameters.** Most gains come from better data (fix the failure modes the eval surfaces), not more epochs. Show a data-driven improvement.
- **v5 — Serve it.** Merge/quantize and serve behind an API so it's usable — not just a checkpoint on disk.

## Evals

**The head-to-head table is the entire pitch.** Fine-tune vs base vs frontier API, on *your* held-out set:

| Model | task accuracy | $/1k requests | p95 latency | notes |
|-------|---------------|---------------|-------------|-------|
| base Llama-3.2-3B | 0.61 | $0.00* | 240 ms | *self-hosted |
| GPT-4-class API | 0.88 | $9.20 | 900 ms | strong but pricey |
| **LoRA fine-tune (3B)** | **0.91** | **$0.00*** | **240 ms** | beats API, ~40× cheaper |

- **Beat the base** on task accuracy — that's the minimum bar.
- **Beat the API on cost/latency** at comparable quality — that's the *win*.
- **Guard against eval leakage:** the test set must be truly held out from training. Say so.
- **Report the failure modes** the fine-tune still has (honesty = seniority).

## Deploy

- **Serve** with vLLM (OpenAI-compatible API) on **Modal** with a GPU, or GGUF via **Ollama** locally.
- Publish the **adapter to Hugging Face Hub** (with a model card: data, training config, eval numbers) — that model card *is* a portfolio artifact.
- Ship a demo Space (HF Spaces) hitting your model.

## Stretch goals

- **DPO / preference tuning** on top of SFT and measure the delta.
- **Reasoning distillation:** distill a big model's chain-of-thought into your small one (DeepSeek-R1-style) and eval reasoning tasks.
- **Fine-tune a sentence-transformer** for *retrieval* on your corpus and plug it into the [RAG brief](chat-with-your-docs-rag.md).
- **Quantization sweep:** 4-bit vs 8-bit vs FP16 quality/latency tradeoff.

## 📚 Reference resources

- 💻 [unslothai/unsloth](https://github.com/unslothai/unsloth) — Apache-2.0 core (AGPL Studio), ~68k★ — 2× faster LoRA/QLoRA, fits consumer GPUs, avoids OOM.
- 💻 [axolotl-ai-cloud/axolotl](https://github.com/axolotl-ai-cloud/axolotl) — Apache-2.0, ~9k★ — config-driven fine-tuning for many models/methods.
- 💻 [huggingface/trl](https://github.com/huggingface/trl) — Apache-2.0, ~11k★ — SFT, DPO, PPO trainers.
- 💻 [huggingface/peft](https://github.com/huggingface/peft) — Apache-2.0, ~17k★ — LoRA and friends, the standard PEFT library.
- 💻 [vllm-project/vllm](https://github.com/vllm-project/vllm) — Apache-2.0, ~85k★ — serve the result at throughput.
- 📄 [QLoRA paper](https://arxiv.org/abs/2305.14314) — 4-bit fine-tuning on a single GPU; the method behind the project.
- 📄 [DeepSeek-R1 (reasoning distillation)](https://arxiv.org/abs/2501.12948) — for the reasoning-distillation stretch goal.
- 🧑‍🏫 [HF — LLM fine-tuning course](https://huggingface.co/learn/nlp-course) — the end-to-end walkthrough.

> Want more fine-tuning variants (DPO, instruction-tune, embedding fine-tune)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
