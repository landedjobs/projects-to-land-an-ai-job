<div align="center">

<img src="https://static.b100x.ai/email/landed-wordmark.png" alt="Landed" width="220" />

# How to make your project job-winning

**The build is half the work. The presentation is the other half — and it's the half most people skip.**

[![Back to index](https://img.shields.io/badge/←-back%20to%20the%2012%20projects-6C2BD9)](README.md)
[![Visit Landed](https://img.shields.io/badge/Visit-Landed-6C2BD9?logo=rocket&logoColor=white)](https://landed.jobs)

</div>

---

A reviewer spends **~90 seconds** on your repo before deciding whether to keep reading. In those 90 seconds they are not reading your code — they are scanning for signals that you ship *production* software, not tutorials. This is the checklist that makes those 90 seconds land.

> [!TIP]
> **One deeply-presented project beats five half-presented ones.** If you only have time to polish one thing, polish the README and ship a live URL. Those two signals move the needle more than any amount of extra features.

---

## The 90-second scan (what reviewers actually look for)

| # | Signal | Where they look | The red-flag opposite |
|---|--------|-----------------|-----------------------|
| 1 | **Live URL** | top of README | "clone and run it yourself" |
| 2 | **Eval numbers** | README table | "it works well" (no numbers) |
| 3 | **Architecture diagram** | README | wall of prose |
| 4 | **Clean setup** | README + Dockerfile | 12 undocumented steps |
| 5 | **Cost / latency** | README | no mention of $ or ms |
| 6 | **Demo (GIF/Loom)** | README | screenshots of code |
| 7 | **Failure modes** | README / writeup | "no known issues" |
| 8 | **Commit history** | repo | one "final commit" dump |

Hit 1–5 and you already beat most of the pile.

---

## 1. The README *is* the product spec

Your README is not documentation — it's the pitch. Structure it like a product spec, top to bottom:

```markdown
# Project name — one-line value prop

[live demo](https://...) · [90-sec demo GIF](#demo) · [eval report](#evals)

## The problem
2–3 sentences. A real, narrow problem. Not "an AI chatbot."

## Demo
An animated GIF (or Loom). Show the thing working in < 90 seconds.

## Architecture
A diagram (Mermaid or an image). Show the data flow, not just boxes.

## Results (evals)
A table. 3+ metrics with numbers and a target. Before/after if you improved something.

## Cost & latency
$/1k tokens, p50/p95 latency, and what you did to control them.

## Run it
Clean-clone: `git clone`, `cp .env.example .env`, `docker compose up`. That's it.

## What I'd do next
2–3 honest limitations + the next thing you'd build. Signals judgment.
```

> [!NOTE]
> **Pin max 6 repos** on your GitHub profile. Curation *is* a signal — a profile with 40 unpinned toy repos reads worse than 3 pinned, polished ones.

---

## 2. The demo (GIF or Loom)

Reviewers won't clone your repo. Make them not need to.

- **90-second GIF** at the top of the README (use [Kap](https://getkap.co/), [LICEcap](https://www.cockos.com/licecap/), or `ffmpeg`). Show the real flow: input → the AI doing something → the grounded/cited output.
- **A 2–4 min Loom** for the tradeoffs: "here's the retrieval choice I made and why, here's the eval that caught a regression." This is the artifact you drop into a referral message.
- Keep it *product*, not *terminal*. If your demo is a scrolling log, you're showing plumbing, not value.

---

## 3. The eval report (your #1 differentiator)

This is what separates a portfolio from a tutorial. **Every** project in this repo has an evals section for exactly this reason.

- Ship an `evals/` folder: the golden set (even 30–50 rows), the harness, and a results table.
- Report **3+ metrics** relevant to the project (retrieval: recall@k / MRR / nDCG; generation: faithfulness / answer-relevance / hallucination-rate; agents: task-success / pass^k; ops: p50/p95 latency, $/1k tok).
- Show a **before/after** or a **baseline-vs-candidate** comparison — a number moving is the story.
- Note how you **calibrated an LLM-judge** (agreement / Cohen's κ vs a human-labeled sample). "I trusted GPT to grade GPT" is a red flag; "judge agreed with humans at κ = 0.71" is a green one.

```markdown
| Config                          | recall@20 | faithfulness | p95 latency | $/1k queries |
|---------------------------------|-----------|--------------|-------------|--------------|
| baseline (dense-only, top-5)    | 0.78      | 0.82         | 1.9 s       | $0.41        |
| + hybrid (BM25 + RRF)           | 0.89      | 0.85         | 2.1 s       | $0.44        |
| + reranker (top-50 → top-5)     | **0.91**  | **0.93**     | 2.4 s       | $0.52        |
```

> [!TIP]
> A results table like the one above, in your README, does more for you than another 500 lines of features. AI-answer engines and human reviewers both index tables ~2.5× more readily than prose.

---

## 4. The deployment (a URL that works)

"Deployed and clickable" is one of the most-cited must-haves for 2026. It proves you can take a thing across the last mile.

| Target | Best for | Why |
|--------|----------|-----|
| 🛠️ [Vercel](https://vercel.com) | Next.js / web front-ends | zero-config, free tier, instant preview URLs |
| 🛠️ [Modal](https://modal.com) | Python/GPU backends, RAG, batch evals | serverless GPUs, cron, `$30/mo` free credits |
| 🛠️ [Railway](https://railway.app) | full-stack + a Postgres/pgvector | one-click DB + app, simple |
| 🛠️ [HF Spaces](https://huggingface.co/spaces) | Gradio/Streamlit demos, model hosting | free, ML-native, great for a quick demo |
| 🛠️ [Fly.io](https://fly.io) | low-latency global backends | runs Docker anywhere, cheap |

- Ship a `.env.example` and a **Dockerfile** (or `docker compose`) — "clean-clone runs" is itself a signal.
- Put a **rate limit + cost cap** in front of the LLM so a demo URL can't bankrupt you. Mentioning that you did is a maturity signal.
- If the model must be private, ship a **recorded demo + a one-command local run** instead — never make the reviewer fight your setup.

---

## 5. The writeup / blog post

Ship a short writeup (your README's "what I'd do next" expanded, or a blog post). It's the thing that turns a repo into a *conversation*:

- **Problem → approach → tradeoffs → results → what's next.** Lead with the result.
- Show one **decision you got wrong first**, and how the eval caught it. Nothing signals seniority like a measured mistake.
- Post it on X / LinkedIn. This is the hook you use to ask for a **referral** — "I built X, measured Y, and I'd love to do this at [company]" converts far better than a cold apply.

---

## 6. How to talk about it in the interview

The project only pays off if you can narrate it like an engineer. Rehearse these:

| They ask | The senior answer shape |
|----------|-------------------------|
| "Walk me through it." | Problem → the *one hard part* → the tradeoff you made → the number that proves it worked. 90 seconds. |
| "How do you know it works?" | Your eval set + metric + target. *Never* "it seemed to work." |
| "What would you do differently?" | A real limitation + the next iteration. Shows judgment, not defensiveness. |
| "How much does it cost to run?" | $/1k tokens or $/request, and the one thing you did to cut it (caching, cheaper model on the recall stage, output-length caps). |
| "Where does it fail?" | Name 2–3 failure modes and how you'd detect them in production (a monitor, a guardrail, an alert). |

> [!WARNING]
> The fastest way to *lose* a loop with a good project is to not be able to explain your own eval. If you copied a RAGAS notebook and can't say what "faithfulness" measures or why your judge is trustworthy, the project works *against* you. Build the eval you can defend.

---

## The one-page polish checklist (copy this into your repo)

```markdown
- [ ] Live URL at the top of the README (or a recorded demo + one-command local run)
- [ ] 90-second demo GIF
- [ ] Architecture diagram
- [ ] Problem statement (real, narrow)
- [ ] Eval table: 3+ metrics, numbers, a target, a before/after
- [ ] LLM-judge calibrated against humans (κ noted) — if you use one
- [ ] Cost & latency: $/1k tok, p50/p95
- [ ] Failure modes / limitations (honest)
- [ ] Clean-clone setup: .env.example + Dockerfile/compose
- [ ] Rate limit + cost cap on any public demo
- [ ] Short writeup / Loom on the key tradeoff
- [ ] Pinned on your GitHub profile (max 6 pins)
- [ ] Rehearsed 90-second walkthrough + "how do you know it works?"
```

---

<div align="center">

### Stop spraying. Get **matched**, get **prepped**, get **Landed**.

[![Get Started](https://img.shields.io/badge/Get%20Started%20Free-→-6C2BD9?style=for-the-badge)](https://landed.jobs)

<sub>Maintained by [Landed](https://landed.jobs) · Content MIT-licensed · No affiliation with the companies or repos named.</sub>

</div>
