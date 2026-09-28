# ✅ Checkpoint 35%: Caches Meet Databases

> ⏱ 15 min · Covers lessons **031–035** · 📈 You're at **35%**
>
> `███████░░░░░░░░░░░░░` Over a third! You now understand how data gets stored safely *and* served fast.

**Rules:** answer out loud or on paper **before** opening answers.

---

## ⚡ Part 1: Recall (5 questions)

1. Name three approaches to cache invalidation.
<details><summary>Answer</summary>

TTL expiry, explicit delete on write (often event/CDC-driven), versioned keys or URLs.
</details>

2. What's the fix for a cache stampede on a hot key?
<details><summary>Answer</summary>

Request coalescing / single-flight (one fetcher), serve-stale-while-revalidate, early probabilistic refresh, TTL jitter.
</details>

3. Why does Redis Cluster failover risk losing a few writes?
<details><summary>Answer</summary>

Replication is asynchronous. The promoted replica may not have received the latest writes.
</details>

4. When would you choose SQL over NoSQL?
<details><summary>Answer</summary>

When you need relationships/joins, ad-hoc queries, multi-row transactions, and strict integrity. It's the default for most apps.
</details>

5. Expand ACID with one sentence each.
<details><summary>Answer</summary>

Atomic: all or nothing. Consistent: constraints hold. Isolated: concurrent transactions don't see partial work. Durable: committed data survives crashes.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "When I buy the last concert ticket online at the same moment as someone else, how does the website make sure only one of us gets it?"

Must include: **transaction, atomic, isolation/locking, commit/rollback**.

---

## 🛠️ Part 3: Mini-design

**A library book-lending app.** Members borrow books (max 5 at a time). Each book copy can be lent to only one member. The catalog is read 1,000×/s, and loans happen 5×/s.

Decide: SQL or NoSQL, the main tables, how to enforce the rules, and what to cache.

<details><summary>One good answer</summary>

- **SQL (Postgres):** strong integrity rules, low write volume.
- Tables: `members`, `books`, `copies(id, book_id, status)`, `loans(id, member_id, copy_id, due, returned_at)`.
- Rules: a transaction that locks the copy row (`FOR UPDATE`), checks `status='available'` and that the member's active loans are < 5, inserts the loan, and updates the status. A partial unique index to guarantee one active loan per copy: `UNIQUE (copy_id) WHERE returned_at IS NULL`.
- Cache: catalog and book details in Redis (cache-aside, TTL), and availability counts with a short TTL. Loans always go to the DB.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "How do you keep a cache consistent with a database?"**
<details><summary>Model answer</summary>

Cache-aside with delete-on-write, TTLs as a safety net, CDC/outbox-driven invalidation for multiple caches, awareness of races (leases, versioned sets), and reads from the source of truth for critical paths. Consistency is eventual and bounded, not perfect.
</details>

**Q2. "What makes a transaction durable?"**
<details><summary>Model answer</summary>

The commit is acknowledged only after the change is recorded in the write-ahead log on stable storage (fsync), and often also replicated. On a crash, the log is replayed.
</details>

**Q3. "Can NoSQL databases be ACID?"**
<details><summary>Model answer</summary>

Many now support ACID at some scope: single-document/item atomicity everywhere, and multi-document/multi-item transactions in MongoDB, DynamoDB, and FoundationDB, often with limits and performance costs. NewSQL systems offer full distributed ACID.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [036 · Transactions & Isolation Levels](036-transactions-and-isolation.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [031](../04-caching/031-cache-invalidation.md), [032](../04-caching/032-cache-stampede-and-hot-keys.md), [035](035-acid.md) |

---

⬅️ [035 · ACID](035-acid.md) · 🗺️ [Phase map](README.md) · ➡️ [036 · Transactions & Isolation Levels](036-transactions-and-isolation.md)

✅ Tick **Checkpoint 35%** in [PROGRESS.md](../../PROGRESS.md). 🎉
