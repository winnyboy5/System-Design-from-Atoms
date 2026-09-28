# ✅ Checkpoint 55%: Distributed Data Thinking

> ⏱ 15 min · Covers lessons **051–055** · 📈 You're at **55%**
>
> `███████████░░░░░░░░░` You can now reason about CAP, consistency, quorums, and retries. These are the concepts interviewers probe hardest.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *The double-charge bug is fixed for good. Before I tell you about the dinner rush, let's review distributed data.*

---

## ⚡ Part 1: Recall (5 questions)

1. How many keys move when you add a node with `hash mod N`, versus with consistent hashing?
<details><summary>Answer</summary>

With mod N, almost all of them. With consistent hashing, about 1/N.
</details>

2. What does CAP actually force you to choose, and when?
<details><summary>Answer</summary>

Between consistency (linearizability) and availability, **during a network partition**.
</details>

3. What's the strongest consistency model that stays available during partitions?
<details><summary>Answer</summary>

Causal consistency.
</details>

4. N=3. Give a W and R that guarantee fresh reads and tolerate one failure.
<details><summary>Answer</summary>

W=2, R=2.
</details>

5. How do idempotency keys prevent double charges?
<details><summary>Answer</summary>

The server records each key with its result. Retries with the same key return the stored result instead of repeating the side effect.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "If a computer network breaks in half, why can't every computer both keep working AND always give the right answer?"

Must include: **partition, consistency, availability, and an example of picking each**.

---

## 🛠️ Part 3: Mini-design

**A global "like" button** on posts: 500k likes/s at peak, across 3 regions. Users must never see their own like disappear. Counts may lag by a few seconds. A user can like a post only once.

Choose the consistency levels, the replication approach, and how to ensure "only once".

<details><summary>One good answer</summary>

- **Only once:** the source of truth is a `likes(post_id, user_id)` record with a **unique key**, so duplicates are idempotent. Writes go to the user's **home region** (conflict avoidance), or use a set CRDT.
- **Counts:** eventual. Regional sharded counters (write-back) aggregated asynchronously, and multi-region merge via a G-Counter-style sum.
- **Read-your-writes:** the client shows its own like optimistically, and the user's "did I like it?" check reads from the home region, or from session state.
- Replication: multi-region async, with LOCAL_QUORUM within a region for durability.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Explain virtual nodes in consistent hashing."**
<details><summary>Model answer</summary>

Each physical server is placed at many points on the ring. This evens out the key distribution, spreads a failed node's load across many peers, and allows weighting by capacity.
</details>

**Q2. "Is Cassandra CP or AP?"**
<details><summary>Model answer</summary>

It's tunable per query. With CL=ONE it behaves AP (always answers, may be stale). With QUORUM reads and writes it gives overlapping quorums, and a partition that prevents a quorum makes those operations fail (choosing C). It's commonly described as AP by design.
</details>

**Q3. "What's the difference between exactly-once delivery and idempotent processing?"**
<details><summary>Model answer</summary>

Exactly-once *delivery* over an unreliable network is generally impossible. Systems deliver at-least-once and make the *processing* idempotent (deduplicating by ID), achieving an **effectively-once** outcome.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to Phase 07: [056 · Sync vs Async](../07-async-messaging/056-sync-vs-async.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [052](052-cap-theorem.md), [053](053-consistency-models.md), [054](054-quorums.md) |

---

⬅️ [055 · Idempotency](055-idempotency.md) · 🗺️ [Phase map](README.md) · ➡️ [056 · Sync vs Async](../07-async-messaging/056-sync-vs-async.md)

✅ Tick **Checkpoint 55%** in [PROGRESS.md](../../PROGRESS.md). 🎉 **Phase 06 complete!** Review the [cheatsheet](CHEATSHEET.md) and [interview bank](INTERVIEW-QUESTIONS.md).
