# 🎤 Phase 04 Interview Question Bank: Caching

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. What is a cache hit ratio and why does it matter? · [027]
<details><summary>Model answer</summary>

Hits ÷ total lookups. It decides how much load reaches the backing store and what latency the average request gets. Going from 90% to 99% cuts DB reads 10×.
</details>

### 🟢 2. Explain cache-aside. · [028]
<details><summary>Model answer</summary>

The app checks the cache. On a miss, it loads from the DB, stores the result with a TTL, and returns it. On a write, it updates the DB and deletes the cache key.
</details>

### 🟢 3. LRU vs LFU? · [030]
<details><summary>Model answer</summary>

LRU evicts the least recently used item (good for recency). LFU evicts the least frequently used item (keeps long-term popular items, and needs decay).
</details>

### 🟢 4. Redis vs Memcached? · [033]
<details><summary>Model answer</summary>

Redis: rich data structures, persistence, replication, clustering, pub/sub. Memcached: a simple multi-threaded string cache with no persistence. Redis is the default choice today.
</details>

### 🟡 5. Why is "delete on write" preferred over "update cache on write"? · [028, 031]
<details><summary>Model answer</summary>

Concurrent writes can apply cache updates out of order, leaving a stale value indefinitely. Deleting forces the next read to fetch the current value. The remaining races are bounded by the TTL.
</details>

### 🟡 6. When is write-back caching appropriate? · [029]
<details><summary>Model answer</summary>

For high-rate, loss-tolerant writes like counters, likes, views, and metrics, where coalescing many updates into periodic DB writes saves huge load. Not for financial or critical data.
</details>

### 🟡 7. What is a cache stampede and how do you prevent it? · [032]
<details><summary>Model answer</summary>

Many requests miss the same key at once (usually after it expires) and hammer the DB. Prevent it with single-flight/locking, serve-stale-while-revalidate, probabilistic early refresh, TTL jitter, and background refresh for hot keys.
</details>

### 🟡 8. How do you handle a hot key? · [032]
<details><summary>Model answer</summary>

Local in-process caching with a short TTL, key splitting into N replicas across shards, read replicas, CDN for public content, and hot-key detection.
</details>

### 🟡 9. How do you keep multiple caches (Redis, CDN, search) consistent with the DB? · [031]
<details><summary>Model answer</summary>

Event-driven invalidation via CDC (Debezium → Kafka) or a transactional outbox. Consumers delete Redis keys, purge CDN surrogate keys, and update search. TTLs as a safety net. Idempotent consumers.
</details>

### 🔴 10. Describe the stale-set race in cache-aside and three mitigations. · [031]
<details><summary>Model answer</summary>

Reader misses and reads the old value → writer updates the DB and deletes the key → reader sets the old value. Mitigations: leases (only the lease holder may set, and invalidation revokes leases), version-checked sets, delayed double delete, and short TTLs.
</details>

### 🔴 11. Design a caching layer for a service with 1M reads/s and 10k writes/s. · [027–033]
<details><summary>Model answer</summary>

Estimate the hot set → size a Redis Cluster (with replicas) for ≤ 70% memory use. Cache-aside with a TTL + jitter. Invalidate via events. An L1 local cache for the hottest keys. Single-flight on misses. Pipelining and pooling. A circuit breaker to the DB with load shedding. Monitor hit ratio, evictions, p99, and hot keys. Target ≥ 99% hit ratio → the DB sees ≤ 10k reads/s.
</details>

### 🔴 12. Your cache cluster fails completely. What happens and how do you design for it? · [032, 033]
<details><summary>Model answer</summary>

An avalanche: the DB gets 100% of reads, and may cascade into a full outage. Design: HA cache (replicas, multi-AZ), a circuit breaker so requests don't wait on a dead cache, DB concurrency limits and load shedding (serve degraded responses), gradual cache warming on recovery, and priority for critical traffic.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 05 Databases](../05-databases/README.md)
