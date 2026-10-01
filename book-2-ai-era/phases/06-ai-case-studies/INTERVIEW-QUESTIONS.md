# 🎤 Phase 06 Interview Question Bank: AI Case Studies

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 45 min (full mock).

---

### 🟢 1. What dominates the design of a ChatGPT-scale assistant? · [045]
<details><summary>Model answer</summary>

GPU concurrency and cost: hundreds of thousands of simultaneous streams, each holding KV memory, so batching, caching, routing, and tiers decide feasibility.
</details>

### 🟢 2. What's the hardest requirement in enterprise document Q&A? · [046]
<details><summary>Model answer</summary>

Never showing a user content they can't access in the source systems, with fast propagation of permission changes.
</details>

### 🟢 3. Why does a coding agent need a sandbox per task? · [047]
<details><summary>Model answer</summary>

It runs arbitrary code and commands. Isolation (no secrets, restricted network, ephemeral) contains mistakes and injected instructions.
</details>

### 🟢 4. Why not use an LLM to pick recommendations per request? · [048]
<details><summary>Model answer</summary>

Too slow and expensive to choose from millions of items in milliseconds. Embedding retrieval + trained ranking does it in ~60 ms, and LLMs help offline and with explanations.
</details>

### 🟡 5. Break down a voice assistant's latency budget. · [049]
<details><summary>Model answer</summary>

Turn detection ~200 ms, streaming ASR final ~100 ms, LLM TTFT ~250 ms, first TTS audio ~120 ms, network ~80 ms ≈ 750 ms, all overlapping by streaming.
</details>

### 🟡 6. How would you evaluate a coding agent? · [047, 037]
<details><summary>Model answer</summary>

Real repository tasks with hidden tests in sandboxes (task success), plus cost and steps per task, policy checks for cheating, and production PR acceptance and revert rates.
</details>

### 🟡 7. How do you handle cold start for new items in a recommender? · [048]
<details><summary>Model answer</summary>

Content-based embeddings (text, image, attributes) via the item tower, freshness boosts, and exploration slots until interaction data builds up.
</details>

### 🔴 8. Full mock: design a ChatGPT-style assistant for 100M DAU. · [045]
<details><summary>Model answer</summary>

Follow lesson 045: napkin (1.5B msgs/day, ~775k concurrent streams), gateway with tiers and token quotas, conversation store and budgeted context builder, router with affinity, GPU pools (continuous batching, paged FP8 KV, TP), durable SSE, guards, fallback ladder, metering. Deep dives: GPU capacity and prefix caching, streaming reliability, cost per tier.
</details>

### 🔴 9. Full mock: design an AI assistant over enterprise documents for 2,000 companies. · [046]
<details><summary>Model answer</summary>

Follow lesson 046: connectors with ACL sync and reconciliation, incremental ingestion, a hybrid multi-tenant index with PQ, identity-derived filters, rerank, late checks, citations, quarantined untrusted content, per-tenant evals, leak probes, freshness and deletion SLOs.
</details>

### 🔴 10. Full mock: design a voice ordering assistant with 10k concurrent calls. · [049]
<details><summary>Model answer</summary>

Follow lesson 049: SIP/WebRTC to regional media servers, VAD + turn detection, streaming ASR, a fast LLM with tools, sentence-chunked TTS, barge-in, spoken read-backs before orders, GPU pools sized by concurrent streams, and fallbacks to IVR or humans.
</details>
