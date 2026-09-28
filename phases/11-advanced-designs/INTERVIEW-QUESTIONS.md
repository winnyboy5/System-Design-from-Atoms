# 🎤 Phase 11 Interview Question Bank: Data Processing & Advanced Designs

> Full prompts + follow-ups. 🟢 warm-up · 🟡 standard · 🔴 deep-dive

---

### 🟢 1. Batch or stream for a daily revenue report? For fraud detection? · [091]
<details><summary>Key points</summary>

Daily report → batch (cheap, exact, and a delay is fine). Fraud detection → stream (a decision within ms to seconds), with batch model training.
</details>

### 🟢 2. How do you find restaurants within 2 km? · [095]
<details><summary>Key points</summary>

Geohash at a matching precision, query the cell + 8 neighbours, filter by haversine distance, then rank and paginate. Or use an ES geo_distance / PostGIS query.
</details>

### 🟡 3. Design a distributed key-value store. · [092]
<details><summary>Key points</summary>

Consistent hashing + vnodes, N replicas (AZ-aware), R/W quorums, vector clocks, hinted handoff, read repair, Merkle anti-entropy, gossip membership, an LSM engine, and tombstones.
</details>

### 🟡 4. Design search autocomplete. · [093]
<details><summary>Key points</summary>

Query logs → aggregated counts → a trie with top-K per node (offline) + a trending layer (stream). In-memory serving, replicated or sharded by prefix. CDN caching of short prefixes. Client debounce. Filtering.
</details>

### 🟡 5. Design a web crawler for 1B pages/month. · [094]
<details><summary>Key points</summary>

A frontier with priority + per-host queues, host-affine workers, robots.txt, a DNS cache, URL normalization + a Bloom filter, content hash/SimHash dedup, WARC storage in S3, adaptive re-crawl, and trap limits.
</details>

### 🟡 6. Design Uber's matching. · [095]
<details><summary>Key points</summary>

Driver locations in in-memory geo shards by region. Candidate search by geo cell. ETA ranking. Atomic driver reservation (conditional update). Offer with a timeout. Trip creation in a transactional DB. The location stream to Kafka.
</details>

### 🟡 7. Design a payment system. · [096]
<details><summary>Key points</summary>

Idempotency keys, a state machine, PSP integration with webhooks, a double-entry append-only ledger, the outbox, sagas, reconciliation, and tokenization/PCI.
</details>

### 🟡 8. Design a distributed cron/job scheduler. · [097]
<details><summary>Key points</summary>

A jobs table with a next_run_at index, leader-elected dispatchers, `SKIP LOCKED` claims, leases + heartbeats, retries with backoff → DLQ, idempotent jobs, fencing via the attempt number, and a misfire policy.
</details>

### 🔴 9. Design Ticketmaster for a mega on-sale. · [098]
<details><summary>Key points</summary>

A virtual waiting room at the edge, bot defenses, a cached seat map, atomic seat holds with a TTL (DB conditional update / Redis Lua), partitioning by event/section, a checkout saga with hold release on failure, and a confirmation guard that checks the hold's validity.
</details>

### 🔴 10. Design Twitter trending topics. · [099]
<details><summary>Key points</summary>

Kafka keyed by hashtag → windowed counts (CMS + heap) per partition → an aggregator for the global top-K per region → Redis. Velocity scoring vs baseline. Spam/bot filtering. Batch recompute.
</details>

### 🔴 11. Follow-up: "The payment provider's webhook never arrived. What happens?" · [096]
<details><summary>Key points</summary>

The payment stays PENDING. A scheduled poller queries the PSP for pending payments older than N minutes. Reconciliation catches anything that's still missing. The user sees "processing", not "failed".
</details>

### 🔴 12. Follow-up: "Your KV store must now support strongly consistent reads for some keys." · [092, 085]
<details><summary>Key points</summary>

Per-request consistency levels (QUORUM/ALL, still imperfect with sloppy quorums), or move those keys to a consensus-backed partition (a Raft group per range) with leader reads (ReadIndex/leases). Discuss the latency and availability trade-offs.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · 🎓 [Checkpoint 100%](checkpoint-100.md)
