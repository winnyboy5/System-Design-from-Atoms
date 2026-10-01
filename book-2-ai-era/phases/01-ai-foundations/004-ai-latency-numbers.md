# 004 · AI Latency Numbers: TTFT, TPOT & Tokens/s

> ⏱ 11 min · 📈 8% · 🅰️ AI-era core · Phase 01: AI Foundations for System Designers
>
> `█░░░░░░░░░░░░░░░░░░░` 8% of Book 2
>
> 🧬 **Atoms used:** latency numbers & percentiles [B1·003] · latency vs throughput [B1·004] · queueing [B1·004] · [002]

---

## 📖 Story

Maya's latency dashboard looks healthy. **Average response time: 3.1 seconds**, down from 8.4.

But the support inbox is on fire. "The chef ignores me." "Frozen again." And the logs show **18%** of messages are **duplicates**: customers tapping "send" again and again.

She splits the one number into two. **Time to first token** at p50 is 0.4 s. At **p99, it's 7.2 seconds**. For one customer in a hundred, the bubble sits **empty** for seven seconds, during the dinner rush, when requests queue behind each other on busy GPUs.

Meanwhile, the words, once they start, stream at **45 tokens a second**: faster than anyone can read.

I told Maya her average was hiding two different stories. In AI systems, "latency" isn't one number. It's at least **three**, and users feel each one differently. Let me give you the numbers that matter.

## 🎯 One-sentence idea

**LLM latency is measured in three parts (time to first token, time per output token, and end-to-end time) at percentiles, because users feel the wait before the first word far more than the total time, and the tail is dominated by queueing.**

## 🧸 Analogy

A **waiter taking your order**:

- **TTFT** is how long until the waiter **says the first word** after you sit down. Ten seconds of silence feels like being ignored, even if dinner is quick.
- **TPOT** is how fast they **talk** once they start. Faster than you can listen? Good enough.
- **End-to-end** is how long until they **finish** the whole speech. It matters for things that need the full answer (an agent's next step, a JSON object).

## 🖼️ Visual

*Diagram brief:* a single timeline for one request. A grey "queue wait" bar, then a blue "prefill" bar ending at a marker labelled TTFT, then a long striped "decode" bar made of equal tick marks labelled TPOT, ending at a marker labelled end-to-end.

```
t=0        queue        prefill  ▼ TTFT                                 ▼ E2E
│░░░░░░░░░░░░░░░░░░░░│█████████│|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|‧|
  waiting for a GPU    reading     one token every TPOT ms (decode)
  (the tail lives      the prompt
   here at peak)
```

## 🔬 How it works

- **TTFT (time to first token)** = queue wait + prefill (+ network). It sets the **feeling of responsiveness**. Target **< 1 s** for chat. Its tail is dominated by **queueing** and **long prompts**.
- **TPOT (time per output token)**, also called inter-token latency, sets streaming speed. People read **~4–6 tokens/s**, so **20–50 ms/token (20–50 tokens/s)** feels instant. Faster matters for **machines** (agents, JSON) waiting on the whole answer.
- **End-to-end = TTFT + output tokens × TPOT.** It matters when the result feeds another step: tool calls, multi-step agents, voice (lesson 049).
- **Measure percentiles, per phase.** The average hides the dinner-rush tail. Track **p50/p95/p99 TTFT and TPOT** separately, plus **goodput**: the share of requests that meet **both** SLOs.
- **Throughput vs latency, again:** a GPU's **total tokens/s** rises with batch size, while each user's TPOT gets a little worse. Pick the batch size from the TPOT SLO, not from maximum throughput (lesson 010).

## 🧩 Worked example

**AI latency numbers worth memorizing (order of magnitude, 2026):**

| Operation | Typical latency |
|---|---|
| Tokenize a prompt | < 1 ms |
| Embed a query (lesson 019) | 10–50 ms |
| Vector search, top 50 (lesson 020) | 5–20 ms |
| Rerank 50 candidates (lesson 023) | 50–150 ms |
| TTFT, small model, short prompt | 100–300 ms |
| TTFT, large model, 10k-token prompt | 0.5–2 s |
| TPOT | 10–50 ms (20–100 tokens/s) |
| A tool call (API, DB) | 50 ms – seconds |
| Reasoning "thinking" tokens | seconds to minutes |

**Maya's dinner-rush tail, explained with Little's Law** (Book 1, lesson 004):

```
Peak: 7,000 requests/s, each occupying a GPU slot for ~8 s
In flight = 7,000 × 8 = 56,000 concurrent sequences
Fleet capacity at the TPOT SLO = 48,000 slots
→ 8,000 requests wait in the queue → TTFT p99 = 7.2 s
```

**The fix, in three moves:**
1. **Cap answers at 300 tokens:** 8 s → 6 s per slot, so in-flight drops to 7,000 × 6 = **42,000**.
2. **Defer non-interactive traffic** (nightly recipe tagging) out of the dinner rush.
3. **Grow the fleet to ~55,000 slots**, ~30% headroom over 42,000.

**TTFT p99: 7.2 s → 0.9 s.** Duplicate taps: **18% → 2%**.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Optimize TTFT | Feels responsive | More capacity headroom, idle GPUs off-peak |
| Optimize TPOT | Fast full answers for agents | Smaller batches, higher cost per token |
| Optimize throughput | Lowest cost per token | Worse TTFT and TPOT under load |
| Stream tokens | Perceived latency ≈ TTFT | Harder to validate the whole answer first |

## 🌍 Real world

- Inference benchmarks (e.g., **MLPerf Inference**) report TTFT and TPOT constraints, not just throughput.
- Providers publish **output tokens per second** and **TTFT** per model, and they vary by **10×** across model sizes.
- Serving research measures **goodput**: throughput that **meets latency SLOs**, because raw throughput can be gamed by letting users wait.

## 📌 Cheat card

> - **TTFT** = queue + prefill. Feels like responsiveness. **< 1 s** for chat.
> - **TPOT** = decode speed. **20–50 ms/token** beats reading speed.
> - **E2E = TTFT + N × TPOT.** It matters for machines.
> - **Percentiles per phase. Goodput, not raw throughput.**
> - **The TTFT tail = queueing.** Little's Law sizes the fleet.

## 🧪 Feynman check

Explain the waiter's three timings: why ten seconds of silence feels worse than a long speech, and when you'd care about the whole speech finishing.

⚠️ **Common confusion:** "Our average latency is 3 seconds, so we're fine." An average blends a fast majority with a terrible tail, and it blends TTFT with TPOT. Users abandon on the **p99 TTFT**, which can be 10× the median at peak.

## ⚡ Quick recall

1. What two things make up TTFT?
<details><summary>Reveal Answer</summary>

Time waiting in the queue for a GPU slot, plus the prefill of the prompt (and a little network time).
</details>

2. Why is a TPOT of 30 ms good enough for chat, but not always for agents?
<details><summary>Reveal Answer</summary>

Humans read ~4–6 tokens/s, so 33 tokens/s outpaces them. Agents wait for the entire output before their next step, so total time matters.
</details>

3. What is goodput?
<details><summary>Reveal Answer</summary>

The rate of requests that meet all their latency SLOs (e.g., both TTFT and TPOT), as opposed to raw throughput.
</details>

## 🎤 Interview practice

**Q. "Define latency SLOs for a customer-facing chat assistant, and explain how you'd size the GPU fleet to meet them at peak."**
<details><summary>Model answer</summary>

- **SLOs:** p95 **TTFT < 800 ms**, p95 **TPOT < 50 ms**, and availability ≥ 99.9%. Track **goodput** (share of requests meeting both) as the headline SLI.
- **Sizing with Little's Law:**
  - Peak arrival rate λ (e.g., 7k/s) × average sequence lifetime W (TTFT + output tokens × TPOT, e.g., 0.5 + 300 × 0.025 ≈ 8 s) = **~56k concurrent sequences**.
  - Benchmark one replica: the **maximum concurrent sequences** it holds while still meeting the TPOT SLO (limited by KV-cache memory and batch size).
  - Replicas = concurrent sequences ÷ per-replica capacity, **+ 30% headroom** and an N+1 buffer per zone.
- **Protecting TTFT at peak:** priority queues (interactive over batch), admission control, a cap on `max_tokens`, and a fallback to a smaller model when queues grow.
- **Likely follow-up:** "GPUs are scarce, so you can't buy the headroom. What then?" → shed or defer batch workloads, cache aggressively (lesson 018), route easy questions to a small model (lesson 015), and show a graceful "busy" state instead of a frozen bubble.
</details>

## 📖 Teaser

> 📖 *Maya's fix needs "56,000 slots", and finance wants to know what a slot actually is: how much GPU does one conversation really need?*

---

⬅️ [003 · Tokens & Context Windows](003-tokens-and-context-windows.md) · 🗺️ [Phase map](README.md) · ➡️ [005 · GPU Napkin Math](005-gpu-napkin-math.md)

✅ **Safe stopping point.** Tick lesson 004 in [PROGRESS.md](../../PROGRESS.md).
