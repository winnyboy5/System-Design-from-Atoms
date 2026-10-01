# 005 · GPU Napkin Math

> ⏱ 13 min · 📈 10% · 🅰️ AI-era core · Phase 01: AI Foundations for System Designers
>
> `██░░░░░░░░░░░░░░░░░░` 10% of Book 2
>
> 🧬 **Atoms used:** back-of-envelope estimation [B1·005] · Little's Law [B1·004] · [002] · [003] · [004]

---

## 📖 Story

The $2.1M-a-day bill has reached the boardroom. Finance asks Maya a fair question: "What if we **ran our own model**? An open 70-billion-parameter one. How many GPUs is that?"

Maya rents one top-end GPU, **80 GB of memory**, and loads the model.

`CUDA out of memory.`

She rents a second one. The model loads. She sends a test question and gets a lovely answer. She sends **forty** questions at once, and the server crashes again.

She has no idea whether Pantry needs **10 GPUs or 10,000**. In Book 1, she'd have answered that on a napkin in two minutes.

I told Maya the napkin still works. GPUs just have **three budgets** instead of one: memory for the model, memory for the conversations, and compute for reading prompts. Let me show you the arithmetic.

## 🎯 One-sentence idea

**GPU capacity comes from three napkin calculations: model weights (parameters × bytes per parameter), the KV cache (bytes per token × tokens per sequence × concurrent sequences), and prefill compute (2 × parameters × prompt tokens per second), and the tightest of the three sets the size of the fleet.**

## 🧸 Analogy

A **food truck kitchen**:

- The **cookbook** is bolted to the counter, and it takes up a fixed amount of space (the weights).
- Every customer's **order ticket** is clipped up while their meal is cooked, and bigger orders need bigger tickets (the KV cache).
- The truck fits as many customers as there's **counter space left** after the cookbook.
- And separately, the chef can only **read** so many tickets per minute (prefill compute).

## 🖼️ Visual

*Diagram brief:* one 8-GPU server drawn as a long bar of 640 GB of GPU memory. A solid block on the left holds the model weights, a thin block holds runtime overhead, and the rest is filled with many small, equal tiles, each one an active conversation's KV cache.

```
8 × 80 GB GPU memory = 640 GB per server
┌──────────────┬──────┬───────────────────────────────────────────────┐
│ Weights 70B  │ Run- │ KV cache: ~660 conversations × 0.66 GB each   │
│ FP16 = 140 GB│ time │ ▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢▢ │
│  (fixed)     │ 60 GB│ (grows with every token of every conversation)│
└──────────────┴──────┴───────────────────────────────────────────────┘
```

## 🔬 How it works

- **Weights = parameters × bytes per parameter.** FP16/BF16 = **2 bytes**, FP8 = **1 byte**, 4-bit = **0.5 bytes**. So a 70B model is **140 GB** in FP16 and **35 GB** in 4-bit. An 8B model is 16 GB in FP16.
- **KV cache per token = 2 (K and V) × layers × KV heads × head size × bytes.** For a typical 70B model (80 layers, 8 KV heads of 128): 2 × 80 × 8 × 128 × 2 B ≈ **0.33 MB per token**. Multiply by tokens per conversation and by concurrent conversations.
- **Concurrent sequences per server ≈ (GPU memory − weights − runtime overhead) ÷ KV per sequence.** This is the "slot" count from lesson 004, and it's usually the **binding limit** for decode.
- **Prefill compute ≈ 2 × parameters × input tokens per second.** Divide by the server's **achieved** FLOPS (~40–60% of peak) to get how many servers just to **read** prompts. Long prompts can make this the tightest budget.
- **Decode speed ≈ bytes read per step ÷ memory bandwidth**, where bytes read = weights + the batch's KV cache. Check that the resulting TPOT meets the SLO before trusting the slot count.

## 🧩 Worked example

**Pantry on an open 70B model, H100-class servers** (8 GPUs × 80 GB, ~3.35 TB/s and ~1 PFLOPS each):

```
1. Memory per server
   Weights (FP16)          = 70B × 2 B           = 140 GB  (needs ≥ 2 GPUs)
   Runtime + activations   ≈                        60 GB
   KV budget               = 640 − 140 − 60      = 440 GB
   One conversation        = 2,000 tokens × 0.33 MB = 0.66 GB
   Slots per server        = 440 ÷ 0.66          ≈ 660

2. Decode speed check
   Bytes per step = 140 GB weights + 440 GB KV = 580 GB
   ÷ (8 × 3.35 TB/s = 26.8 TB/s)               ≈ 22 ms TPOT ✅ (< 50 ms SLO)

3. Servers by memory (lesson 004 needs ~55,000 slots at peak)
   55,000 ÷ 660 ≈ 84 servers

4. Servers by prefill compute
   Input = 2,300 msg/s × 1,500 tokens = 3.45M tokens/s (average!)
   FLOPs = 2 × 70B × 3.45M ≈ 4.8 × 10¹⁷ /s
   Per server ≈ 8 × 1 PFLOPS × 50% = 4 × 10¹⁵ /s
   → ~120 servers on average, more at peak 😮
```

**So this means:** the **prompts**, not the conversations, are the real bottleneck. The 800-token system prompt is identical on every request, so **caching its prefill** (lesson 018) removes over half the compute. The 4-bit model (lesson 012) quadruples the KV budget. Maya's napkin now has a target: **~100 servers instead of guesswork**, at roughly **$50–80k/day** of GPU rental, versus $2.1M/day in API fees, if quality holds up.

## ⚖️ Trade-offs

| Lever | Frees | Costs |
|---|---|---|
| Lower precision (FP8, 4-bit) | 2–4× weight and KV memory | Some quality loss (measure it) |
| Shorter conversations | KV memory, prefill compute | Less context |
| Smaller model | Everything | Quality |
| Bigger GPUs (more GB per card) | More slots per server | Higher price per hour |
| Prefix caching | Prefill compute | Cache memory, routing complexity |

## 🌍 Real world

- Model cards publish **layers, KV heads, and head size**, which is all you need for the KV formula. **Grouped-query attention** (few KV heads) is why modern models have small KV caches.
- **vLLM** reports the **KV-cache capacity in tokens** at start-up: it's exactly this calculation.
- GPU generations differ mainly in **memory size and bandwidth** (80 → 141 → 192+ GB), which directly sets slots per server and TPOT.

## 📌 Cheat card

> - **Weights = params × bytes** (FP16 = 2, FP8 = 1, 4-bit = 0.5).
> - **KV/token = 2 × layers × KV heads × head size × bytes** (~0.1–0.3 MB for 8B–70B models).
> - **Slots = (GPU mem − weights − overhead) ÷ KV per sequence.**
> - **Prefill FLOPs ≈ 2 × params × input tokens.** Use ~50% of peak FLOPS.
> - **TPOT ≈ (weights + batch KV) ÷ bandwidth.** The tightest budget wins.

## 🧪 Feynman check

Explain the food truck: why the cookbook's size decides how many order tickets fit, why a big order uses more counter, and why a chef who reads slowly can be the bottleneck even with space to spare.

⚠️ **Common confusion:** "The model is 140 GB, so two 80 GB GPUs are enough." They hold the **weights**, with almost no room left for any conversation's KV cache. Capacity is set by what's left **after** the weights, and by prefill compute.

## ⚡ Quick recall

1. How much memory do a 70B model's weights take in FP16, and in 4-bit?
<details><summary>Reveal Answer</summary>

140 GB in FP16 (2 bytes per parameter), and 35 GB in 4-bit (0.5 bytes per parameter).
</details>

2. What determines how many concurrent conversations a server can hold?
<details><summary>Reveal Answer</summary>

The GPU memory left after weights and overhead, divided by the KV cache per conversation (bytes per token × tokens per conversation).
</details>

3. How do you estimate prefill compute?
<details><summary>Reveal Answer</summary>

About 2 × parameters × input tokens per second, divided by the server's achieved FLOPS (≈ half of peak).
</details>

## 🎤 Interview practice

**Q. "Estimate the GPUs needed to serve an 8B-parameter model to 1M daily users who each send 10 messages, with 2,000-token prompts and 300-token answers."**
<details><summary>Model answer</summary>

- **Traffic:** 10M messages/day ≈ 115/s average, ~350/s peak.
- **Weights:** 8B × 2 B = **16 GB** (FP16), so one 80 GB GPU holds the model with ~55 GB left after overhead.
- **KV:** a typical 8B model (32 layers, 8 KV heads × 128) ≈ 2 × 32 × 8 × 128 × 2 B = **128 KB/token** → 2,300 tokens × 128 KB ≈ **0.3 GB** per sequence → ~180 slots per GPU.
- **Concurrency (Little's Law):** a sequence lives ≈ 0.3 s TTFT + 300 × 15 ms ≈ 5 s → 350/s × 5 s ≈ **1,750 slots** → **~10 GPUs** by memory.
- **Prefill compute:** 350/s × 2,000 = 700k tokens/s × 2 × 8B = 1.1 × 10¹⁶ FLOPs/s ÷ ~0.5 PFLOPS per GPU ≈ **~22 GPUs** at peak → **prefill is the binding limit**.
- **Answer:** ~**25–30 GPUs** with headroom and zone redundancy, dropping to ~15 with prefix caching of shared prompt parts.
- **Likely follow-up:** "How do you validate?" → load-test one replica with a realistic prompt/answer length mix, measure max concurrency at the TPOT SLO, then scale linearly.
</details>

## 📖 Teaser

> 📖 *Maya's napkin finally balances, and then QA files a terrifying bug: the satay question passes nine times out of ten, with the same prompt, the same model, and the same code.*

---

⬅️ [004 · AI Latency Numbers: TTFT, TPOT & Tokens/s](004-ai-latency-numbers.md) · 🗺️ [Phase map](README.md) · ➡️ [✅ Checkpoint 10%](checkpoint-10.md)

✅ **Safe stopping point.** Tick lesson 005 in [PROGRESS.md](../../PROGRESS.md), then do the checkpoint!
