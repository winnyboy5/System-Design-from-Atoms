# 🎤 Phase 01 Interview Question Bank: Foundations

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 5 min.

---

### 🟢 1. What is the difference between scalability and performance? · [001, 004]
<details><summary>Model answer</summary>

**Performance** = how fast the system is for *one* user or request (latency). **Scalability** = whether it *stays* fast as load grows, by adding resources. A system can be fast for one user and collapse at 1,000 (performant, not scalable).
</details>

### 🟢 2. What is p99 latency and why do we care? · [003]
<details><summary>Model answer</summary>

The time under which 99% of requests complete. It captures the "tail" that averages hide. At high volume, 1% is many users, and fan-out means tails affect most page loads.
</details>

### 🟢 3. How much downtime per year is 99.99%? · [006]
<details><summary>Model answer</summary>

About **52.6 minutes per year**, or about 4.4 minutes per month.
</details>

### 🟢 4. What is the difference between an SLO and an SLA? · [007]
<details><summary>Model answer</summary>

An SLO is an internal reliability target. An SLA is an external contract with penalties. The SLA is looser than the SLO so there's a buffer.
</details>

### 🟡 5. Estimate QPS and storage for a service with 50M DAU posting 3 items/day of 2 KB, kept 3 years. · [005]
<details><summary>Model answer</summary>

- Writes: 150M/day ÷ 10⁵ = **1,500 QPS** (peak ~4,500).
- Storage/day: 150M × 2 KB = **300 GB/day**. 3 years ≈ 300 GB × 1,100 ≈ **330 TB**, × 3 replicas ≈ **~1 PB**.
- Implication: a horizontally scalable store, sharding, and a retention/tiering strategy.
</details>

### 🟡 6. A request chain has 4 services at 99.9% each. What's the end-to-end availability? How do you improve it? · [006]
<details><summary>Model answer</summary>

0.999⁴ ≈ **99.6%**. Improve it with redundancy for each component (parallel copies), by making non-critical dependencies soft (cache, fallback, async), with circuit breakers, and by reducing the number of hard dependencies.
</details>

### 🟡 7. Your thread pool keeps running out. Requests arrive at 800/s and take 250 ms. How big should the pool be? · [004]
<details><summary>Model answer</summary>

Little's Law: 800 × 0.25 = **200 concurrent**. Size about **300–400** for headroom, *and* ask why requests take 250 ms. Reducing latency shrinks the pool you need.
</details>

### 🟡 8. What questions do you ask at the start of any design interview? · [008]
<details><summary>Model answer</summary>

Core features and out-of-scope items. Users and scale (DAU, QPS, data size). Read/write ratio. Latency targets. Consistency vs availability. Durability. Geography. Special constraints (compliance, cost).
</details>

### 🟡 9. Why is "average response time" a bad SLO? · [003, 007]
<details><summary>Model answer</summary>

It's skewed by outliers in both directions and hides the tail. Good SLOs are ratio-based on percentiles: "99% of requests under 300 ms over 30 days".
</details>

### 🔴 10. Walk through everything that happens when you type a URL and press Enter, and where you'd optimize. · [002]
<details><summary>Model answer</summary>

DNS (browser → OS → resolver → root/TLD/authoritative) → TCP handshake → TLS handshake (cert validation, key exchange) → HTTP request → CDN edge (cache hit?) → LB → app server → cache/DB/services → response → HTML parse → sub-resource fetches → render. Optimizations: DNS prefetch, connection reuse/HTTP/2+, TLS 1.3/session resumption, CDN caching, server-side caching, compression, fewer round trips, lazy loading.
</details>

### 🔴 11. Explain tail latency amplification and three ways to fight it. · [003]
<details><summary>Model answer</summary>

With fan-out to n backends, P(at least one slow) = 1 − (1 − p)ⁿ. With 100 backends at 1% slow each, about 63% of requests are slow. Fixes: **hedged requests** (send a backup after the p95 time), **timeouts with fallbacks**, **reducing fan-out** (aggregate or cache), **fixing sources of variance** (GC tuning, isolation from noisy neighbours).
</details>

### 🔴 12. How do error budgets change how a team works? · [007]
<details><summary>Model answer</summary>

They turn reliability into a shared, quantified resource. Budget remaining → ship fast and experiment. Budget exhausted → freeze risky changes and invest in reliability. This aligns product and SRE, prevents both over-caution and recklessness, and makes "is it reliable enough?" a data question instead of an opinion.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 02 Networking](../02-networking/README.md)
