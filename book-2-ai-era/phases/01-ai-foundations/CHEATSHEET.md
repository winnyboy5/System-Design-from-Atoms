# 📌 Phase 01 Cheatsheet: AI Foundations

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 001 | A model = **slow, priced per token, probabilistic, GPU-bound**. Classic atoms still apply. |
| 002 | **Tokenize → prefill (compute, TTFT) → decode loop (memory bandwidth, TPOT) → stream.** |
| 003 | **Context = budget.** History is re-sent every turn (quadratic). Pin rules, summarize, retrieve. |
| 004 | **TTFT, TPOT, E2E at percentiles.** Tail TTFT = queueing. Track **goodput**. |
| 005 | **Weights + KV cache + prefill compute.** The tightest budget sizes the fleet. |
| 006 | **Outputs are samples.** Temperature 0 ≠ deterministic. Ground, constrain, verify. |
| 007 | **SLOs on service, quality, and cost.** Pin model versions. |
| 008 | **Gate: should this be AI?** Then quality bar, error tiers, actions, cost, data, degraded mode. |

## 🔢 Numbers to know

```
1 token ≈ 4 chars ≈ ¾ English word · reading speed ≈ 4–6 tokens/s
TTFT: 100–300 ms (small model) · 0.5–2 s (large model, long prompt)
TPOT: 10–50 ms (20–100 tokens/s) · output tokens cost ~3–5× input tokens
FP16 = 2 B/param · FP8 = 1 · 4-bit = 0.5 → 70B FP16 = 140 GB
KV/token: ~0.1 MB (8B) to ~0.3 MB (70B, GQA)
H100-class GPU: 80 GB, ~3.35 TB/s, ~1 PFLOPS dense BF16
```

## 🧮 Formulas

| Formula | Use |
|---|---|
| Cost = msgs × (in_tokens × in_price + out_tokens × out_price) | Daily bill |
| E2E = TTFT + out_tokens × TPOT | Answer time |
| Weights = params × bytes | Will it fit? |
| KV/token = 2 × layers × kv_heads × head_dim × bytes | Memory per conversation |
| Slots = (GPU mem − weights − overhead) ÷ KV per sequence | Concurrency per server |
| Prefill FLOPs ≈ 2 × params × input tokens | Compute for prompts |
| TPOT ≈ (weights + batch KV) ÷ bandwidth | Decode speed |
| In-flight = arrival rate × sequence lifetime | Little's Law fleet size |

## 🧩 Mnemonics

- **"Read fast, write slow"**: prefill is parallel, decode is one token at a time.
- **"The model phrases, the database decides"**: facts come from systems of record.
- **"Fast, good, cheap: measure all three"**: service, quality, cost SLOs.
