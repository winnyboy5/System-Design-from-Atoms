# 010 · Static vs Continuous Batching

> ⏱ 12 min · 📈 20% · 🅰️ AI-era core · Phase 02: Model Serving & Inference Infrastructure
>
> `████░░░░░░░░░░░░░░░░` 20% of Book 2
>
> 🧬 **Atoms used:** queues [B1·057] · latency vs throughput [B1·004] · [002] · [004] · [009]

---

## 📖 Story

Maya's first serving setup batches requests the obvious way: collect **32** questions, run them through the GPU together, return them together, repeat.

Most answers are short: "Yes, it's vegan." **60 tokens.** But one customer asked for a full **Thanksgiving menu with recipes**: **2,000 tokens**.

So the GPU generates 60 tokens for everyone, and then **31 finished conversations sit in the batch**, doing nothing, while one sequence writes a cookbook. For **two-thirds of a minute**, the batch runs at **1/32 of its width**.

Meanwhile, **400 new questions** pile up at the door. They can't join until the whole batch finishes. Their TTFT: **48 seconds**.

I told Maya she'd built a **lift that won't open its doors until every passenger reaches the top floor**. The fix is a lift that lets people on and off at **every floor**. Let me show you.

## 🎯 One-sentence idea

**Continuous batching schedules the GPU one decode step at a time, so finished sequences leave and waiting sequences join between any two steps, which keeps the batch full, multiplies throughput several times over, and stops long answers from blocking short ones.**

## 🧸 Analogy

A **lift in a busy building**:

- **Static batching:** the lift fills up, goes to the top, and only opens again when **every** passenger has reached their floor. People getting off at floor 2 ride all the way up and down. People in the lobby wait for the whole trip.
- **Continuous batching:** the doors open at **every floor**. Riders leave the moment they arrive, and new riders step in **immediately**. The lift is always nearly full, and nobody waits for the slowest passenger.

## 🖼️ Visual

*Diagram brief:* two grids of GPU decode steps over time. In the static grid, rows (sequences) end at different times, leaving a large empty white triangle of wasted slots until the longest row finishes. In the continuous grid, as each row ends, a new row starts in its slot at the very next step, so the grid stays nearly solid.

```
STATIC (batch of 4)                     CONTINUOUS (4 slots)
step → 1 2 3 4 5 6 7 8 9                step → 1 2 3 4 5 6 7 8 9
seq A  ■ ■ ✓ · · · · · ·                slot1  A A ✓ E E E ✓ H H
seq B  ■ ■ ■ ■ ✓ · · · ·                slot2  B B B B ✓ F F F F
seq C  ■ ■ ■ ■ ■ ■ ■ ■ ✓                slot3  C C C C C C C C ✓
seq D  ■ ✓ · · · · · · ·                slot4  D ✓ G G G G ✓ I I
        · = wasted slot                 E–I join the moment a slot frees up
new requests wait until step 9 ⏳       new requests wait ≤ 1 step ⚡
```

## 🔬 How it works

- **Why batch at all:** decode is memory-bound (lesson 002). Reading the weights once and producing **one token for each of 64 sequences** costs barely more than producing one token for one, so batching multiplies throughput almost for free.
- **Static batching** forms a batch, runs it to completion, then forms the next. Utilization ≈ **average length ÷ longest length**, so one long answer wastes the whole batch, and new arrivals wait for the slowest sequence.
- **Continuous (iteration-level) batching:** the scheduler runs **one decode step**, removes sequences that hit a stop token, **admits waiting sequences** into the freed slots (running their prefill), and repeats. Slots stay full, and TTFT stops depending on other people's answer lengths.
- **Chunked prefill:** a new 10k-token prompt would stall everyone's decode while it's prefilled. So the scheduler splits long prefills into **chunks** (e.g., 512 tokens) mixed into decode steps, keeping TPOT smooth.
- **Admission control:** the scheduler admits sequences only while **KV-cache memory** remains (lesson 011) and the batch stays small enough to meet the **TPOT SLO**. Priorities let interactive chat jump ahead of batch jobs, and **preemption** pauses low-priority sequences when memory runs out.

## 🧩 Worked example

**One GPU, 32 slots, a realistic answer-length mix** (90% of answers ~60 tokens, 10% ~2,000 tokens):

```
Static batching:
  A batch almost always contains a 2,000-token answer (1 − 0.9³² ≈ 97%)
  → each batch runs ~2,000 steps
  Useful tokens = 32 × (0.9 × 60 + 0.1 × 2,000) ≈ 8,100
  Slot-steps available = 32 × 2,000 = 64,000 → utilization ≈ 13%

Continuous batching:
  Slots refill every step → utilization ≈ 90% (scheduling overhead aside)
  Throughput ≈ 90 / 13 ≈ 7× higher on the same GPU
```

**Maya's dinner rush, replayed:** the Thanksgiving menu still takes 2,000 steps, but **2,000 other answers** flow through the other 31 slots while it does. **TTFT p95: 48 s → 0.6 s. Tokens/s/GPU: ×6.8.** The planned GPU order shrinks by **85%**.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Static batching | Simple to build | Wasted slots, head-of-line blocking |
| Continuous batching | 3–10× throughput, steady TTFT | Complex scheduler, memory management |
| Bigger max batch | Lower cost per token | Higher TPOT for everyone |
| Chunked prefill | Smooth TPOT during long prompts | Slightly slower TTFT for the long prompt |
| Priority + preemption | Interactive traffic protected | Batch jobs slow down at peak |

## 🌍 Real world

- The **Orca** paper (2022) introduced iteration-level scheduling. **vLLM, SGLang, TensorRT-LLM, and TGI** all use continuous batching by default.
- **Chunked prefill** is standard in modern engines to keep decode latency stable.
- Some large deployments go further and **disaggregate** prefill and decode onto separate GPU pools, each batched for its own bottleneck.

## 📌 Cheat card

> - **Batching = one weight read, many tokens.** Decode is memory-bound, so it's nearly free.
> - **Static: utilization ≈ avg length ÷ max length.** Head-of-line blocking.
> - **Continuous: join and leave every step.** 3–10× throughput.
> - **Chunked prefill** keeps TPOT smooth.
> - **Admit by KV memory and TPOT SLO.** Prioritize interactive.

## 🧪 Feynman check

Explain the two lifts: why people on the first lift wait for the slowest passenger, and how opening the doors at every floor keeps the lift full.

⚠️ **Common confusion:** "Batching always adds latency because requests wait to fill a batch." That's true for **static** batching with a fill timeout. With continuous batching, a new request joins at the **next step**, typically milliseconds away, so batching **cuts** queueing delay rather than adding it.

## ⚡ Quick recall

1. Why is batching nearly free for decode?
<details><summary>Reveal Answer</summary>

Decode is memory-bandwidth-bound: one read of the weights can produce a token for every sequence in the batch, with spare compute to do the extra math.
</details>

2. What is the main flaw of static batching?
<details><summary>Reveal Answer</summary>

The batch runs until its longest sequence finishes, so finished slots sit idle and new requests wait (head-of-line blocking).
</details>

3. What does chunked prefill prevent?
<details><summary>Reveal Answer</summary>

A long new prompt stalling everyone else's decode steps, which would spike their time per output token.
</details>

## 🎤 Interview practice

**Q. "Design the request scheduler for an LLM inference server that serves both interactive chat and bulk summarization on the same GPUs."**
<details><summary>Model answer</summary>

- **Core loop:** continuous batching. Each iteration: run one decode step for active sequences, evict finished ones, then admit waiting sequences while **KV-cache blocks** are available and the batch is under the **TPOT-derived cap**.
- **Two priority queues:** interactive (strict TTFT/TPOT SLOs) and bulk (throughput only). Interactive is admitted first. Bulk fills spare slots.
- **Prefill handling:** chunked prefill with a per-iteration token budget, so long bulk prompts never stall interactive decode.
- **Preemption:** when memory runs short, pause bulk sequences (swap their KV to CPU memory, or drop and recompute later). Never preempt interactive sequences mid-answer.
- **Fairness and limits:** per-tenant concurrency caps and `max_tokens` limits, so one tenant can't fill every slot.
- **Metrics:** queue depth per class, KV utilization, batch size, TTFT/TPOT per class, preemptions/s, and tokens/s/GPU.
- **Likely follow-up:** "When would you split them onto separate fleets?" → when bulk volume is large enough that preemption churn hurts both, or when a different (cheaper, quantized) model is acceptable for bulk.
</details>

## 📖 Teaser

> 📖 *The lift now opens at every floor, until it stops letting anyone in at all: the GPU reports "out of memory" with 40 passengers aboard and half the space empty.*

---

⬅️ [009 · GPUs & Accelerators for Designers](009-gpus-and-accelerators.md) · 🗺️ [Phase map](README.md) · ➡️ [✅ Checkpoint 20%](checkpoint-20.md)

✅ **Safe stopping point.** Tick lesson 010 in [PROGRESS.md](../../PROGRESS.md), then do the checkpoint!
