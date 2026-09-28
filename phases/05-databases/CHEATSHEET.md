# 📌 Phase 05 Cheatsheet: Databases

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 034 | **SQL** = schema + joins + ACID (the default). **NoSQL** = flexible + horizontal + model-by-query. |
| 035 | **ACID** = Atomic, Consistent, Isolated, Durable. The WAL provides A + D. |
| 036 | Isolation: **RU → RC → RR/Snapshot → Serializable**. Watch for lost updates and write skew. |
| 037 | **B-tree index** = the book's index. **Leftmost prefix rule.** Every index slows writes. |
| 038 | **KV** (coat check) · **Document** (folder) · **Wide-column** (partition + sorted rows) · **Graph** (relationships). |
| 039 | **Normalize the source of truth, denormalize the read paths.** Snapshots on purpose. |
| 040 | **List the queries first.** Kill **N+1**. **Pool connections** (Little's Law sizing). |
| 041 | **OLTP** (row, small transactions) vs **OLAP** (column, big scans). ETL/ELT into a warehouse. |
| 042 | **Object storage** for blobs, metadata in a DB, **pre-signed URLs**, CDN in front. |
| 043 | **Inverted index**: term → docs, **BM25** ranking. Search is **derived**, synced via CDC. |
| 044 | Specialists: **TSDB, geo, vector, ledger, coordination (etcd)**. Mind the cardinality. |
| 045 | Five questions: **shape, access, consistency, scale, team.** Default **Postgres + Redis + S3**. |

## 🔢 Numbers

```
B-tree depth for millions of rows: 3–4 levels
Postgres/MySQL: ~5k–50k simple reads/s, ~1k–10k+ durable writes/s per node
Comfortable single DB node: ~1–10 TB
Object storage durability: 11 nines · ~$0.02/GB-month (standard, illustrative)
Columnar compression: 5–10×+ · Elasticsearch refresh ≈ 1 s
Pool size ≈ QPS × query time × 2
```

## 🪄 Tricks & mnemonics

- **ACID:** "**A**ll **C**hanges **I**n **D**atabase."
- **Isolation levels:** "**R**eally **R**eally **R**ead **S**afely."
- **Composite index order:** equality columns first, then range/sort.
- **Counter updates:** `SET x = x + 1`, never read-modify-write in app code.
- **Optimistic locking:** `WHERE id=? AND version=?`.
- **Wide-column keys:** `PRIMARY KEY ((partition), clustering)`, with **time buckets** to bound partitions.
- **Invoices keep snapshots.** Don't "fix" historical data by normalizing it.

## ⚠️ Top mistakes

- Choosing NoSQL for hype, then needing joins and transactions.
- Read-modify-write races at the default isolation level.
- Indexing everything (slow writes) or nothing (full scans).
- ORM N+1 loops.
- BLOBs in the relational DB. Analytics on the OLTP primary.
- Treating search or cache as the source of truth.

---

🗺️ [Phase map](README.md) · 🎤 [Interview questions](INTERVIEW-QUESTIONS.md) · 🗄️ [Database chooser](../../cheatsheets/DATABASE-CHOOSER.md)
