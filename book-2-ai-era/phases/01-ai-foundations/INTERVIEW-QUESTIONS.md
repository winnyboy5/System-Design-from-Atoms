# 🎤 Phase 01 Interview Question Bank: AI Foundations

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 5 min.

---

### 🟢 1. What is a token, and why does it matter for system design? · [003]
<details><summary>Model answer</summary>

A sub-word unit the model reads and writes (~4 characters of English). Cost, latency, and context limits are all measured in tokens, so prompt and answer length are design parameters.
</details>

### 🟢 2. What's the difference between TTFT and TPOT? · [002, 004]
<details><summary>Model answer</summary>

TTFT = time until the first output token (queue + prefill). TPOT = time between output tokens during decode. TTFT drives perceived responsiveness, TPOT drives streaming speed and total time.
</details>

### 🟢 3. Why is decode memory-bound? · [002]
<details><summary>Model answer</summary>

Each step reads all weights and the KV cache from GPU memory to produce one token per sequence, with little arithmetic per byte, so bandwidth, not FLOPS, limits it.
</details>

### 🟢 4. Is temperature 0 deterministic? · [006]
<details><summary>Model answer</summary>

Not reliably. Batching and GPU floating-point ordering can flip near-ties. It's more consistent, not guaranteed identical, and not guaranteed correct.
</details>

### 🟡 5. Estimate the memory for a 70B model serving 500 concurrent 4k-token conversations. · [005]
<details><summary>Model answer</summary>

Weights 140 GB (FP16). KV ≈ 0.33 MB/token × 4,000 = 1.3 GB per conversation × 500 = **~660 GB**. Total ≈ 800 GB + overhead → at least **two 8×80 GB servers**, or one with FP8 weights and KV (~400 GB).
</details>

### 🟡 6. How do you keep a long chat's cost bounded? · [003]
<details><summary>Model answer</summary>

Cap the input budget, keep a sliding window of recent turns, summarize older turns, store durable facts in a profile, and retrieve old turns only when relevant. Cost becomes linear in messages.
</details>

### 🟡 7. Which SLIs would you add for an AI feature beyond latency and availability? · [007]
<details><summary>Model answer</summary>

Quality (golden-set score, sampled groundedness, thumbs-down/regenerate rates) and cost (tokens and $ per request and per conversation, cache hit rate), plus model version tracking.
</details>

### 🟡 8. When should you *not* use an LLM? · [008]
<details><summary>Model answer</summary>

When rules, search, or a lookup can do it reliably: prices, balances, eligibility, status. Use the model for open-ended language and judgement, and render facts from systems of record.
</details>

### 🔴 9. Size a GPU fleet for 5,000 messages/s, 1,000-token prompts, 250-token answers, on a 70B model. · [004, 005]
<details><summary>Model answer</summary>

- **Lifetime:** ~0.4 s TTFT + 250 × 25 ms ≈ 6.7 s → **~33k concurrent** sequences (Little's Law).
- **Memory:** 1,250 tokens × 0.33 MB ≈ 0.41 GB each. An 8×80 GB server has ~440 GB for KV → ~1,000 slots → **~33 servers**.
- **Prefill:** 5k × 1,000 = 5M tokens/s × 2 × 70B = 7 × 10¹⁷ FLOPs/s ÷ 4 × 10¹⁵ per server ≈ **~175 servers** → prefill dominates.
- **So:** prefix-cache shared prompt parts, use FP8, consider a smaller model for easy turns, and possibly **disaggregate** prefill and decode onto separate pools. Add 30% headroom.
</details>

### 🔴 10. Design the quality-monitoring system for an LLM product used by millions. · [006, 007]
<details><summary>Model answer</summary>

- A golden set (offline, versioned, run N times per case) on every change and hourly.
- Online sampling (1–2%) scored by a calibrated LLM judge, with weekly human audits.
- User signals in a stream (thumbs, regenerate, escalation) aggregated per model version and prompt version.
- Version pinning, prompt hashes in every trace, quality SLOs with burn-rate alerts, and automatic rollback of the last change when quality budgets burn.
</details>
