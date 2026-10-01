# 012 · Quantization & Model-Size Trade-offs

> ⏱ 12 min · 📈 24% · 🅰️ AI-era core · Phase 02: Model Serving & Inference Infrastructure
>
> `████░░░░░░░░░░░░░░░░` 24% of Book 2
>
> 🧬 **Atoms used:** compression trade-offs [B1·004] · [005] · [006] · [009] · [011]

---

## 📖 Story

Finance has read about **4-bit models** online: "**A quarter of the memory. A quarter of the GPUs.**"

Maya downloads a 4-bit version of Pantry's 70B model, deploys it to a canary, and the numbers look amazing. Weights shrink from **140 GB to 35 GB**. TPOT drops from **22 ms to 9 ms**. Each server holds **more than twice** as many conversations.

Then a customer bakes the "Ask the Chef" banana bread and posts a photo of a **brick**. The recipe called for **"1 tsp baking soda"**. The 4-bit chef wrote **"1 tbsp"**. That's three times as much.

Maya runs the golden set (lesson 007). Overall score: **92% → 89%**. Not terrible. But on the **quantities and conversions** slice, it falls from **97% to 84%**.

I told Maya that quantization is like **photocopying a recipe at lower resolution**. Most of it reads fine. But the **small print** (the numbers) is the first thing to blur. Let me show you how to shrink a model without baking bricks.

## 🎯 One-sentence idea

**Quantization stores a model's weights (and optionally its KV cache and activations) in fewer bits, cutting memory and speeding up memory-bound decode by 2–4×, at a quality cost that varies by task, so it must be validated on your own evals, slice by slice, before you ship it.**

## 🧸 Analogy

**Photocopying a cookbook at lower resolution**:

- At **high resolution** (16-bit), everything is crisp.
- At **medium** (8-bit), it's half the paper and still perfectly readable.
- At **low** (4-bit), it's a quarter of the paper, and most text reads fine, but the **tiny numbers** in the margins start to smudge.
- Good photocopiers **sharpen the important parts** (smart quantization methods protect sensitive weights). You still **proofread** before printing a thousand copies.

## 🖼️ Visual

*Diagram brief:* three bars for the same 70B model at FP16, FP8, and 4-bit, shrinking from 140 GB to 70 GB to 35 GB. Under each bar, a small tile shows the golden-set score, with the quantities slice highlighted in red for the 4-bit bar.

```
FP16  ████████████████████████████  140 GB · TPOT 22 ms · golden 92% · quantities 97%
FP8   ██████████████                 70 GB · TPOT 13 ms · golden 92% · quantities 96% ✅
INT4  ███████                        35 GB · TPOT  9 ms · golden 89% · quantities 84% ❌
```

## 🔬 How it works

- **Formats:** **FP16/BF16** (2 bytes, the training default), **FP8** (1 byte, natively supported by recent GPUs), and **INT4/4-bit** (½ byte, weight-only). Each halving cuts the bytes read per decode step, so TPOT drops almost proportionally (lesson 002).
- **What gets quantized:** **weights only** (the most common: INT4 methods like GPTQ/AWQ, which compute in higher precision), **weights + activations** (FP8 end to end, which also speeds up compute-bound prefill), and the **KV cache** (FP8 KV doubles concurrency, lesson 011).
- **Quality loss is uneven.** Small models lose more than big ones. **Arithmetic, numbers, code, rare languages, and long reasoning** degrade first. Averages hide this, so evaluate **per slice** of your golden set.
- **Calibration matters:** good methods use sample data to find **outlier weights** and keep them precise, scale values per group, and skip sensitive layers. A careless 4-bit conversion is far worse than a careful one.
- **Alternatives:** a **distilled** smaller model (trained to imitate the big one) can beat a heavily quantized big model. Compare **quality per dollar**, not just bits.

## 🧩 Worked example

**Maya's quantization decision table** (70B model, 8×80 GB server, Pantry golden set of 400 cases × 3 runs):

| Variant | Weights | KV slots/server | TPOT | Golden | Quantities slice | $ / 1M tokens |
|---|---|---|---|---|---|---|
| FP16 | 140 GB | ~660 | 22 ms | 92.1% | 97% | 1.0× |
| FP8 (W + A + KV) | 70 GB | ~1,500 | 13 ms | 91.8% | 96% | **0.45×** |
| INT4 weights + FP8 KV | 35 GB | ~1,600 | 9 ms | 89.0% | 84% | 0.40× |

```
FP8 KV slots: (640 − 70 − 60) GB ÷ (2,000 × 0.165 MB) ≈ 1,550
```

**The decision:** ship **FP8** everywhere: a **55% cost cut** for 0.3 points of quality, within the noise of the eval. Keep INT4 for the **recipe-tagging batch job**, whose slices didn't regress. And add a **deterministic unit converter** as a verifier on quantities (lesson 006), so no model variant can bake a brick again.

## ⚖️ Trade-offs

| Choice | You gain | You pay |
|---|---|---|
| FP8 | ~2× memory and speed, minimal loss on large models | Needs recent hardware for full speed-ups |
| INT4 weight-only | ~4× weight memory, faster decode | Noticeable loss on numbers, code, reasoning |
| FP8 KV cache | ~2× concurrency | Small loss on long contexts |
| Distilled small model | Bigger savings, faster | Training cost, lower ceiling |
| Stay at FP16 | Maximum quality | Most GPUs |

## 🌍 Real world

- **FP8 inference** is standard on recent data-centre GPUs, and many open models ship official FP8 checkpoints.
- **GPTQ, AWQ**, and similar methods are the common 4-bit weight-only approaches, especially for local and edge deployment.
- Providers serve **"mini"/"flash"** tiers that are often distilled and quantized, which is why they're 10–30× cheaper.

## 📌 Cheat card

> - **FP16 = 2 B, FP8 = 1 B, INT4 = ½ B per weight.** Fewer bytes → faster decode.
> - **Quantize weights, activations, and/or the KV cache.**
> - **Numbers, code, and reasoning degrade first.** Eval **per slice**.
> - **FP8 is the safe default on large models.** INT4 needs proof.
> - **Compare quality per dollar** vs distilled smaller models.

## 🧪 Feynman check

Explain the low-resolution photocopy: why most of the recipe still reads fine, why the numbers blur first, and why you proofread before printing.

⚠️ **Common confusion:** "The 4-bit model scored only 3 points lower, so it's fine." Average scores hide **concentrated** damage. A 3-point average drop can be a **13-point** drop on exactly the slice that matters, such as quantities, dosages, or code. Always break evals down by slice.

## ⚡ Quick recall

1. Why does quantization speed up decode?
<details><summary>Reveal Answer</summary>

Decode is memory-bandwidth-bound, and fewer bytes per weight means less data to read on every step.
</details>

2. Which kinds of tasks usually degrade first under aggressive quantization?
<details><summary>Reveal Answer</summary>

Arithmetic and numbers, code, long multi-step reasoning, and rare languages.
</details>

3. What's the advantage of quantizing the KV cache?
<details><summary>Reveal Answer</summary>

It shrinks per-token memory (FP8 halves it), so a server can hold roughly twice as many concurrent conversations.
</details>

## 🎤 Interview practice

**Q. "Your inference cost must drop by 50% without hurting quality. Walk through how you'd evaluate quantization and alternatives."**
<details><summary>Model answer</summary>

- **Baseline:** measure the current $ per 1M tokens, TTFT/TPOT, and golden-set scores **by slice** (task type, language, numeric-heavy, long context).
- **Candidates:**
  - FP8 weights + activations + KV (usually near-lossless on large models).
  - INT4 weight-only with a calibrated method.
  - A smaller or distilled model, possibly with routing so only hard queries hit the big model (lesson 015).
- **Evaluation:**
  - Run each candidate on the golden set **N times per case**, and compare per slice with confidence intervals.
  - Load-test each for tokens/s/GPU at the TPOT SLO, then compute **$ per 1M tokens**.
- **Decision rule:** choose the cheapest variant with **no statistically significant regression** on any critical slice. Add verifiers for critical fields as defence in depth.
- **Rollout:** canary with online quality SLIs (lesson 044), and automatic rollback on a quality burn.
- **Likely follow-up:** "FP8 alone gives 45%, not 50%. What else?" → prefix caching for shared prompts, shorter outputs, and routing easy traffic to a small model usually close the gap.
</details>

## 📖 Teaser

> 📖 *FP8 fits beautifully, until product asks for a model so large that even at one byte per weight, it won't fit inside a single server.*

---

⬅️ [011 · The KV Cache & PagedAttention](011-kv-cache-and-pagedattention.md) · 🗺️ [Phase map](README.md) · ➡️ [013 · Tensor & Pipeline Parallelism](013-model-parallelism.md)

✅ **Safe stopping point.** Tick lesson 012 in [PROGRESS.md](../../PROGRESS.md).
