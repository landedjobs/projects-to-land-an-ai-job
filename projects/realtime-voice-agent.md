# 🎙️ Real-time voice agent — the full-stack, low-latency flex

> A voice agent you can *talk to* — STT → LLM (with tools) → TTS, streaming, sub-second turn-taking. It's the most impressive live demo you can put in a referral message, and it proves you can wrangle **latency, streaming, and interruption** end to end.

[![Difficulty](https://img.shields.io/badge/difficulty-🔴%20advanced-6C2BD9)](#) [![Skill](https://img.shields.io/badge/proves-real--time%20+%20streaming%20+%20latency-00A86B)](#)

- [Problem](#problem) · [Who it impresses](#who-it-impresses) · [Skills proven](#skills-proven) · [Stack](#stack) · [Milestones](#milestones) · [Evals](#evals) · [Deploy](#deploy) · [Stretch goals](#stretch-goals) · [📚 Reference resources](#-reference-resources)

---

## Problem

Build a voice agent for one **narrow, real use case** — a support line, an appointment booker, an interview-practice coach, a drive-thru order-taker. The user speaks; the agent listens, thinks (calling tools when needed), and speaks back, **fast enough to feel like a conversation** (target: first audio out < ~800 ms, natural barge-in/interruption). The hard part isn't the LLM — it's the **latency budget and the streaming pipeline.**

> [!TIP]
> Latency is the whole product. A voice agent that answers correctly in 4 seconds *feels broken*; one that answers "good enough" in 700 ms feels magical. Budget every millisecond — STT partials, streaming LLM tokens, and TTS that starts before the sentence finishes — and *report the budget* in your README.

## Who it impresses

**AI Engineers, AI Product Engineers, Forward-Deployed Engineers**, and any team shipping voice (support, sales, health, edtech). Real-time multimodal is cutting-edge and rare in portfolios — a working demo is a strong differentiator.

## Skills proven

Real-time streaming (WebRTC / WebSockets) · **STT + TTS integration** · **latency engineering** (TTFT, turn-taking, barge-in) · agent tool-use under a latency budget · full-stack (browser mic → backend → audio out) · cost/latency tradeoffs.

## Stack

| Layer | Pick | Why |
|-------|------|-----|
| Orchestration | [Pipecat](https://github.com/pipecat-ai/pipecat) / [LiveKit Agents](https://github.com/livekit/agents) | handle WebRTC, VAD, turn-taking so you focus on the agent |
| STT | Deepgram / Whisper (streaming) | partial transcripts = lower perceived latency |
| LLM | a fast model (Claude Haiku / GPT-4-mini class) + tools | speed matters more than raw IQ here |
| TTS | ElevenLabs / Cartesia / OpenAI TTS | streaming TTS that starts mid-sentence |
| Realtime API (opt.) | OpenAI/Gemini speech-to-speech | one-hop path — compare its latency to your pipeline |
| Transport | WebRTC (LiveKit) | robust real-time audio, echo cancellation |

## Milestones

- **v0 — Half-duplex loop.** Push-to-talk: record → STT → LLM → TTS → play. Ugly but end-to-end.
- **v1 — Stream everything.** STT partials, streaming LLM tokens, streaming TTS. Start speaking before the model finishes. Measure TTFT.
- **v2 — Turn-taking + barge-in.** Add voice-activity detection so the user can interrupt and the agent stops talking. This is what makes it feel human.
- **v3 — Give it a job (tools).** Wire 2–3 tools (look up an order, book a slot) so the agent *does* something, not just chats. Keep tool latency inside the budget.
- **v4 — Latency budget.** Instrument each stage (STT, LLM TTFT, TTS start) and shave it. Compare your pipeline vs a native speech-to-speech Realtime API.
- **v5 — Front-end.** A clean web UI (mic button, live transcript, waveform) so anyone can try it.

## Evals

- **Latency (the headline):** p50/p95 of **time-to-first-audio** and full turn latency, broken out by stage. This table *is* the project.
- **Task success:** on scripted scenarios, did it complete the task (booked the slot, found the order)? Report success rate.
- **Transcription quality:** WER on a sample if STT is a bottleneck.
- **Interruption handling:** does barge-in work reliably? Report a pass rate over test interruptions.
- **Cost:** $/minute of conversation (STT + LLM + TTS).

  | Stage | p50 | p95 |
  |-------|-----|-----|
  | STT (final) | 180 ms | 320 ms |
  | LLM TTFT | 210 ms | 480 ms |
  | TTS first audio | 140 ms | 260 ms |
  | **Time-to-first-audio** | **530 ms** | **~1.0 s** |

## Deploy

- **Backend:** LiveKit Cloud (free tier) or Pipecat on **Modal** / **Fly.io** (low-latency regions matter).
- **Front-end:** Next.js on **Vercel** with the LiveKit/WebRTC client.
- Record a demo call (this is the *best* GIF/video in the whole repo) and pin the latency table at the top.

## Stretch goals

- **Compare architectures:** cascaded (STT→LLM→TTS) vs native speech-to-speech — report the latency/quality/cost tradeoff. That comparison alone is a talk-worthy result.
- **Phone number** via Twilio so people can literally call your agent.
- **Multilingual** turn-taking.
- **Emotion / prosody** control in TTS.

## 📚 Reference resources

- 💻 [pipecat-ai/pipecat](https://github.com/pipecat-ai/pipecat) — BSD-2, ~5k★ — real-time voice/multimodal agent framework (VAD, turn-taking).
- 💻 [livekit/agents](https://github.com/livekit/agents) — Apache-2.0, ~6k★ — WebRTC agents, production-grade transport.
- 💻 [openai/whisper](https://github.com/openai/whisper) — MIT, ~104k★ — the STT baseline; streaming forks exist.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — Apache-2.0, ~116k★ — voice-agent examples to compare.
- 📘 [OpenAI Realtime API docs](https://platform.openai.com/docs/guides/realtime) — the native speech-to-speech path to benchmark against.
- 📰 [Latency in voice AI](https://www.deepgram.com/learn/voice-ai-latency) — where the milliseconds go, and how to claw them back.

> Want more multimodal ideas (transcription+diarization, blog-to-podcast, vision)? See the **60+ catalog** → [`ai-engineer-portfolio-projects`](https://github.com/landedjobs/ai-engineer-portfolio-projects).

---

[← Back to all 12 projects](../README.md) · [Presentation checklist →](../presentation-checklist.md)
