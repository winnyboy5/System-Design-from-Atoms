# ✅ Checkpoint 90%: 🎉 Level-Up! Distributed Systems Depth

> ⏱ 15 min · Covers lessons **086–090** · 📈 You're at **90%**
>
> `██████████████████░░` 🎉 **90%!** You understand the deep machinery: locks, fencing, gossip, distributed transactions, event sourcing, and probabilistic structures.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Pantry's engine room holds no more mysteries for Maya. There's one chapter left, and I want you ready for it.*

---

## ⚡ Part 1: Recall (5 questions)

1. What's a fencing token, and who checks it?
<details><summary>Answer</summary>

A monotonically increasing number issued with each lock or lease grant. The protected resource (storage) checks it and rejects writes carrying a lower token than it has already seen.
</details>

2. How fast does gossip spread information?
<details><summary>Answer</summary>

In about O(log N) rounds, with constant per-node load.
</details>

3. 2PC vs saga: the key difference?
<details><summary>Answer</summary>

2PC holds locks and commits atomically (blocking, fragile). A saga runs local transactions with compensations (available, but no isolation).
</details>

4. In event sourcing, what's the source of truth?
<details><summary>Answer</summary>

The append-only log of domain events. State is derived by replaying it.
</details>

5. Which structure answers "how many distinct users?" in ~12 KB?
<details><summary>Answer</summary>

HyperLogLog.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

5-minute timer. Explain to a friend:

> "How does an online shop make sure an order, its payment, and the stock update all 'happen together', when each lives in a different service?"

Must include: **saga, compensation, idempotency, outbox, why not 2PC**.

---

## 🛠️ Part 3: Mini-design

**A trending-hashtags feature**: 500k posts/s. Show the top 10 hashtags in the last 5 minutes, and also the number of unique users who posted each top hashtag.

Choose the data structures and outline the pipeline.

<details><summary>One good answer</summary>

- Kafka topic of hashtag events → a stream processor (Flink) with **sliding 5-minute windows**.
- Per window: a **count-min sketch** for frequencies + a **min-heap of the top-K** candidates (the heavy-hitters algorithm), or exact counts for the top candidates.
- An **HLL per top hashtag** for unique users (mergeable across partitions).
- Partition by hashtag, with local top-K per partition merged by an aggregator into the global top 10.
- Results go to Redis for serving, and are refreshed every few seconds. (Lesson 099 covers this in depth.)
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Why is a single-Redis lock unsafe for correctness?"**
<details><summary>Model answer</summary>

Async replication can lose the lock on failover, and there's no fencing, so a paused client can act after its lease expires. Use consensus-based locks plus fencing tokens checked by the resource.
</details>

**Q2. "How do Cassandra replicas find and fix inconsistencies?"**
<details><summary>Model answer</summary>

Read repair (on reads with mismatches), hinted handoff (for writes missed during short outages), and anti-entropy repair using Merkle trees to compare replica data ranges and stream only the differences.
</details>

**Q3. "When would you NOT use event sourcing?"**
<details><summary>Model answer</summary>

Simple CRUD domains without audit or history needs, teams unfamiliar with the pattern, and cases where strong immediate read consistency across many views is required. The complexity (versioning, projections, GDPR) outweighs the benefits.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to Phase 11: [091 · Batch vs Stream Processing](../11-advanced-designs/091-batch-vs-stream-processing.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [086](086-leader-election-and-locks.md), [088](088-2pc-vs-sagas.md), [090](090-probabilistic-data-structures.md) |

🏆 **Level-up reward:** 90%. Ten lessons to go, and they're the fun, specialist designs!

---

⬅️ [090 · Probabilistic Data Structures](090-probabilistic-data-structures.md) · 🗺️ [Phase map](README.md) · ➡️ [091 · Batch vs Stream Processing](../11-advanced-designs/091-batch-vs-stream-processing.md)

✅ Tick **Checkpoint 90%** in [PROGRESS.md](../../PROGRESS.md). 🎉 **Phase 10 complete!** Review the [cheatsheet](CHEATSHEET.md) and [interview bank](INTERVIEW-QUESTIONS.md).
