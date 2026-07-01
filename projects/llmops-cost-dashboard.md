# 💸 LLMOps cost & latency dashboard — the production-mindset signal

> An observability layer that traces every LLM call — **tokens, latency (p50/p95), cost, cache hits** — and surfaces it in a dashboard with alerts. It's the project that proves you think about the LLM app *after* it ships: the part that separates "I built a demo" from "I run this in production."

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-observability%20+%20LLMOps-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Instrument a real LLM app (your [RAG](chat-with-your-docs-rag.md), [agent](agentic-workflow.md), or any public one) with **full observability**: trace every call end to end, attribute **cost per request / per feature / per user**, track **latency (TTFT, p50/p95)**, measure **cache hit-rate**, and alert on regressions (cost spike, latency drift, error-rate jump). Then *act on it* — use the dashboard to cut cost or latency and show the before/after. The deliverable is the control plane, and the story is a number you moved.

> [!TIP]
> Cost and latency are back-of-envelope interview staples — being able to say "this feature costs $0.004/request, p95 is 2.1s, and caching cut cost 38%" from a dashboard *you built* is a strong senior signal. A support assistant at 200k req/day can run ~$1,100/day; output tokens dominate cost (~4× input). Knowing this cold, with a dashboard to back it, sets you apart.

## Who it impresses

**AI Engineers, LLMOps / ML Platform Engineers, AI Product Engineers**, and any team past the prototype stage worrying about their model bill. Production-mindset is one of the most-cited hiring signals for 2026.

## Skills proven

**Tracing / observability** (OpenTelemetry) · **cost attribution** (tokens → $) · **latency engineering** (TTFT, p50/p95) · **caching** (semantic + exact) · alerting / drift detection · dashboards · the cost/latency tradeoffs of model and prompt choices.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Tracing | [Langfuse](https://github.com/langfuse/langfuse) / [Phoenix](https://github.com/Arize-ai/phoenix) | LLM-native traces, cost, latency out of the box |
| Instrumentation | OpenTelemetry / OpenLLMetry | vendor-neutral, standard |
| Caching | Redis (exact) + semantic cache | cache is the #1 cost/latency lever |
| Dashboard | Langfuse UI / Grafana / a Streamlit | show it clickable |
| Alerting | a cron check → Slack/email | cost-spike & drift alerts |

## Milestones

- **v0 — Trace one call.** Wrap your LLM client so every call logs model, tokens (in/out), latency, and cost. See it in Langfuse/Phoenix.
- **v1 — Attribute cost.** Roll up $ per request → per feature → per user/session. Compute it from token counts × per-model price. This is the number everyone wants.
- **v2 — Latency breakdown.** Track TTFT and total, split by stage (retrieval vs generation vs tools). Report p50/p95, not just the mean — tails are what page you.
- **v3 — Caching.** Add exact + semantic caching; measure hit-rate and the cost/latency it saves. *This is your "number you moved."*
- **v4 — Alerts + drift.** Cron a check that fires on cost spikes, latency regressions, error-rate jumps, or output-distribution drift.
- **v5 — Dashboard.** A clean page: cost over time, p50/p95, cache hit-rate, top-cost features, live alerts.

## Evals

Here "evals" means proving the observability is *accurate and useful:*

- **Cost accuracy:** your computed $/request matches the provider bill within a small % (validate it).
- **Before/after you optimized:** the headline table —

  | Change | $/1k req | p95 latency | cache hit-rate |
  |--------|----------|-------------|----------------|
  | baseline | $6.40 | 2.9 s | 0% |
  | + exact cache | $4.80 | 2.4 s | 21% |
  | + semantic cache | $3.90 | 2.1 s | 34% |
  | + cheaper recall model | **$2.30** | **1.8 s** | 34% |

- **Alert precision:** do the alerts fire on *real* regressions and not noise? (Report false-alarm rate.)
- **Overhead:** how much latency/cost does the tracing layer itself add? (Keep it small; report it.)

## Deploy

- **Backend + collector:** the instrumented app on **Modal** / **Railway**; Langfuse self-hosted or cloud.
- **Dashboard:** a public Langfuse/Phoenix project, or a Streamlit page on **HF Spaces**.
- Lead the README with the before/after cost-latency table and a dashboard screenshot.

## Stretch goals

- **Cost-aware routing:** route easy queries to a cheap model, hard ones to a strong one — and measure the blended cost/quality.
- **Budget guardrail:** a hard per-user/per-day spend cap with graceful degradation.
- **Eval + cost together:** join this with the [eval suite](production-eval-suite.md) so a PR shows *both* quality and cost deltas.
- **Multi-provider price comparison** for the same workload.

## 📚 Reference resources

- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — MIT, ~30k★ — traces, cost, latency, dashboards; the core.
- 💻 [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) — Apache-2.0, ~10k★ — OpenTelemetry-native tracing + drift.
- 💻 [traceloop/openllmetry](https://github.com/traceloop/openllmetry) — Apache-2.0, ~6k★ — OpenTelemetry instrumentation for LLM apps.
- 💻 [BerriAI/litellm](https://github.com/BerriAI/litellm) — MIT, ~20k★ — a proxy that centralizes cost/latency logging + budgets across providers.
- 📘 [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) — the standard schema for LLM traces.
- 📰 [Langfuse — LLM cost tracking](https://langfuse.com/docs/model-usage-and-cost) — how token→$ attribution is done in practice.

> Want more LLMOps variants (drift monitor, A/B eval, CI gate)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
