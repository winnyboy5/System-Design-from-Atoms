# ✅ Checkpoint 45%: The Data Toolbox

> ⏱ 15 min · Covers lessons **041–045** · 📈 You're at **45%**
>
> `█████████░░░░░░░░░░░` Nearly halfway through the whole guide. You now know *which* store to use for *what*.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Maya can justify every database on the diagram. Before Pantry goes national, I want to make sure you can too.*

---

## ⚡ Part 1: Recall (5 questions)

1. Why shouldn't you run big analytics queries on the production OLTP database?
<details><summary>Answer</summary>

They scan huge amounts of data, compete for CPU, I/O, and cache with live traffic, and row stores are inefficient for aggregations. Use a columnar warehouse instead.
</details>

2. What's the upload pattern that keeps file bytes off your app servers?
<details><summary>Answer</summary>

Pre-signed URLs: the API issues a time-limited URL, and the client uploads directly to object storage.
</details>

3. Explain the inverted index in one sentence.
<details><summary>Answer</summary>

A map from each term to the list of documents containing it, so searches look up and intersect lists instead of scanning documents.
</details>

4. What's the "cardinality trap" in time-series databases?
<details><summary>Answer</summary>

Adding unbounded labels (like user IDs) multiplies the number of series, exploding memory and slowing queries.
</details>

5. Name the five questions for choosing a database.
<details><summary>Answer</summary>

Data shape, access patterns, consistency needs, scale/latency, team/operations.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "Why does a big app like YouTube use lots of different kinds of databases instead of just one?"

Must include: **source of truth, object storage for videos, cache, search index, warehouse for analytics**.

---

## 🛠️ Part 3: Mini-design

**A fitness-tracking app.** Users log workouts (structured), upload progress photos, and wearables send heart rate every second while exercising. Users search workouts by name. The company wants weekly engagement reports.

Choose storage for each kind of data and justify it in one line each.

<details><summary>One good answer</summary>

- Users, workouts, plans → **Postgres** (relational, integrity).
- Progress photos → **S3 + CDN** (blobs), metadata in Postgres, pre-signed uploads.
- Heart-rate stream → a **time-series DB** (TimescaleDB/InfluxDB) with downsampling (1 s → 1 min after 30 days). Labels: user and device only if cardinality is OK (or store per-user series in a wide-column store).
- Workout search → Postgres full-text at first, then **Elasticsearch** when it's needed.
- Weekly reports → CDC/ETL into a **warehouse**.
- Cache → **Redis** for profiles and today's summary.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "How do you keep Elasticsearch in sync with your primary DB?"**
<details><summary>Model answer</summary>

CDC (Debezium) or a transactional outbox → Kafka → indexer that does idempotent upserts and deletes by ID. Monitor lag, support full reindexing into a new index behind an alias, and treat search as eventually consistent.
</details>

**Q2. "Row store vs column store: explain with an example."**
<details><summary>Model answer</summary>

Row stores keep each record together, so "get order 42" is one read, which suits OLTP. Column stores keep each column together, so "SUM(revenue) by region" reads 2 of 50 columns, compresses well, and suits OLAP.
</details>

**Q3. "Where would you store user-uploaded videos and their metadata?"**
<details><summary>Model answer</summary>

The video files (plus transcoded renditions) go in object storage behind a CDN. Metadata (owner, title, status, duration, rendition keys) goes in a database. Uploads are multipart via pre-signed URLs, and a processing pipeline is triggered by object-created events.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to Phase 06: [046 · Leader-Follower Replication](../06-scaling-data/046-leader-follower-replication.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [041](041-oltp-vs-olap.md), [043](043-search-and-inverted-index.md), [045](045-choosing-a-database.md) |

---

⬅️ [045 · Choosing a Database](045-choosing-a-database.md) · 🗺️ [Phase map](README.md) · ➡️ [046 · Leader-Follower Replication](../06-scaling-data/046-leader-follower-replication.md)

✅ Tick **Checkpoint 45%** in [PROGRESS.md](../../PROGRESS.md). 🎉 **Phase 05 complete!** Review the [cheatsheet](CHEATSHEET.md) and [interview bank](INTERVIEW-QUESTIONS.md).
