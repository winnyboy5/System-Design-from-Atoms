# 🎤 Phase 02 Interview Question Bank: Model Serving

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 5 min.

---

### 🟢 1. What is continuous batching? · [010]
<details><summary>Model answer</summary>

Scheduling at the level of individual decode steps: finished sequences leave and new ones join between steps, keeping the batch full and avoiding head-of-line blocking.
</details>

### 🟢 2. What is PagedAttention? · [011]
<details><summary>Model answer</summary>

Managing the KV cache in small fixed-size blocks mapped by per-sequence block tables, allocated on demand like virtual memory. It eliminates most fragmentation and enables prefix sharing.
</details>

### 🟢 3. Why is SSE usually preferred for token streaming? · [014]
<details><summary>Model answer</summary>

It's one-way server-to-client over plain HTTP, works through most infrastructure, and has built-in reconnect with event IDs for resuming.
</details>

### 🟢 4. What's the difference between prefix caching and semantic caching? · [018]
<details><summary>Model answer</summary>

Prefix caching reuses the computed KV of an identical prompt beginning, so outputs are unchanged and it's always safe. Semantic caching reuses a previous **answer** for a similar question, which can be wrong when similar ≠ same.
</details>

### 🟡 5. How would you compare two GPU types for serving a model? · [009]
<details><summary>Model answer</summary>

Benchmark tokens/s per GPU at your TTFT/TPOT SLOs with realistic prompt and answer lengths, then compute $ per 1M tokens. Check memory fit (weights + KV) and availability. Don't use hourly price or "GPU util %".
</details>

### 🟡 6. When does speculative decoding not help? · [017]
<details><summary>Model answer</summary>

At large batch sizes, where compute is already busy, and when acceptance rates are low (unpredictable text, or a poorly matched draft). It also costs extra memory for the draft.
</details>

### 🟡 7. How do you route between a cheap and an expensive model without hurting quality? · [015]
<details><summary>Model answer</summary>

Rules by task, a learned difficulty router, or a cascade with validation-based escalation. Evaluate the routing policy on a golden set by slice, monitor online quality per route, and keep fallbacks.
</details>

### 🟡 8. Why does GPU autoscaling need prediction? · [016]
<details><summary>Model answer</summary>

Cold starts take minutes (provisioning, images, weights), longer than many spikes. Predictive scaling and warm pools provide capacity before demand arrives, and reactive scaling handles surprises.
</details>

### 🔴 9. Design the inference platform for a company serving five models to 20 internal products. · [009–016]
<details><summary>Model answer</summary>

- **Gateway:** one API by task, token quotas per product, routing, fallbacks, metering, and caching.
- **Serving pools per model:** continuous batching, paged KV with FP8, TP sized to the TPOT SLO, and replicas by Little's Law. Separate long-context pools.
- **Scheduling:** priority classes (interactive vs batch), preemption, and batch jobs in off-peak hours or on spot capacity.
- **Capacity:** reserved baseline, predictive scaling, warm pools, fast weight loading, and multi-region placement.
- **Observability:** TTFT/TPOT/goodput, KV utilization, cache hit rate, and $ per 1M tokens per model and product.
</details>

### 🔴 10. Your inference bill must halve in a quarter. Plan it. · [012, 015, 017, 018]
<details><summary>Model answer</summary>

1. **Measure:** spend by product, model, input vs output tokens, and cache hit rate.
2. **Quick wins:** prompt restructuring + prefix caching, shorter outputs (`max_tokens`, structured outputs), and dropping unused context.
3. **Routing:** small models for easy intents, cascades, and eval-gated routing.
4. **Efficiency:** FP8, continuous-batching tuning, speculative decoding where latency-bound, and right-sized GPUs.
5. **Workload shifts:** batch jobs to discounted batch APIs or off-peak.
6. **Guardrails:** quality SLOs per change, canaries, and budget alerts per product.
</details>
