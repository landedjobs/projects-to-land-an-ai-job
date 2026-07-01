# 🛡️ Guardrails & moderation — the safety layer that says "production"

> A guardrails layer around an LLM app: input/output moderation, **prompt-injection defense, PII detection, jailbreak resistance** — with a **red-team eval suite** that measures how well it holds. Safety is a first-class 2026 hiring signal because it's what stands between a demo and something you can put in front of users.

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-safety%20+%20guardrails%20+%20red--teaming-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Take an LLM app (yours or a stub) and wrap it in a **guardrails layer** that a real product needs: block/redact PII, resist prompt injection and jailbreaks, keep outputs on-policy (no toxic/off-topic/unsafe content), and enforce structure. Then **red-team your own defenses** and report how they hold. The deliverable is the safety layer *and the adversarial eval that stress-tests it* — the combination is what reads as "I ship to real users."

> [!WARNING]
> **Prompt injection is the #1 unsolved LLM security problem — treat it that way.** The classic vector: content the model reads (a retrieved doc, a tool description, a user upload) contains instructions that override the system prompt. There's no perfect fix; the senior move is *defense-in-depth* (least-privilege tools, input/output filters, human-in-the-loop for high-stakes actions, sandboxing) and *honesty about residual risk.* Don't claim you "solved" injection.

## Who it impresses

**AI Engineers, AI Safety / Trust & Safety Engineers, Forward-Deployed Engineers**, and any regulated-industry team (fintech, health, legal). Safety maturity is disproportionately rare in portfolios and disproportionately valued.

## Skills proven

Input/output **moderation** · **prompt-injection & jailbreak defense** · **PII detection/redaction** · policy enforcement · **red-teaming / adversarial eval** · defense-in-depth architecture · latency/cost of a guardrail layer.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Guardrails framework | [Guardrails-AI](https://github.com/guardrails-ai/guardrails) / [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) | validators + rails, or roll your own to prove understanding |
| Moderation | provider moderation API + a classifier | layered defense |
| PII | Microsoft Presidio | detect/redact PII pre- and post-LLM |
| Injection detection | a small classifier / prompt-based detector | catch override attempts |
| Red-team | [garak](https://github.com/NVIDIA/garak) / [PyRIT](https://github.com/Azure/PyRIT) | automated adversarial probing |

## Milestones

- **v0 — The unguarded app.** A simple LLM endpoint with *no* protection. Attack it yourself; document what gets through. That's your baseline.
- **v1 — Input guardrails.** PII detection/redaction on inputs (Presidio), a moderation check, and a prompt-injection detector on any untrusted content.
- **v2 — Output guardrails.** Moderate/validate outputs (no PII leakage, on-policy, valid structure), with a bounded repair loop for violations.
- **v3 — Defense-in-depth architecture.** Least-privilege tools, a policy layer, human-in-the-loop for high-stakes actions, and sandboxing. Write the threat model down.
- **v4 — Red-team suite.** Build/borrow an adversarial set: jailbreaks, injection payloads, PII-extraction attempts, toxic prompts. Automate it (garak/PyRIT).
- **v5 — Measure + iterate.** Report attack-success-rate before vs after each guardrail; close the gaps the red-team finds.

## Evals

The red-team eval *is* the project:

| Attack category | unguarded ASR* | with guardrails ASR | Δ |
|-----------------|----------------|---------------------|---|
| Direct jailbreak | 0.72 | 0.11 | −0.61 |
| Prompt injection (via doc) | 0.65 | 0.19 | −0.46 |
| PII extraction | 0.40 | 0.02 | −0.38 |
| Toxic-output elicitation | 0.55 | 0.06 | −0.49 |

<sub>*ASR = attack success rate (lower is better).</sub>

- **Attack success rate** per category, before/after guardrails.
- **False-positive rate:** how often do guardrails block *legitimate* inputs? (Over-blocking is its own failure — report the tradeoff.)
- **Latency & cost** the guardrail layer adds per request.
- **Residual risk:** name the attacks that *still* get through and how you'd monitor for them in prod.

## Deploy

- **API:** a guarded `/chat` proxy (FastAPI) on **Modal** / **Railway** that fronts any LLM.
- **UI:** a demo page where a reviewer can *try to break it* — an interactive red-team box is a memorable demo.
- Put the before/after ASR table + false-positive rate at the README top, plus your honest "residual risk" section.

## Stretch goals

- **Injection benchmark:** evaluate against a public prompt-injection benchmark and report your score.
- **Adaptive attacks:** an LLM red-teamer that evolves attacks against your defenses (measure how fast it finds a gap).
- **Structured refusal + safe-completion** instead of hard blocks (better UX).
- **Content-provenance / watermarking** for generated outputs.

## 📚 Reference resources

- 💻 [guardrails-ai/guardrails](https://github.com/guardrails-ai/guardrails) — Apache-2.0, ~5k★ — input/output validators and rails.
- 💻 [NVIDIA/NeMo-Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) — Apache-2.0, ~5k★ — programmable rails (topical, safety, jailbreak).
- 💻 [microsoft/presidio](https://github.com/microsoft/presidio) — MIT, ~4k★ — PII detection & redaction.
- 💻 [NVIDIA/garak](https://github.com/NVIDIA/garak) — Apache-2.0, ~5k★ — LLM vulnerability scanner / red-team probes.
- 💻 [Azure/PyRIT](https://github.com/Azure/PyRIT) — MIT, ~3k★ — Python risk-identification toolkit for automated red-teaming.
- 📄 [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — the canonical threat taxonomy to structure your suite around.
- 📰 [Anthropic — prompt injection & agent security](https://www.anthropic.com/news/agentic-misalignment) — framing on why injection stays hard.

> Want more eval/safety variants (adversarial suite, hallucination audit)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
