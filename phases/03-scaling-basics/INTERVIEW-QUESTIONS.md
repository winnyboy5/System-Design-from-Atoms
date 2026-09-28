# 🎤 Phase 03 Interview Question Bank: Scaling Basics

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. Vertical vs horizontal scaling: pros and cons? · [017]
<details><summary>Model answer</summary>

Vertical: simple, no code changes, but it has a ceiling, it's a SPOF, and it gets expensive at the top. Horizontal: near-unlimited and redundant, but it needs statelessness, load balancing, and handling of distributed problems.
</details>

### 🟢 2. Why must app servers be stateless to scale out? · [018]
<details><summary>Model answer</summary>

So any server can handle any request. Servers can be added, removed, or killed without losing user data, and without needing sticky routing.
</details>

### 🟢 3. What does a health check do in a load balancer? · [019]
<details><summary>Model answer</summary>

It periodically verifies each backend can serve, and removes failing ones from rotation until they recover.
</details>

### 🟢 4. What is a CDN, and what content belongs on it? · [023]
<details><summary>Model answer</summary>

A network of edge caches near users. Static assets (images, JS/CSS, video), downloads, and public, cacheable API responses with short TTLs.
</details>

### 🟡 5. Compare token bucket and fixed-window rate limiting. · [024]
<details><summary>Model answer</summary>

Token bucket allows controlled bursts (up to the capacity) and enforces a smooth average rate. Fixed window is simpler but allows up to 2× the limit around window boundaries. The sliding window counter is a cheap middle ground.
</details>

### 🟡 6. L4 vs L7 load balancing: when would you use each? · [021]
<details><summary>Model answer</summary>

L4 for raw throughput, non-HTTP protocols, and TLS pass-through. L7 for path/header routing, TLS termination, retries, auth, and gRPC balancing. They're often layered: L4 at the edge, L7 behind it.
</details>

### 🟡 7. What does an API gateway do, and what should it NOT do? · [022]
<details><summary>Model answer</summary>

Do: routing, auth, rate limiting, logging, transformation, caching. Don't: business logic. It becomes a bottleneck and a coupling point that every team must change.
</details>

### 🟡 8. How do you do a zero-downtime deploy behind a load balancer? · [019, 025]
<details><summary>Model answer</summary>

A rolling or blue-green deploy. Readiness probes gate traffic to new instances. Connection draining and graceful shutdown for old ones. Backward-compatible DB changes. Automatic rollback on error-rate increases.
</details>

### 🟡 9. When should a company move from a monolith to microservices? · [026]
<details><summary>Model answer</summary>

When team size causes deploy contention, when parts have very different scaling or reliability needs, or when there are compliance isolation needs. Start with a modular monolith, and extract along domain boundaries with the strangler fig.
</details>

### 🔴 10. Design a distributed rate limiter for 1M requests/s across 100 gateway nodes. · [024]
<details><summary>Model answer</summary>

Token bucket per key in sharded Redis via an atomic Lua script. To cut the round trips: local token caching (each node leases batches of tokens) or approximate local counters synced every ~100 ms. Tiered limits and endpoint-specific limits. Fail open with local limits if Redis is degraded. Return 429 + headers. Monitor rejection rates and hot keys.
</details>

### 🔴 11. Your autoscaled service still falls over during sudden spikes. Diagnose and fix. · [025]
<details><summary>Model answer</summary>

Likely causes: scale-out lag (metric delay + boot time), scaling on the wrong metric, downstream bottlenecks (DB connections), and cold caches on new instances. Fixes: predictive or scheduled scaling, warm pools, faster boot, higher minimum replicas, a queue to absorb bursts, load shedding, connection poolers, and cache warming.
</details>

### 🔴 12. Explain the "distributed monolith" anti-pattern and how to escape it. · [026]
<details><summary>Model answer</summary>

Services that share databases and depend on long synchronous call chains, so they must deploy together and fail together. Escape by redrawing domain boundaries, giving each service ownership of its data, replacing sync chains with events and local read models, versioning APIs, and merging services that always change together.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 04 Caching](../04-caching/README.md)
