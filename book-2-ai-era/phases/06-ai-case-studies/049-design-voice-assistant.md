# 049 · Design a Real-Time Voice Assistant

> ⏱ 15 min · 📈 98% · 🅱️ Production & case studies · Phase 06: AI Case Studies & Capstone
>
> `███████████████████░` 98% of Book 2
>
> 🧬 **Atoms used:** TCP vs UDP [B1·010] · real-time communication [B1·016] · latency budgets [B1·003] · [004] · [014] · [017] · [029] · [036] · [043]

---

## 📖 Story

The final card: **"'Talk to Pantry': customers phone in or tap a mic and order by voice. Natural conversation. Under a second to reply. They can interrupt."**

The voice team's prototype is honest about its problems. It records the customer until they **stop talking**, sends the audio to speech recognition, sends the text to the model, waits for the **whole** answer, sends it to speech synthesis, and plays it. Every step waits for the one before.

Customers hear **3.8 seconds of silence** after every sentence. They say "hello?" into the gap, which the system hears as a **new question**. When the chef starts reading a long list of specials, customers can't stop it: they talk over it and get **ignored**. And one caller ordered "**two** pizzas" and heard back "**ten** pizzas confirmed".

"In text chat," Maya tells the panel, "a slow answer feels slow. In voice, **silence feels broken**. Humans expect a reply within about **half a second**. So the whole design is one thing: a **latency budget**, spent across a pipeline where **everything streams**."

## 🎯 One-sentence idea

**A real-time voice assistant is a streaming pipeline (audio transport, voice-activity and turn detection, streaming speech recognition, a fast LLM, and streaming speech synthesis) with every stage overlapping the next to fit a ~800 ms voice-to-voice budget, plus barge-in handling, spoken confirmation of actions, and graceful fallbacks.**

## 🧸 Analogy

A **great waiter taking a phone order**:

- They **listen while you speak**, already understanding as you go (streaming recognition).
- They know when you've **finished a sentence** versus just pausing to think (turn detection).
- They start **answering immediately**, one sentence at a time, instead of composing a speech in silence (streaming generation and synthesis).
- If you **cut in**, they **stop talking at once** and listen (barge-in).
- And before charging your card, they **read the order back** (spoken confirmation).

## 🖼️ Visual

*Diagram brief:* a horizontal timeline of one conversational turn with overlapping bars: the caller's speech, streaming ASR partial transcripts under it, an end-of-turn marker, LLM generation starting at the marker, TTS starting on the first sentence, and audio playback starting about 700 ms after the caller stopped. A barge-in arrow shows playback cancelled when the caller speaks again.

```
caller:  "Can I get two margheritas and a…  tiramisu?"│(stops)
ASR:      partials stream ────────────────────────────▶│final +100 ms
turn:                                                  │end-of-turn detected (+200 ms)
LLM:                                                   └▶ TTFT 250 ms → "Two margheritas and a tiramisu…"
TTS:                                                         └▶ first audio after 1st sentence +120 ms
playback:                                                       ▶🔊 starts ≈ 700 ms after caller stops
barge-in: caller speaks → playback cut < 100 ms → back to listening
```

## 🔬 How it works

- **Requirements and napkin:** peak **40,000 concurrent calls** (app mic + phone lines). **Voice-to-voice < 800 ms p50, < 1.5 s p95.** Barge-in < 200 ms. Orders confirmed aloud. Each call streams audio both ways (~32 kbps), and holds ASR, LLM, and TTS capacity for minutes, so sizing is by **concurrent sessions**, not requests (Little's Law).
- **Transport:** **WebRTC** (UDP, low latency, jitter buffers, echo cancellation) for apps, and a **SIP/telephony gateway** for phone calls, terminating at **regional media servers** close to callers (Book 1 lessons 010, 016). Every 100 ms of network round trip comes straight out of the budget.
- **Listening:** **voice-activity detection** + **turn detection** (silence plus a small model that judges whether the sentence is complete) decide when to respond: too eager and you interrupt people, too patient and silence grows. **Streaming ASR** emits partial transcripts while the caller speaks, so the final transcript is ready ~100 ms after they stop.
- **Thinking and speaking:** a **fast model** (small or speculative-decoded, lesson 017) with prefix-cached prompts, streaming tokens into **sentence chunks** for **streaming TTS**, which begins speaking after the first sentence. Tool calls (menu search, cart) run **in parallel** with a spoken filler ("Let me check that…"). Speech-to-speech models can collapse ASR → LLM → TTS into one model with lower latency, at the cost of less control and inspectability.
- **Barge-in and state:** when VAD detects the caller speaking during playback, **stop audio immediately**, cancel generation (lesson 014), and record what was actually heard, so the conversation state matches reality.
- **Actions and safety:** numbers and items are **normalized and validated** (lesson 035), and every order is **read back** for a spoken "yes" before placing it (lesson 036). Low ASR confidence → ask again. Failures fall back to a **simpler flow** (menu-by-voice prompts) or a **human agent** (lesson 043).

## 🧩 Worked example

**The voice-to-voice budget (p50):**

| Stage | Before (sequential) | After (streaming) |
|---|---|---|
| End-of-speech detection | 1,000 ms (fixed silence wait) | 200 ms (VAD + turn model) |
| ASR final transcript | 600 ms (batch) | 100 ms (streaming) |
| LLM | 1,500 ms (full answer) | 250 ms (TTFT, small model, prefix cache) |
| TTS | 500 ms (whole answer) | 120 ms (first sentence) |
| Network (both ways) | 200 ms | 80 ms (regional media servers) |
| **Voice-to-voice** | **3.8 s** | **750 ms** ✅ |

**Capacity napkin at 40k concurrent calls:**

```
Average call 3 min, the caller speaks ~40% of the time
ASR: 40k streams; one GPU handles ~200–400 streams → ~100–200 GPUs
LLM: ~1 turn per 10 s per call → 4k turns/s, ~150 output tokens each → small-model pool
TTS: ~40% of calls speaking at once → 16k streams; ~100 per GPU → ~160 GPUs
```

**"Two pizzas" → "ten pizzas", replayed:** ASR heard "to pizzas", the model guessed "ten". Now quantities pass through a normalizer with confidence scores: low confidence → "**Sorry, how many margheritas?**", and the read-back requires a spoken "yes". Quantity errors on placed orders: **1.2% → 0.05%**.

## ⚖️ Trade-offs

| Decision | Choice | Trade-off |
|---|---|---|
| Pipeline vs speech-to-speech | Cascaded ASR → LLM → TTS | Control, inspectability, and tool use vs a few hundred ms more latency |
| Turn detection | Silence + semantic turn model | Fewer interruptions vs slight added delay |
| Model size | Small/fast for turns, large for hard cases | Latency vs quality |
| Media placement | Regional media servers | Low latency vs more infrastructure |
| Confirmation | Spoken read-back for orders | Accuracy vs a slightly longer call |

## 🌍 Real world

- Voice agent platforms build on **WebRTC** media servers, streaming ASR, and streaming TTS, and publish **voice-to-voice latency** targets under ~1 second.
- Real-time **speech-to-speech** model APIs reduce latency by skipping text intermediates, while many production systems keep a cascaded pipeline for control and tool use.
- Contact centres integrate voice AI through **SIP** telephony, with human handoff as the standard fallback.

## 📌 Cheat card

> - **Silence feels broken:** budget ~800 ms voice-to-voice.
> - **Everything streams and overlaps:** ASR partials, LLM tokens, sentence-chunked TTS.
> - **VAD + turn detection.** Barge-in cuts playback < 200 ms and cancels generation.
> - **WebRTC/SIP to regional media servers.** Size by concurrent sessions.
> - **Normalize, validate, and read back every order.** Fallback to simpler flows or humans.

## 🧪 Feynman check

Explain the great phone waiter: why they understand while you're still talking, how they know you've finished, and why they stop instantly when you interrupt.

⚠️ **Common confusion:** "Voice is just chat with speech-to-text in front and text-to-speech behind." Run sequentially, those stages add up to **seconds** of silence. Voice is a **real-time streaming system** with its own budget, turn-taking, interruption, and confirmation problems.

## ⚡ Quick recall

1. Roughly what voice-to-voice latency should a voice assistant target?
<details><summary>Reveal Answer</summary>

Under about 800 ms at p50 (and well under ~1.5 s at p95), because longer silences feel broken in conversation.
</details>

2. What is barge-in, and what must the system do?
<details><summary>Reveal Answer</summary>

The caller speaking while the assistant talks. The system must stop playback immediately, cancel generation, and update state to what was actually heard.
</details>

3. Why stream TTS by sentence?
<details><summary>Reveal Answer</summary>

So speech starts after the first sentence is generated, instead of waiting for the full answer, cutting perceived latency.
</details>

## 🎤 Interview practice

**Q. "Design a voice ordering assistant for a pizza chain: 10,000 concurrent calls at Friday peak, under one second to respond."**
<details><summary>Model answer</summary>

- **Transport:** SIP trunks → regional telephony/media gateways. App users via WebRTC to the same regional media servers.
- **Pipeline per call:** VAD + turn detection → streaming ASR (partials) → a small, fast LLM with prefix-cached prompts and menu tools → sentence-chunked streaming TTS. Budget: 200 + 100 + 250 + 120 + 80 ≈ 750 ms.
- **State:** call session state (cart, step) in a fast store keyed by call ID. The order itself lives in the order system (lesson 031).
- **Actions:** menu items and quantities normalized and validated against the catalogue. Spoken read-back + "yes" before placing. Payment via a secure DTMF/IVR or a link, never spoken card numbers through the LLM.
- **Capacity:** 10k concurrent ASR and TTS streams → GPU pools sized from per-GPU stream benchmarks. LLM turns ≈ 1k/s on a small-model pool. Warm pools before Friday peaks (lesson 016).
- **Resilience:** fallbacks to a simpler menu flow or a human agent. Timeouts on every stage. Recordings and transcripts traced with PII redaction (lesson 039).
- **Likely follow-up:** "Background noise makes ASR unreliable." → noise suppression, confidence-based re-asks, constrained vocabularies for menu items, and confirmation of key fields.
</details>

## 📖 Teaser

> 📖 *Five cards, five designs, and the panel has one last request, which isn't a card at all: Maya hands the marker to the person reading this.*

---

⬅️ [048 · Design Embedding-Based Recommendations](048-design-recommendations.md) · 🗺️ [Phase map](README.md) · ➡️ [050 · Capstone: Design It & Teach It Back](050-capstone.md)

✅ **Safe stopping point.** Tick lesson 049 in [PROGRESS.md](../../PROGRESS.md).
