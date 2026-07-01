# 🤖 Agentic workflow — a shipping agent that automates a real task

> An agent that plans, calls multiple tools, recovers from failure, and *finishes a real workflow* — with a trajectory eval that proves it. The differentiator isn't "it used an LLM to call a function"; it's **retry budgets, structured tool errors, a real verifier, and a bounded loop.**

[![Difficulty](https://img.shields.io/badge/difficulty-🔴%20advanced-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-tool%20use%20+%20orchestration%20+%20recovery-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Automate one **real, multi-step workflow** end to end — the kind a person actually does by hand. Strong picks: a research agent (plan → search → read → synthesize a cited brief), an on-call triage agent (read alert → query logs → summarize → draft a fix), a "always-on morning briefing" agent, or a customer-refund agent (verify order → check window → issue refund). The job is not "chain some prompts" — it's **an agent that owns a task, uses ≥3 tools, and recovers when a tool fails.**

> [!TIP]
> **Start with a workflow, not an agent.** Most "agent" problems are better solved by a fixed workflow (prompt-chaining, routing, parallelization) — cheaper, predictable, and usually enough. Reach for a true agent (model owns the control flow) only when the steps genuinely can't be pre-planned. Saying *why* you chose one over the other is a senior signal.

## Who it impresses

**AI Engineers, AI Agent Engineers, Forward-Deployed Engineers, AI Product Engineers.** Agent orchestration is one of the hottest 2026 skills, and most portfolios have a *toy* agent — a real one with recovery and a trajectory eval stands out immediately.

## Skills proven

Tool / function-calling design · the agent-computer interface (ACI) · context engineering for the loop · **structured tool errors + retry/backoff** · **bounded loops & retry budgets** · **verifiers** · cost tracking · **trajectory evaluation** · guardrails.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Framework | LangGraph **or** hand-rolled loop | LangGraph = stateful graphs, checkpoints; hand-rolled = you *prove* you understand the loop |
| Multi-agent (opt.) | CrewAI | role-based, fast to demo — but keep single-agent unless you need >1 writer |
| Tools | 3+ real APIs (search, a DB, GitHub, Slack) | breadth of tool use is the signal |
| Model | Claude / GPT-4-class w/ tool use | native tool-calling + a "think" step |
| Tracing | Langfuse / Phoenix | every tool call, token, $, and the full trajectory |
| Eval | custom trajectory harness + LLM-judge | trajectory eval, not just final-answer eval |

## Milestones

- **v0 — One tool, clean turn.** Understand what the API actually exchanges: system prompt → tools → the model emits a tool call → you run it → feed the result back. Log the raw turns.
- **v1 — Design the tools like a public API.** Use **poka-yoke** schemas — make the wrong call *unrepresentable*, not just discouraged:
  ```python
  {"name": "update_ticket",
   "description": "Set a ticket status. Does NOT create or delete tickets.",
   "input_schema": {"type": "object", "properties": {
       "ticket_id": {"type": "string", "pattern": "^TICK-[0-9]{4,}$"},
       "status": {"type": "string", "enum": ["open", "pending", "resolved"]}},
       "required": ["ticket_id", "status"], "additionalProperties": False}}
  # Now "set it to closed" can't even be expressed — the model must pick a valid enum.
  ```
- **v2 — A "think" step.** Insert a cheap reasoning step between observe and act. It's the highest-ROI reliability lift for the least code.
- **v3 — Structured tool errors + recovery.** Return errors the model can *reason about*:
  ```python
  {"ok": False, "value": None,
   "error": {"kind": "TRANSIENT", "message": "upstream 503", "retry_after_ms": 2000}}
  # agent retries TRANSIENT (within budget), gives up on PERMANENT, escalates REQUIRES_HUMAN
  ```
- **v4 — Bounded loop + retry budget + verifier.** Cap iterations and cost. Add a **real verifier** (a test, a schema check, a second model checking the work) — *the verifier is the whole game.* An agent that can't check its own output loops forever or ships garbage.
- **v5 — Cost + trace dashboard.** Wire Langfuse; put a hard $/run cap in place.

## Evals

- **Trajectory eval beats output eval for agents.** Grade *how* it got there, not just the final answer — did it call the right tools in a sensible order, did it recover from the injected failure?
- **pass^k, not pass@k.** Run each task *k* times and require it to succeed *every* time (pass^k) — that measures reliability, which is what production needs. "Best of 5 runs" is eval-gaming.
- **LLM-as-judge, calibrated.** Binary, criterion-specific, justified:
  ```python
  JUDGE_PROMPT = (
    "You are grading whether the agent COMPLETED THE REFUND CORRECTLY.\n"
    "PASS only if ALL hold: (1) it verified the order exists, (2) it checked the "
    "refund window, (3) the refund amount matches the order.\n"
    "If you cannot tell from the transcript, answer UNKNOWN — do not guess.\n"
    "Answer JSON: {verdict: PASS|FAIL|UNKNOWN, reason: }")
  # calibrate vs human labels (Cohen's kappa >= 0.6); never let one model family generate AND judge.
  ```
- **Report:** task-success rate, pass^3, mean tool-calls/task, recovery rate on injected failures, $/run, p95 latency.

## Deploy

- **Backend:** FastAPI on **Modal** (serverless, built-in cron for the "always-on" variant) or **Railway**.
- **Trigger:** a `/run` endpoint, a Slack slash-command, or a cron for a briefing agent.
- Ship a recorded demo (the agent recovering from a failure is a *great* GIF) + `.env.example`. Hard cost cap on the public URL.

## Stretch goals

- **MCP tools** — expose your tools over MCP so the agent works with Claude Desktop / any MCP client (see [mcp-second-brain](mcp-second-brain.md)).
- **Multi-agent** with a supervisor that owns the write and workers that only read — and measure whether it actually beats single-agent (often it doesn't).
- **Reflexion** — let the agent critique and retry its own failed trajectory.
- **Eval-gated CI** so a prompt change can't silently drop task-success.

## 📚 Reference resources

- 💻 [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — MIT, ~36k★ — stateful graph agents, checkpoints, human-in-the-loop.
- 💻 [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — MIT, ~55k★ — role-based multi-agent, fast to demo.
- 💻 [ashishpatel26/500-AI-Agents-Projects](https://github.com/ashishpatel26/500-AI-Agents-Projects) — MIT, ~33k★ — 500+ agent use-cases to steal a *problem* from.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — Apache-2.0, ~116k★ — runnable agents w/ memory, MCP, voice.
- 📄 [Anthropic — Building effective agents](https://www.anthropic.com/research/building-effective-agents) — the workflow-vs-agent taxonomy every interviewer expects you to know.
- 📄 [ReAct: Reasoning + Acting](https://arxiv.org/abs/2210.03629) — the foundational think-act-observe loop.
- 📘 [OpenAI Agents SDK](https://github.com/openai/openai-agents-python) — MIT, ~10k★ — a clean, minimal agent runtime to read.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — MIT, ~30k★ — trace every tool call, token, and dollar.

> Want more agent variants (hierarchical supervisor, plan-and-execute, MemGPT memory)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
