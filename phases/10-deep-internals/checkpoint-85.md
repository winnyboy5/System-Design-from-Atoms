# ✅ Checkpoint 85%: Under the Hood

> ⏱ 15 min · Covers lessons **081–085** · 📈 You're at **85%**
>
> `█████████████████░░░` You now understand the machinery inside databases and consensus systems. This is staff-level material.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Maya can now explain what happens deep inside Pantry's databases and consensus clusters. Let's test your depth.*

---

## ⚡ Part 1: Recall (5 questions)

1. B-tree vs LSM tree: which is write-optimized, and why?
<details><summary>Answer</summary>

LSM. Writes are sequential appends (WAL + memtable, then flushes to sorted files), with no in-place page updates.
</details>

2. Under MVCC, why don't readers block writers?
<details><summary>Answer</summary>

Writers create new row versions, while readers see the version visible to their snapshot, so no read locks are needed.
</details>

3. What does the "ELC" in PACELC mean?
<details><summary>Answer</summary>

Else (no partition), choose between latency and consistency.
</details>

4. Lamport vs vector clocks: what can vector clocks detect?
<details><summary>Answer</summary>

Concurrency: that two events happened without knowledge of each other.
</details>

5. When is a Raft entry committed?
<details><summary>Answer</summary>

When it's stored on a majority of the nodes.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

5-minute timer. Explain to a curious friend:

> "How can 5 computers agree on the order of events when any of them might crash, and their clocks don't agree?"

Must include: **leader, term, majority, log, why clocks aren't used for ordering**.

---

## 🛠️ Part 3: Mini-design

**A configuration service** (like etcd) storing feature flags for 10,000 servers. Requirements: never serve conflicting values, survive 2 node failures, and let clients watch for changes.

Decide: cluster size, the consensus approach, the read strategy, how clients get updates, and what happens during a partition.

<details><summary>One good answer</summary>

- **5 nodes** (tolerate 2 failures), **Raft**, spread across 3 AZs (2-2-1).
- Writes go to the leader and commit on a majority (3 of 5).
- Reads: linearizable reads via ReadIndex for critical ones, and serializable (follower, possibly stale) reads for high-volume polling.
- Clients use **watch** streams (long-lived gRPC) from any node, keyed by revision, and resume from the last revision after a reconnect.
- Partition: the majority side keeps serving writes. The minority side rejects writes (and linearizable reads). Clients reconnect to the majority.
- 10k watchers: fan them out across all nodes or proxies. Keep the data small (config only).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Why does Postgres need VACUUM?"**
<details><summary>Model answer</summary>

MVCC leaves dead row versions after updates and deletes. VACUUM reclaims their space, updates visibility maps, and freezes old transaction IDs to prevent wraparound. Long transactions block it.
</details>

**Q2. "What's the danger of using timestamps for last-write-wins across regions?"**
<details><summary>Model answer</summary>

Clock skew can make an older write carry a larger timestamp, so the truly newer write is discarded silently. Use version vectors, HLC plus conflict handling, single-writer ownership, or consensus.
</details>

**Q3. "Raft vs leader-follower async replication?"**
<details><summary>Model answer</summary>

Raft commits on a majority (no acknowledged-write loss, safe automatic leader election, no split brain) at the cost of majority round-trip latency. Async leader-follower is faster, but can lose acknowledged writes on failover, and needs external failover logic and fencing.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [086 · Leader Election, Locks & Fencing](086-leader-election-and-locks.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [081](081-btree-lsm-and-wal.md), [084](084-time-and-clocks.md), [085](085-consensus-raft.md) |

---

⬅️ [085 · Consensus (Raft)](085-consensus-raft.md) · 🗺️ [Phase map](README.md) · ➡️ [086 · Leader Election, Locks & Fencing](086-leader-election-and-locks.md)

✅ Tick **Checkpoint 85%** in [PROGRESS.md](../../PROGRESS.md). 🎉
