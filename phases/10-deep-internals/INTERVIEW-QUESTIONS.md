# 🎤 Phase 10 Interview Question Bank: Deep Internals

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. What's a write-ahead log for? · [081]
<details><summary>Model answer</summary>

Durability and atomicity: changes are appended and fsynced to a sequential log before being acknowledged, and replayed after crashes. It also feeds replication and CDC.
</details>

### 🟢 2. What's the difference between Lamport and vector clocks? · [084]
<details><summary>Model answer</summary>

Lamport clocks give a total order consistent with causality, but can't detect concurrency. Vector clocks track per-node counters and can tell whether events are ordered or concurrent.
</details>

### 🟢 3. What does a Bloom filter guarantee? · [090]
<details><summary>Model answer</summary>

No false negatives: "not present" is certain. "Present" may be a false positive, at a tunable rate.
</details>

### 🟡 4. B-tree vs LSM tree trade-offs? · [081]
<details><summary>Model answer</summary>

B-tree: in-place updates, low read amplification, random write I/O. LSM: sequential writes and high throughput, with read amplification (mitigated by Bloom filters), compaction overhead, and tombstones.
</details>

### 🟡 5. How does MVCC implement snapshot isolation? · [082]
<details><summary>Model answer</summary>

Each row version is tagged with its creating and expiring transaction IDs. A transaction's snapshot determines which versions are visible. Updates create new versions, and old ones are garbage-collected later.
</details>

### 🟡 6. Explain Raft leader election and why split votes are rare. · [085]
<details><summary>Model answer</summary>

A follower times out without heartbeats, becomes a candidate, increments the term, and requests votes. Nodes vote once per term, and only for up-to-date logs. A majority wins. Randomized election timeouts desynchronize candidates.
</details>

### 🟡 7. How do you implement a correct distributed lock? · [086]
<details><summary>Model answer</summary>

A consensus-backed store (etcd/ZooKeeper) with leases (TTL + renewal), and fencing tokens (a revision or term) passed with every protected write, which the resource validates against the highest token it has seen.
</details>

### 🟡 8. When would you use a saga, and how do you make it reliable? · [088]
<details><summary>Model answer</summary>

For multi-service business transactions. Use an orchestrator with persisted state, idempotent steps and compensations, an outbox for events, timeouts, irreversible steps last, and semantic locks (PENDING states).
</details>

### 🔴 9. Explain how Spanner achieves external consistency globally. · [084, 085]
<details><summary>Model answer</summary>

Paxos-replicated shards, and 2PC across shards with Paxos-backed participants. TrueTime gives bounded clock uncertainty. Commit timestamps are chosen and then the system waits out the uncertainty (commit wait), so timestamp order equals real-time order. MVCC enables lock-free consistent snapshot reads at any replica.
</details>

### 🔴 10. Design anti-entropy between replicas of a 10 TB dataset. · [087, 090]
<details><summary>Model answer</summary>

Merkle trees per token range. Replicas exchange roots and descend into the mismatched subtrees, then stream only the differing ranges. Schedule it incrementally to limit I/O. Combine it with read repair and hinted handoff for fast convergence. Gossip handles membership.
</details>

### 🔴 11. Design an event-sourced ledger. · [089, 055]
<details><summary>Model answer</summary>

A per-account event stream (append with expected version), double-entry events, idempotency keys per transaction, snapshots for fast balance loads, projections for balances, statements, and analytics, reconciliation jobs, crypto-shredding for PII, and event versioning (upcasters).
</details>

### 🔴 12. Why can't a failure detector be both fast and always accurate? · [087]
<details><summary>Model answer</summary>

In asynchronous networks, delays are unbounded, so slow and dead look identical. Short timeouts give false positives. Long timeouts give slow detection. Use adaptive (phi accrual) and indirect (SWIM) detection, and design for false suspicions (fencing, idempotency).
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 11 Advanced designs](../11-advanced-designs/README.md)
