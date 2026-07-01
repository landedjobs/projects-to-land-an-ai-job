# Contributing

Thanks for helping build the best **"projects that land an AI job"** repo on GitHub. This is a *curated, quality-over-quantity* resource — the whole point is that every brief clears a high bar. This file **is** that bar.

Maintained by [Landed](https://landed.jobs). No affiliation with any company or repo named here.

> [!NOTE]
> We keep this repo to **~12 deep, buildable briefs**. If you want a broad menu of 60+ ideas, that's the sibling [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects). Here, we only add a project if it *proves a distinct, in-demand skill* and comes with real evals and a deploy path.

---

## What we accept

- **New project briefs** — a project archetype that hiring managers actually scan for and that isn't already covered. Must ship as a full `projects/<slug>.md` (see the template below).
- **Improvements to existing briefs** — sharper milestones, a better eval metric, a stronger reference repo, a real deployment recipe.
- **Reference resources** — papers, docs, videos, courses, tools, repos — typed, annotated, license-noted. See [`add-a-resource`](.github/ISSUE_TEMPLATE/add-a-resource.md).
- **Presentation-checklist upgrades** — better README templates, eval-report formats, interview talk-tracks. See [`presentation-checklist.md`](presentation-checklist.md).
- **Fixes** — broken links, wrong stack advice, stale 2026 facts, mislabeled licenses/stars.

## The non-negotiables

### 1. A project earns its slot — or it doesn't ship

Before a new brief is accepted, answer these in the PR:

- **What distinct skill does it prove** that no existing brief already proves? (RAG-with-evals, agent orchestration, structured extraction, fine-tuning, from-scratch, serving/LLMOps, multimodal, guardrails…)
- **Who does it impress**, specifically? (Which role/loop reacts to this?)
- **Does it have a real eval layer and a deploy path?** If it can't be measured and can't be shipped to a URL, it's a tutorial, not a portfolio project — and it doesn't belong here.

If a new idea overlaps an existing brief, improve the existing brief instead of adding a near-duplicate.

### 2. Every brief follows the same structure

Each `projects/<slug>.md` must have, in order:

1. **Problem** — the real, narrow problem (not "build a chatbot").
2. **Who it impresses** — the specific role/loop.
3. **Skills proven** — the resume-line skills.
4. **Stack** — concrete, current tools (2026), with a note on why.
5. **Milestones** — build in stages (v0 → v1 → v2), each independently shippable.
6. **Evals** — *how you measure it*. Named metrics + a target. **This is the differentiator.**
7. **Deploy** — a concrete recipe to a live URL.
8. **Stretch goals** — the "senior" extensions.
9. **📚 Reference resources** — typed, annotated, license + stars noted, capped ~8.

A brief with no eval section, or a bare link dump, will be sent back.

### 3. Every link is typed and annotated

Never a bare link dump. Tag the type inline and add a one-line *why*:

> 📄 paper · 📘 docs · 📰 article · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

For **repos and tools**, note the **license** and **approximate stars**. Cap ~8 links per brief — curate, don't accumulate.

### 4. License discipline

This repo ships **MIT** (see [`LICENSE`](LICENSE)).

| Source license | What you may do |
|---|---|
| MIT · Apache-2.0 · CC0 | **Adapt** text/code, with attribution (`License · Source · Author`). |
| CC-BY-4.0 | **Link + commentary only.** Do not copy verbatim. |
| CC-BY-SA / **non-commercial** (e.g. `NirDiamant/RAG_Techniques`) | **Link + commentary only.** Re-implement techniques yourself; never redistribute the notebooks. |
| Unknown / proprietary | Link only, and only if it's publicly readable. |

When in doubt: **link, don't copy.** We attribute the shoulders we stand on — `Shubhamsaboo/awesome-llm-apps`, `rasbt/LLMs-from-scratch`, `patchy631/ai-engineering-hub`, and others are credited in the briefs that draw on them.

### 5. Style

- **Answer-first**, senior voice, no fluff. Dated 2026.
- **Tables over prose** for anything comparable.
- Numbers over adjectives ("recall@20 = 0.91", not "great retrieval").
- Use `> [!NOTE]` / `> [!TIP]` / `> [!WARNING]` callouts for aha-moments and senior traps.

---

## How to contribute

1. Open an issue first for a **new project** ([Suggest a project](.github/ISSUE_TEMPLATE/suggest-a-project.md)) so we can agree it earns a slot.
2. Fork, branch, write the brief against the template, run a quick internal-link self-check.
3. Open a PR using the [PR template](.github/PULL_REQUEST_TEMPLATE.md) and tick the quality checklist.

Small fixes (links, typos, stale facts) can skip straight to a PR.

---

<sub>Maintained by [Landed](https://landed.jobs) · Part of the [Landed AI-native jobs family](https://github.com/landedjobs/awesome-ai-native-jobs).</sub>
