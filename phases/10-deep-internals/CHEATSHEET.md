# 📌 Phase 10 Cheatsheet: Deep Internals

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 081 | **WAL first.** **B-tree** = update in place (reads). **LSM** = memtable → SSTables → compaction (writes). |
| 082 | **MVCC** = versions + snapshots → readers don't block writers. Needs **VACUUM/purge**. Long transactions = bloat. |
| 083 | **PACELC:** Partition → A or C. **Else → Latency or Consistency** (every request). |
| 084 | **Clocks lie.** Lamport (causal order), vector (concurrency), HLC, TrueTime (commit wait). |
| 085 | **Raft:** leader per term, commit on majority, up-to-date vote rule. 2f+1 tolerates f. |
| 086 | **Leases + fencing tokens.** Correctness locks need consensus. Redis locks are for efficiency only. |
| 087 | **Gossip:** O(log N) spread. **Failure detection = suspicion** (phi accrual, SWIM). |
| 088 | **2PC** (atomic, blocking) vs **saga** (compensations, no isolation). Irreversible steps last. |
| 089 | **Event sourcing** (history is the truth) + **CQRS** (separate read models). Overkill for CRUD. |
| 090 | **Bloom** (seen?), **HLL** (distinct), **count-min** (frequency), **Merkle** (diff). |

## 🔢 Numbers

```
Raft: 3 nodes → tolerates 1 · 5 → 2 · election timeout ~150–300 ms (randomized)
NTP skew: ms on a LAN, tens of ms over the internet · TrueTime ε: a few ms
Bloom: ~9.6 bits/item → 1% FP, k ≈ 7 · HLL: ≤12 KB, ~0.81% error
Gossip: fanout 2–3, interval ~1 s, rounds ≈ log_k(N)
```

## 🪄 Mnemonics

- **"Ask the diary before the binder"**: WAL before the data pages.
- **LSM = "sticky notes → booklets → merge."**
- **PACELC:** "**P**artition? **A** or **C**. **E**lse? **L** or **C**."
- **Lamport:** "max + 1".
- **Saga:** "every step has an undo button."
- **Bloom:** "**No** means no. **Yes** means maybe."

## ⚠️ Top mistakes

- Ordering cross-machine events by wall-clock timestamps.
- Distributed locks without fencing. Redis locks for correctness.
- Long-running transactions on MVCC databases.
- 2PC across independent microservices.
- Event sourcing a simple CRUD app.
- Trusting a Bloom filter "yes" without checking the source.

---

🗺️ [Phase map](README.md) · 🎤 [Interview questions](INTERVIEW-QUESTIONS.md)
