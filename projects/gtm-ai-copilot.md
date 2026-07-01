# 🚀 GTM / AI-PM copilot — the differentiated, business-facing build

> A context-aware assistant that automates a real go-to-market or product workflow — lead enrichment + scoring + personalized outbound, or a support-ticket triager, or a 0→1 AI micro-app with real users. It's under-represented in portfolios, which makes it a *differentiator* for AI Product Engineer and GTM Engineer loops.

[![Difficulty](https://img.shields.io/badge/difficulty-🟡%20intermediate-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-applied%20AI%20+%20business%20value-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Automate a workflow a **revenue or product team** does by hand. Strong picks:
- **Lead enrichment + scoring + outbound:** take a company/person → enrich from public signals → score fit → draft a *personalized* (not templated) outreach with the reasoning.
- **Support-ticket triage:** classify → route → draft a reply → escalate the hard ones, with a confidence gate.
- **0→1 AI micro-app:** ship a tiny end-to-end tool (auth, UI, LLM, deploy) that solves one narrow problem — **and get 10 real users.**

The bar is **measured business value**, not a chatbot: a personalization-quality score, a triage-accuracy number, or real usage. This project reads as "I build things a company can actually deploy."

> [!TIP]
> The rare, high-signal move here is **real users**. A deployed micro-app with 10 people using it — and a note on what you learned from their behavior — beats a dozen unused demos. For AI Product Engineer loops especially, "I shipped it and watched people use it" is the story.

## Who it impresses

**AI Product Engineers, GTM Engineers, Forward-Deployed Engineers, founding engineers** at product-led startups. It shows product sense + shipping ability + the judgment to tie AI to a business outcome.

## Skills proven

Applied LLM product-building · **enrichment + API/webhook orchestration** · personalization with grounding · **classification/routing with confidence gates** · rapid full-stack prototyping (React/Next.js) · **tying AI output to a business metric** · lightweight evals for a fuzzy task.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| App | Next.js + Vercel AI SDK | fastest path to a deployed, real UI |
| LLM | Claude / GPT-4-class w/ tools + structured output | grounded, typed generation |
| Enrichment | public APIs / search / scraping (respect ToS) | real signals → real personalization |
| Data | Postgres (Supabase/Neon) | store leads/tickets/usage |
| Automation (opt.) | Clay-style pipeline / webhooks | the GTM-Engineer idiom |
| Analytics | PostHog / simple event logging | prove the "real users" story |

## Milestones

- **v0 — The narrow slice.** One input → one useful output (a scored lead + a drafted message; a classified ticket + a reply). Ground it — cite the signal that drove the personalization.
- **v1 — Structured + confidence.** Typed output (score, category, reasoning) with a **confidence gate** that routes low-confidence cases to a human. Judgment > automation-for-its-own-sake.
- **v2 — Orchestrate the workflow.** Chain enrichment → scoring → drafting (or classify → route → reply). Handle rate limits and failures gracefully.
- **v3 — A real UI.** A clean front-end anyone can use: paste a lead / a ticket, see the output, edit, send.
- **v4 — Ship + get users.** Deploy publicly; get ~10 real users (or run it on a real backlog of leads/tickets). Instrument usage.
- **v5 — Measure the value.** Report the metric that matters: reply-rate on outbound, triage accuracy, time saved, or usage/retention.

## Evals

Fuzzy tasks still get measured — that's the differentiator:

- **Personalization quality** (outbound): an LLM-judge (calibrated) rating relevance/specificity vs a templated baseline; ideally a real reply-rate.
- **Triage accuracy** (support): precision/recall on category + routing, on a labelled set; false-escalation rate.
- **Confidence-gate precision:** are the "needs human" flags actually the wrong ones?
- **Usage (micro-app):** activation, 10 real users, a retention/usage number and what you learned.

  | Metric | templated baseline | this copilot |
  |--------|--------------------|--------------|
  | personalization score (1–5, judge) | 2.4 | **4.1** |
  | triage accuracy | 0.74 | **0.91** |
  | human-review load | 100% | **18%** (rest auto-handled) |

## Deploy

- **Full app** on **Vercel** (front-end) + **Railway/Supabase** (DB + API). The whole point is a clickable, usable product.
- Ship a demo GIF *and* a live URL; if it processes real leads/tickets, show a (redacted) run.
- Lead the README with the business metric and the "real users" note.

## Stretch goals

- **Persistent memory** (a SalesGPT-style assistant that remembers the account across touches).
- **Multi-channel** (email + SMS + Slack) outbound or triage.
- **A/B the AI vs a human/templated baseline** and report lift with significance.
- **Marketing-asset generator** (brief → landing page) as a second surface.

## 📚 Reference resources

- 💻 [filip-michalsky/SalesGPT](https://github.com/filip-michalsky/SalesGPT) — MIT, ~2.7k★ — context-aware sales agent with memory; steal the pattern.
- 💻 [vercel/ai](https://github.com/vercel/ai) — Apache-2.0, ~11k★ — the AI SDK for a fast, deployable Next.js app.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — Apache-2.0, ~116k★ — GTM/lead-scoring/support-agent examples.
- 💻 [ashishpatel26/500-AI-Agents-Projects](https://github.com/ashishpatel26/500-AI-Agents-Projects) — MIT, ~33k★ — business-workflow agent ideas.
- 📘 [Vercel AI SDK docs](https://sdk.vercel.ai/docs) — streaming, tools, structured output for the app.
- 🧑‍🏫 [Landed — Become a GTM Engineer](https://github.com/landedjobs/become-a-gtm-engineer) — the GTM-engineering skill map this project targets.

> Building for a specific role? See the sibling roadmaps: [`ai-product-engineer-roadmap`](https://github.com/landedjobs/ai-product-engineer-roadmap) · [`become-a-gtm-engineer`](https://github.com/landedjobs/become-a-gtm-engineer). More project ideas → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
