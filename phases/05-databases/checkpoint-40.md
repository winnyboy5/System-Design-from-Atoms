# ✅ Checkpoint 40%: 🎉 Level-Up! Data Modelling Muscles

> ⏱ 15 min · Covers lessons **036–040** · 📈 You're at **40%**
>
> `████████░░░░░░░░░░░░` 🎉 **40%! Half of the core 80% is done.** You can now reason about transactions, indexes, and data models like a backend engineer.

**Rules:** answer out loud or on paper **before** opening answers.

---

## ⚡ Part 1: Recall (5 questions)

1. Which anomaly can happen at Read Committed when two transactions do read-modify-write on the same row?
<details><summary>Answer</summary>

A lost update.
</details>

2. Index `(user_id, created_at)`: can it serve `WHERE created_at > X` alone?
<details><summary>Answer</summary>

Generally no, because of the leftmost prefix rule.
</details>

3. Which NoSQL family fits "latest 50 messages in a chat" at billions of messages?
<details><summary>Answer</summary>

Wide-column (partition by chat + time bucket, clustered by timestamp descending).
</details>

4. Give one piece of data that should be denormalized as a snapshot.
<details><summary>Answer</summary>

The price or shipping address on an order/invoice.
</details>

5. How do you fix an N+1 query?
<details><summary>Answer</summary>

A JOIN, a batched `WHERE id IN (...)`, ORM eager loading, or DataLoader.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "Why is looking something up in a huge database fast, and why does adding more 'lookup shortcuts' make saving new stuff slower?"

Must include: **index, B-tree levels, full scan, write cost**.

---

## 🛠️ Part 3: Mini-design

**A recipe-sharing app.** Access patterns:
1. Get a recipe with its ingredients (20k/s)
2. List a user's recipes, newest first (2k/s)
3. Recipe like counts shown everywhere (20k/s reads, 500 likes/s)
4. "Find recipes with chicken and garlic" (500/s)

Design the tables/stores, indexes, and any denormalization.

<details><summary>One good answer</summary>

- **Postgres** source of truth: `recipes(id, author_id, title, body, like_count, created_at)`, `ingredients`, `recipe_ingredients(recipe_id, ingredient_id)`, `likes(user_id, recipe_id, PK(user_id, recipe_id))`.
- (1) PK lookup + one join or a JSONB ingredients array. Cache in Redis.
- (2) Index `(author_id, created_at DESC)` + cursor pagination.
- (3) A **denormalized `like_count`** updated in the same transaction as the like insert (or via Redis counters + a flush for hot recipes).
- (4) **Elasticsearch** (inverted index on ingredients), fed by CDC/events (lesson 043).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Explain optimistic vs pessimistic locking and when to use each."**
<details><summary>Model answer</summary>

Pessimistic: lock the row up front (`SELECT ... FOR UPDATE`). Good under high contention on a few rows, but it risks blocking and deadlocks. Optimistic: read a version, and update only if it's unchanged, retrying on a conflict. Good for low contention and long user "think time" (editing forms).
</details>

**Q2. "How do you choose which indexes to create?"**
<details><summary>Model answer</summary>

Start from the hot queries (frequency × cost from query stats). Create composite indexes matching the equality filters, then the range or sort columns. Consider covering indexes. Verify with EXPLAIN ANALYZE. Monitor the write overhead, and drop unused indexes.
</details>

**Q3. "When would you pick a document DB over a relational one?"**
<details><summary>Model answer</summary>

When data is naturally aggregate-shaped and read as a whole (a profile, a catalog item with varied attributes), the schema varies or evolves often, and cross-entity transactions and joins are rare. Otherwise, relational (possibly with JSONB) is a safer default.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [041 · OLTP vs OLAP](041-oltp-vs-olap.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [036](036-transactions-and-isolation.md), [037](037-indexes.md), [038](038-nosql-families.md) |

🏆 **Level-up reward:** 40%! Celebrate properly. Walk, snack, or game. Your brain consolidates while you rest.

---

⬅️ [040 · Access Patterns & Pooling](040-access-patterns-and-pooling.md) · 🗺️ [Phase map](README.md) · ➡️ [041 · OLTP vs OLAP](041-oltp-vs-olap.md)

✅ Tick **Checkpoint 40%** in [PROGRESS.md](../../PROGRESS.md). 🎉
