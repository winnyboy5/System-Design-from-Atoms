# 045 · How to Choose a Database

> ⏱ 8 min · 📈 45% · 🅰️ Part A (core) · Phase 05: Databases
>
> `█████████░░░░░░░░░░░` 45% of the whole guide

---

## 📖 Story

Maya pins Pantry's architecture diagram to the wall and steps back. It looks like a subway map of a city that grew too fast.

**Postgres. Redis. S3. OpenSearch. Cassandra. A warehouse. A TSDB.** Seven databases. Seven things that can page someone at 3 a.m. Seven sets of backups, upgrades, and failure modes.

At her design review, a reviewer taps the diagram with a pen and asks one quiet question:

*"Why is each of these here?"*

For four of them, Maya has crisp answers. For the other three she hears herself say "it seemed like the right tool", and feels the ground shift under her feet.

I want you never to be caught in that silence. Here's a **repeatable way to choose**, so you never pick a database on hype again.

## 🎯 One-sentence idea

**Choose a database by answering five questions (data shape, access patterns, consistency needs, scale and latency, and what your team can operate), default to boring, proven choices, and add a specialist only for a need the default can't meet.**

## 🧸 Analogy

Choosing a **vehicle**:

- Moving furniture → a **van** (object storage).
- The daily commute, where safety matters → a **reliable family car** (Postgres).
- 10,000 parcels a day on fixed routes → a **truck fleet** (Cassandra/DynamoDB).
- Racing → a **sports car** (Redis: blazing, but not for moving house).

No vehicle is "best." **The job decides.**

## 🖼️ Visual

*Diagram brief:* a flowchart that starts from the default (Postgres) and adds one specialist only when a specific yes/no question forces it.

```mermaid
flowchart TD
    S["Start: Postgres/MySQL<br/>(the default)"] --> Q1{"Blobs/files?"}
    Q1 -->|"yes"| O["+ Object storage"]
    Q1 -->|"no"| Q2{"Hot reads need<br/>under 1 ms?"}
    Q2 -->|"yes"| C["+ Redis cache"]
    Q2 -->|"no"| Q3{"Full-text search?"}
    Q3 -->|"yes"| E["+ Search engine"]
    Q3 -->|"no"| Q4{"Writes exceed one<br/>node and access is by key?"}
    Q4 -->|"yes"| W["Shard it, or<br/>Cassandra/DynamoDB"]
    Q4 -->|"no"| Q5{"Analytics over<br/>history?"}
    Q5 -->|"yes"| DW["+ Warehouse"]
    Q5 -->|"no"| DONE["✅ Keep it simple"]
```

## 🔬 How it works

- **1. Shape:** relational entities, documents, key → blob, time series, graph, or files?
- **2. Access patterns:** by PK, ranges, ad-hoc filters, full-text, aggregations, traversals? What's the read:write ratio, and the QPS of each?
- **3. Consistency:** are multi-row atomic invariants needed? Must reads be strongly consistent, or is eventual fine?
- **4. Scale and latency:** QPS, data size and growth, p99 targets, single- or multi-region?
- **5. Team and ops:** managed vs self-hosted, existing expertise, cost, tooling. **Every extra database is permanent on-call.** The red flags: hype-driven picks, NoSQL for relational ad-hoc workloads, more databases than engineers, and caches or search treated as the source of truth.

## 🧩 Worked example

**Maya's review, redone with the five questions:**

| Data | Shape / access | Choice | Justified by |
|---|---|---|---|
| Users, dishes, orders, payments | Relational, transactions | **Postgres** | Integrity and money |
| Sessions, carts, hot pages | KV, < 1 ms | **Redis** | Latency + TTL |
| Videos, photos | Blobs | **S3 + CDN** | Cost + durability |
| Dish search | Text + geo + facets | **OpenSearch** | Relevance |
| Chat (2B+ rows) | Partition + time range, write-heavy | **Cassandra** | Write scale |
| Analytics | Big scans | **Warehouse** | Isolation from prod |
| Fridge metrics | Time series | ~~TSDB~~ → **TimescaleDB on Postgres** | 60k series fits an extension. One fewer system |

Seven became **six**, and every survivor has a one-line justification. A 3-person startup would begin with **Postgres + Redis + S3** and add the rest only when a trigger fires.

## ⚖️ Trade-offs

| Maya's choice | What she gains | What she pays |
|---|---|---|
| One general DB | Simplicity, one thing to run | Hits limits on special workloads |
| Polyglot persistence | The best tool per job | Ops burden and cross-store sync |
| Managed services | Less ops | Cost and lock-in |
| Self-hosted | Control, cost at scale | On-call and expertise |

## 🌍 Real world

- **Uber** has used MySQL (Schemaless), Cassandra, Redis, and Kafka, each for distinct needs.
- **Instagram** grew on Postgres + Redis + Memcached, and added Cassandra for specific high-write features.
- Many successful startups run **only Postgres + Redis** for years.

## 📌 Cheat card

> - **Five questions: shape · access · consistency · scale · team.**
> - **Default: Postgres + Redis + S3.** Add the others **for a named reason**.
> - **One source of truth.** Caches and indexes are **derived**.
> - **Write down the trigger** that would make you change your choice.
> - Full chooser: [DATABASE-CHOOSER.md](../../cheatsheets/DATABASE-CHOOSER.md)

## 🧪 Feynman check

Use the vehicles to explain why a food app might run Postgres, Redis, *and* S3, and why that's not over-engineering.

⚠️ **Common confusion:** "Pick the database that scales to a billion users." Pick what fits **today's** requirements with a **clear, written growth path**. Premature scale choices slow every feature you ship until the day the scale actually arrives, if it ever does.

## ⚡ Quick recall

1. What are the five questions for choosing a database?
<details><summary>Reveal Answer</summary>

Data shape, access patterns, consistency/transactions, scale/latency, and team/operations.
</details>

2. What's a sensible default stack for a new product?
<details><summary>Reveal Answer</summary>

A relational DB (Postgres/MySQL) + Redis for caching + object storage for files.
</details>

3. Why is polyglot persistence a trade-off?
<details><summary>Reveal Answer</summary>

You get the best tool per job but pay in operational burden and in keeping data synchronized across stores.
</details>

## 🎤 Interview practice

**Q. "Design the storage layer for a URL shortener: 100M new links/day, 10B redirects/day. Justify every database you add."**
<details><summary>Model answer</summary>

- **Numbers first:**
  - Writes ≈ 100M ÷ 10⁵ ≈ **1,200/s**.
  - Reads ≈ 10B ÷ 10⁵ ≈ **115k/s** (peak ~300k/s).
  - **~100:1 read-heavy**, pure **key → value** (`short → long`).
- **Primary store:**
  - ~500 B × 100M/day × 365 × 5 ≈ **~90 TB** over 5 years → a horizontally scalable **KV store** (DynamoDB/Cassandra), or Postgres **sharded by short code**.
  - Justified by: simple key access at a size and write rate beyond one node.
- **Cache:** Redis plus the **CDN edge**. Clicks follow a power law, so a small hot set serves the large majority of redirects, and most never touch the DB. Justified by: 300k/s at ~1 ms.
- **Analytics:** click events → **Kafka** → **warehouse**. Justified by: scans must never hit the redirect path.
- **What I'm *not* adding:** search (no text queries) and a graph DB (no relationships).
- **Change triggers:** multi-region active-active redirects → a globally replicated KV store. Ad-hoc link management queries → a small Postgres metadata store alongside it.
- **Likely follow-up:** "How do you generate unique short codes?" → a counter + base62 via a range-allocating ID service, or a pre-generated key pool (lessons 072, 074).
</details>

## 📖 Teaser

> 📖 *Chapter 6 is next. Pantry launches nationwide, and for the first time, one database machine is simply not enough.*

---

⬅️ [044 · Specialized Databases](044-specialized-databases.md) · 🗺️ [Phase map](README.md) · ➡️ [✅ Checkpoint 45%](checkpoint-45.md)

✅ **Safe stopping point.** Tick lesson 045 in [PROGRESS.md](../../PROGRESS.md), then do the checkpoint!
