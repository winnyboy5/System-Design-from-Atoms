# 📌 Phase 02 Cheatsheet: Model Serving

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 009 | GPUs = **capacity, bandwidth, FLOPS, interconnect.** Compare **$ per 1M tokens**, not $ per hour. |
| 010 | **Continuous batching:** join and leave every step. Chunked prefill. Admit by KV memory and TPOT cap. |
| 011 | **Paged KV cache:** ~16-token blocks on demand, block tables, shared prefixes, preempt by swap or recompute. |
| 012 | **Quantize:** FP8 ≈ safe default, INT4 needs proof. **Eval per slice.** |
| 013 | **TP inside a server, PP across, EP for MoE, replicas for throughput.** |
| 014 | **SSE + durable buffer + Last-Event-ID + idempotency.** Heartbeats. "Stop" frees the slot. |
| 015 | **Model gateway:** tasks not URLs, token quotas, routing, fallbacks, metering. |
| 016 | **Scale on queue/KV signals.** Predictive + warm pool. Cut every slice of the cold start. |
| 017 | **Speculative decoding:** draft k, verify in one pass, lossless, best at small batches. |
| 018 | **Prefix cache (always), exact cache (sometimes), semantic cache (scoped).** Static first. |

## 🔢 Numbers to know

```
NVLink ~900 GB/s per GPU · network ~50 GB/s per 400 Gb/s link · PCIe ~64 GB/s
Static batching utilization ≈ avg length ÷ max length (often 10–30%)
Contiguous KV waste 60–80% · paged KV waste < 4%
Weights: 70B → 140 GB FP16 · 70 GB FP8 · 35 GB INT4
Cold start: provision + image + weights + load (minutes!) · 140 GB at 600 MB/s ≈ 4 min
Speculative decoding: 2–3× lower TPOT at small batch sizes
Prefix cache hits with routing: often > 90% for shared system prompts
```

## 🧮 Formulas

| Formula | Use |
|---|---|
| $ per 1M tokens = ($/hour) ÷ (tokens/hour ÷ 10⁶) | Hardware comparison |
| Tokens per spec step = (1 − α^(k+1)) ÷ (1 − α) | Speculative decoding gain |
| Routed cost = Σ (share × price) per model | Routing savings |
| Load time = weight bytes ÷ aggregate read bandwidth | Cold-start slice |
| Prefill saved = cached prefix tokens ÷ prompt tokens × hit rate | Prefix caching gain |

## 🧩 Mnemonics

- **"Lift doors at every floor"**: continuous batching.
- **"Rooms, not floors"**: paged KV.
- **"Chefs share a dish only through a hatch"**: TP needs NVLink.
- **"Retry the delivery, never the cooking"**: stream resume.
- **"The fastest token is the one you never generate."**
