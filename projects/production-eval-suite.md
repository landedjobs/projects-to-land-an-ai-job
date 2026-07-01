# 📊 Production eval suite — the project that says "senior"

> "Eval is the new system design." This is the single project that most reliably makes an interviewer sit up, because **almost nobody builds it** — and the ones who do are the ones seniors want to hire. A golden set, a calibrated LLM-judge, an eval-gated CI, and a regression story.

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-eval%20rigor%20+%20LLMOps-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Take *any* LLM feature — ideally your [RAG app](chat-with-your-docs-rag.md) or [agent](agentic-workflow.md), or a public one — and build the **eval harness that a real team would gate deploys on.** Not a one-off notebook that prints a score: a golden set derived from error analysis, a judge you *calibrated against humans*, a results dashboard, and a **CI gate that fails a PR on regression.** The deliverable is the safety net, and the story is a regression it caught.

> [!NOTE]
> This is the highest signal-to-effort project in the whole repo. It needs no GPU, no fancy front-end, and it directly answers the question every good interviewer asks: *"How do you know it works?"* Most candidates can't. You will.

## Who it impresses

**Everyone senior.** AI Engineers, LLM Engineers, ML Platform / LLMOps, Data Scientists doing GenAI eval, AI PMs who own launch quality. A candidate who evals is a candidate who's shipped to production.

## Skills proven

Error-analysis-driven **golden set** construction · **LLM-as-judge design + calibration** · faithfulness / relevance / hallucination metrics · **pass^k reliability** · **A/B prompt eval with statistical significance** · **eval-gated CI** · drift monitoring · cost/latency tracking.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Metrics | RAGAS (RAG) + custom criteria | recognized names + your own rubric |
| Judge | Claude / GPT-4-class, *different family from the generator* | avoids self-preference bias |
| Harness | Phoenix / Braintrust / plain pytest | pytest = it lives in CI naturally |
| Tracing | Langfuse / Phoenix (OpenTelemetry) | traces + datasets in one place |
| CI | GitHub Actions | the eval gate lives here |
| Stats | `scipy.stats` | significance on A/B prompt tests |

## Milestones

- **v0 — Golden set from error analysis, not vibes.** Collect real (or realistic) inputs, run the feature, **read the failures**, and label a set of 50–200 cases with the *specific* failure mode each exposes. The set comes from where the system breaks, not from what's easy to write.
- **v1 — Metrics that fit the feature.** RAG → faithfulness, context-precision, answer-relevance. Agent → task-success, pass^k. Extraction → field-level accuracy. Pick 3+ and define each precisely.
- **v2 — A judge you can trust.** Binary, criterion-specific, justified, and **calibrated**:
  ```python
  # calibrate against human labels before trusting it:
  kappa = cohen_kappa(judge_labels, human_labels)   # want >= 0.6-0.8
  # if low: tighten the criteria, re-label. Never let the same model family generate AND judge.
  ```
- **v3 — A/B prompt eval with significance.** Compare prompt/model variants and report whether the difference is *real*, not noise (bootstrap CI or a paired test). This kills "it felt better" forever.
- **v4 — Eval-gated CI.** A GitHub Action runs the suite on every PR and **fails the build if a metric regresses** past a threshold. This is the artifact that reads as production maturity.
- **v5 — Dashboard + drift.** A results page (Phoenix/Langfuse or a small Streamlit) showing metrics over time, cost, latency, and drift alerts.

## Evals

The eval suite *is* the project — so here, "evals" means **how you prove the suite itself is trustworthy:**

- **Judge calibration:** Cohen's κ vs a human-labeled sample (report the number).
- **Judge robustness:** position-swap and length-bias checks (does the judge favor the first/longer answer regardless of quality?).
- **Set quality:** de-dupe, remove leaky questions, cover each failure mode.
- **The money table** — a regression the gate caught:

  | Change | task-success | faithfulness | Δ | gate |
  |--------|--------------|--------------|---|------|
  | main | 0.91 | 0.93 | — | ✅ |
  | "cheaper model" PR | 0.86 | 0.88 | −0.05 | ❌ blocked |
  | "new prompt" PR | 0.94 | 0.95 | +0.03 | ✅ merged |

> [!WARNING]
> **Eval gaming is a red flag.** Best-of-5 runs with no seed control, a judge from the same model family as the generator, or a golden set written to be easy — reviewers spot these. The whole value of the project is that your numbers are *honest*. Say how you kept them honest.

## Deploy

- **CI:** the GitHub Action *is* the deploy — a passing/failing check on PRs, visible in the repo.
- **Dashboard:** a small Streamlit/Gradio results page on **HF Spaces**, or a public Langfuse/Phoenix project.
- Put the **CI badge** and the regression table at the top of the README. That badge alone signals more than most whole projects.

## Stretch goals

- **Adversarial / red-team eval suite** — jailbreaks, prompt-injection, PII leakage (pairs with [guardrails-moderation](guardrails-moderation.md)).
- **Drift monitor** over time with alerting (Phoenix).
- **Trajectory eval** for agents, not just output eval.
- Package it as a reusable pip module others can drop into *their* project — a genuine open-source contribution.

## 📚 Reference resources

- 💻 [explodinggradients/ragas](https://github.com/explodinggradients/ragas) — Apache-2.0, ~14k★ — faithfulness/precision/relevance metrics for RAG.
- 💻 [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) — Apache-2.0, ~10k★ — OpenTelemetry-native eval, datasets, drift.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — MIT, ~30k★ — traces, datasets, and CI eval hooks.
- 🧑‍🏫 [Hamel Husain — Your AI product needs evals](https://hamel.dev/blog/posts/evals/) — the canonical "error analysis → golden set → judge" playbook.
- 📄 [Judging LLM-as-a-Judge (MT-Bench)](https://arxiv.org/abs/2306.05685) — biases of LLM judges and how to control for them.
- 📘 [Anthropic — building evals](https://docs.anthropic.com/en/docs/test-and-evaluate/develop-tests) — practical eval-design guidance.
- 💻 [openai/evals](https://github.com/openai/evals) — MIT, ~16k★ — a large registry of eval patterns to learn from.

> Want the broader LLMOps menu (CI gates, drift monitors, cost dashboards)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
