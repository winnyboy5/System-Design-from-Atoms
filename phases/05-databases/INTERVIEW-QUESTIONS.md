# 🎤 Phase 05 Interview Question Bank: Databases

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. SQL vs NoSQL: when would you use each? · [034]
<details><summary>Model answer</summary>

SQL for relational data, ad-hoc queries, and transactions (the default). NoSQL for massive scale with simple, known access patterns, flexible schemas, or special shapes (KV, wide-column, graph).
</details>

### 🟢 2. What does ACID stand for? · [035]
<details><summary>Model answer</summary>

Atomicity, Consistency, Isolation, Durability, with one sentence of explanation for each.
</details>

### 🟢 3. What is an index, and what does it cost? · [037]
<details><summary>Model answer</summary>

A sorted auxiliary structure (a B-tree) that finds rows without full scans. It costs storage and slows writes (every write updates every index).
</details>

### 🟢 4. What's the N+1 problem? · [040]
<details><summary>Model answer</summary>

One query for a list plus one query per item for related data. Fix it with joins, batching, or eager loading.
</details>

### 🟡 5. Explain isolation levels and one anomaly each prevents. · [036]
<details><summary>Model answer</summary>

Read Committed prevents dirty reads. Repeatable Read/Snapshot prevents non-repeatable reads (and some phantoms). Serializable prevents write skew and phantoms, behaving as if transactions ran one at a time.
</details>

### 🟡 6. How would you model chat messages in Cassandra? · [038]
<details><summary>Model answer</summary>

Partition key `(chat_id, time_bucket)`, clustering key `message_time DESC`. Queries read the latest N messages from one partition. Separate tables for other access patterns. Bound partition size with buckets.
</details>

### 🟡 7. When should you denormalize? · [039]
<details><summary>Model answer</summary>

For hot read paths where joins or aggregates are too slow: counters, read models, search documents, and historical snapshots. Keep the normalized source of truth, and sync copies through transactions or events.
</details>

### 🟡 8. OLTP vs OLAP, and how does data move between them? · [041]
<details><summary>Model answer</summary>

OLTP: small transactions, row store, live app. OLAP: large scans, column store, analytics. Data moves via batch ETL/ELT or CDC streaming into a lake or warehouse.
</details>

### 🟡 9. How do you prevent double-booking a hotel room? · [035, 036]
<details><summary>Model answer</summary>

Enforce it in the database: an exclusion constraint on (room, date range), or a unique row per (room, night) with a unique constraint, or lock the room row before checking. Serializable isolation with retries is an alternative.
</details>

### 🔴 10. Design the storage for a global e-commerce platform. · [034–045]
<details><summary>Model answer</summary>

Postgres (sharded by customer or region) for orders, users, and payments. Redis for sessions, carts, and cache. S3 + CDN for images. Elasticsearch for product search (fed by CDC). Kafka for events. A warehouse for analytics. A TSDB for metrics. Justify each with its access pattern, and discuss consistency across stores (outbox/CDC) and the source of truth.
</details>

### 🔴 11. Your Postgres primary is at 95% CPU. Walk through the diagnosis and scaling path. · [037, 040, 045]
<details><summary>Model answer</summary>

Find the top queries (pg_stat_statements) → add or fix indexes, kill N+1, add caching → pool connections → read replicas for reads → a vertical upgrade → partition big tables → move analytics out → shard (Citus/app-level) or move specific workloads to specialized stores. Measure after each step.
</details>

### 🔴 12. Why do random UUID primary keys hurt write performance, and what do you use instead? · [037]
<details><summary>Model answer</summary>

Random inserts spread across the whole B-tree, so there are more page splits, a poor cache hit rate, and write amplification. Use time-ordered IDs (UUIDv7, ULID, Snowflake), so inserts append near the right edge of the index (lesson 072).
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 06 Scaling data](../06-scaling-data/README.md)
