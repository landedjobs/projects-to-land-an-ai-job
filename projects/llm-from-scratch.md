# 🧬 LLM from scratch — prove you understand the machine

> Build a GPT — tokenizer, attention, training loop — **from scratch**, then reproduce a small model (nanoGPT-style). No framework magic. It's the project that lets you answer *any* transformers-internals question with "I implemented that," which is exactly what LLM-engineering and research-adjacent loops probe for.

[![Difficulty](https://img.shields.io/badge/difficulty-🔴%20advanced-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-transformers%20internals-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Implement a transformer language model from first principles in PyTorch: a **BPE tokenizer**, **multi-head self-attention**, positional encodings, the training loop, sampling — and **train it to produce coherent text** on a small corpus (TinyShakespeare, then a bigger set). Then reproduce a small GPT-2 (124M-class) so you have a *trained* artifact, not just a notebook. The point isn't a useful product — it's that you can explain, and have *built*, every layer of the machine everyone else only calls via API.

> [!NOTE]
> This project is a *knowledge* signal, not a *product* signal. It won't get you a shipping-focused AI-Engineer role on its own — pair it with the [RAG](chat-with-your-docs-rag.md) or [agent](agentic-workflow.md) project. But for **LLM Engineer / Applied Scientist / Research Engineer** loops, being able to say "I implemented attention and pretrained a GPT" is a genuine edge, and it makes the interview's internals questions trivial.

## Who it impresses

**LLM Engineers, Applied Scientists, Research Engineers, ML infra.** Frontier labs and model teams value candidates who understand the machine end-to-end. It's the antidote to "I only know the API."

## Skills proven

Tokenization (**BPE**) · **self-attention / multi-head attention** from scratch · transformer architecture (residuals, layernorm, MLP blocks) · autoregressive training loop · sampling (temperature, top-k/top-p) · **pretraining mechanics** · optimization (AdamW, LR schedule, gradient accumulation) · basic distributed/mixed-precision (stretch).

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Framework | PyTorch (no HF `Trainer`) | you write the loop — that's the point |
| Reference | [nanoGPT](https://github.com/karpathy/nanoGPT) / [minbpe](https://github.com/karpathy/minbpe) / Raschka's book | best-in-class from-scratch references (read, then reimplement) |
| Compute | one GPU (Colab / Modal / rented A100) | 124M reproduction fits a single modern GPU |
| Data | TinyShakespeare → OpenWebText subset | small → real |

## Milestones

- **v0 — BPE tokenizer from scratch.** Implement byte-pair encoding (à la minbpe). Train it on your corpus; verify encode/decode round-trips. Now you *own* the tokenizer, the "unit of cost and latency."
- **v1 — Attention from scratch.** Implement scaled dot-product + multi-head attention by hand. Unit-test it against a reference. This is the layer every interview asks about.
- **v2 — A working transformer block.** Stack attention + MLP + residuals + layernorm into a GPT block; assemble the full model.
- **v3 — Training loop.** AdamW, LR warmup+decay, gradient accumulation, checkpointing. Train on TinyShakespeare until it produces coherent text.
- **v4 — Reproduce a small GPT-2.** Scale to 124M-class on a real corpus; track loss to a target. Now you have a *trained* model.
- **v5 — Sampling + a tiny demo.** Temperature / top-k / top-p sampling and a text-generation demo.

## Evals

- **Loss curve → target.** Train/val loss over steps; hit a documented target (e.g. GPT-2 124M val loss on OpenWebText subset). The curve *is* the proof.
- **Tokenizer correctness:** round-trip fidelity + compression ratio vs a reference BPE.
- **Attention unit tests:** your implementation matches a reference (`torch.nn.functional.scaled_dot_product_attention`) within tolerance.
- **Generation quality:** samples at a few temperatures; a small held-out perplexity number.
- **(Stretch) tokens/sec** throughput and how mixed-precision / a fused kernel changed it.

## Deploy

- This one deploys as **understanding**, not a URL. Ship:
  - a clean, *readable* repo (the code is the artifact — comment the hard parts),
  - a **writeup / notebook** walking attention → training → samples,
  - loss curves and generated samples in the README,
  - optionally a tiny HF Space that samples from your trained model.
- A short blog post ("I built a GPT from scratch — here's what clicked about attention") is a great referral hook for research-adjacent roles.

## Stretch goals

- **llm.c-style depth:** reimplement a piece in raw C/CUDA to show systems-level understanding.
- **Full finetune + instruction-tune** your pretrained model (bridges to the [fine-tuning brief](fine-tuned-specialist-model.md)).
- **Rotary embeddings / GQA / FlashAttention** — add a modern architectural piece and measure the effect.
- **Distributed training** (DDP) across 2+ GPUs.

## 📚 Reference resources

- 💻 [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) — book repo (check LICENSE; **read + reimplement, attribute Sebastian Raschka**), ~98k★ — the definitive step-by-step "build a ChatGPT-like LLM in PyTorch."
- 💻 [karpathy/nanoGPT](https://github.com/karpathy/nanoGPT) — MIT, ~60k★ — the cleanest GPT-2 reproduction to learn from.
- 💻 [karpathy/minbpe](https://github.com/karpathy/minbpe) — MIT, ~11k★ — minimal BPE tokenizer, perfect for v0.
- 💻 [karpathy/llm.c](https://github.com/karpathy/llm.c) — MIT, ~30k★ — GPT-2 in raw C/CUDA for the systems stretch goal.
- 🎬 [Karpathy — Let's build GPT](https://www.youtube.com/watch?v=kCc8FmEb1nY) — the canonical from-scratch walkthrough.
- 📄 [Attention Is All You Need](https://arxiv.org/abs/1706.03762) — the transformer paper; implement what it describes.

> Want more from-scratch variants (tiny Llama, sentence-level GPT, CUDA depth)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
