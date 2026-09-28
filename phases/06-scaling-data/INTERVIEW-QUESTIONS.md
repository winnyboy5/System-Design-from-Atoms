# 🎤 Phase 06 Interview Question Bank: Scaling Data

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. Replication vs sharding? · [046, 049]
<details><summary>Model answer</summary>

Replication copies the same data to multiple nodes (availability, read scaling). Sharding splits data across nodes (capacity, write scaling). Most large systems shard and replicate each shard.
</details>

### 🟢 2. Sync vs async replication? · [046]
<details><summary>Model answer</summary>

Sync waits for replicas before acknowledging (no data loss, slower). Async acknowledges immediately (fast, may lose recent writes on failover). Semi-sync waits for one replica.
</details>

### 🟢 3. What is the CAP theorem? · [052]
<details><summary>Model answer</summary>

In a network partition, a distributed system must choose between consistency (linearizable reads) and availability (every node responds). Partitions are unavoidable, so it's a C-vs-A decision during them.
</details>

### 🟢 4. What does idempotent mean? · [055]
<details><summary>Model answer</summary>

Performing the operation multiple times has the same effect as performing it once.
</details>

### 🟡 5. How do you provide read-your-writes consistency with read replicas? · [047]
<details><summary>Model answer</summary>

Route a user's reads of their own data to the leader (or pin them after a write), or track the write's LSN/timestamp and read only from replicas that have caught up. Also invalidate caches on write.
</details>

### 🟡 6. Explain consistent hashing and virtual nodes. · [051]
<details><summary>Model answer</summary>

Servers and keys hash onto a ring, and each key belongs to the next server clockwise, so adding or removing a server moves ~1/N of keys. Virtual nodes give each server many ring positions, for balance and spread-out recovery.
</details>

### 🟡 7. How do you choose a shard key? · [050]
<details><summary>Model answer</summary>

Pick from the top queries: high cardinality, even data and traffic distribution, common queries answered in one shard, and a value that rarely changes. Avoid monotonically increasing keys with range sharding. Plan for hot tenants.
</details>

### 🟡 8. What does R + W > N guarantee, and what doesn't it? · [054]
<details><summary>Model answer</summary>

It guarantees read and write replica sets overlap, so a read contacts at least one replica with the latest successful write. It doesn't by itself guarantee linearizability under sloppy quorums, concurrent writes, or partially failed writes.
</details>

### 🟡 9. How do you resolve conflicts in multi-leader replication? · [048]
<details><summary>Model answer</summary>

Avoid them (a home leader per record), last-write-wins (simple, loses data), version vectors with app-level merges (siblings), or CRDTs for automatic convergent merges.
</details>

### 🔴 10. Design a sharding migration from a single Postgres DB with zero downtime. · [049, 050]
<details><summary>Model answer</summary>

Choose the key and logical shard count. Build the routing layer. Backfill the target shards (snapshot + CDC catch-up). Dual-read verification. Switch reads by cohort. Switch writes (brief write pause or dual-write with reconciliation). Monitor. Keep a rollback path. Handle global uniqueness, sequences (switch to Snowflake IDs), and cross-shard queries via a warehouse.
</details>

### 🔴 11. Walk through a failover that causes split brain, and how to prevent it. · [046, 054]
<details><summary>Model answer</summary>

A network partition isolates the leader. Followers elect a new leader, while the old one still accepts writes from clients on its side, so two leaders make divergent writes. Prevent it with majority-quorum elections (the minority side can't elect), leader leases, and fencing tokens so storage rejects writes from stale leaders (lesson 086), plus STONITH or VIP moves.
</details>

### 🔴 12. Design idempotent, exactly-once-effect order processing across a queue and a DB. · [055]
<details><summary>Model answer</summary>

At-least-once delivery. Each message has an ID. The consumer, in one DB transaction, inserts `processed(message_id)` (unique) and applies the order state change. Duplicates hit the unique violation and are skipped. Outgoing events go through a transactional outbox (lesson 062). External calls use idempotency keys.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 07 Async & messaging](../07-async-messaging/README.md)
