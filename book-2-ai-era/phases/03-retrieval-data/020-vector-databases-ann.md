# 020 · Vector Databases & ANN Search

> ⏱ 13 min · 📈 40% · 🅰️ AI-era core · Phase 03: Retrieval & Data for AI
>
> `████████░░░░░░░░░░░░` 40% of Book 2
>
> 🧬 **Atoms used:** indexes [B1·037] · sharding [B1·049] · replication [B1·046] · specialized databases [B1·044] · [019]

---

## 📖 Story

Pantry's flavour map grows from 2 million recipes to **50 million** vectors: recipes, menu items, reviews, and cook bios.

Maya's search does the honest thing: for every query, compute the similarity to **every single vector**. That's 50M × 1,024 multiplications: **51 billion** per query. On a CPU, each search takes **4 seconds**. At dinner time, 3,000 searches arrive every second.

She moves the vectors into a vector database with an **approximate** index. Searches drop to **6 ms**. Then a vegan customer in Lisbon asks for "**vegan comfort food near me**", and gets **zero results**.

The index found the 100 nearest dishes on the whole map, then filtered for "vegan" and "Lisbon". **None of the 100** survived both filters. Lisbon's vegan stews were the 400th-nearest, so the index never looked at them.

I told Maya that a vector index is like a **librarian who's fast because she doesn't check every shelf**. That's the deal: speed in exchange for occasionally missing a book. You have to know **which books she skips**, and **how to tell her about your filters before she starts walking**. Let me show you.

## 🎯 One-sentence idea

**A vector database stores embeddings in an approximate-nearest-neighbour index (graph-based HNSW, cluster-based IVF, or compressed PQ) that finds the closest vectors in milliseconds by checking only a small fraction of them, trading a little recall for huge speed, and its hard parts are tuning that trade-off, filtering by metadata, and scaling memory.**

## 🧸 Analogy

A **librarian finding the books most like yours**:

- **Brute force:** compare your book to **every** book in the building. Perfect, and hopelessly slow.
- **HNSW:** the librarian follows a **web of "see also" signs**: big jumps between distant sections first, then smaller steps between neighbouring shelves. Very fast, very accurate, and the signs take space.
- **IVF:** books are grouped into **themed rooms**. She only searches the few rooms closest to your topic.
- **PQ:** she keeps a **pocket-sized summary card** for each book instead of the book itself: tiny, slightly blurry.
- **Filters:** tell her "**vegan, Lisbon only**" **before** she starts walking, not after she's picked 100 books.

## 🖼️ Visual

*Diagram brief:* an HNSW graph drawn in three layers. The top layer has a few points with long links, the middle has more points, and the bottom has all points with short links. A query's search path drops from a long hop at the top to short hops at the bottom, ending at its nearest neighbours.

```
Layer 2 (few nodes, long links)     A ─────────────── B
                                    │                 │
Layer 1 (more nodes)                A ──── C ──── D ── B
                                           │
Layer 0 (all 50M nodes, short links) … C ─ x ─ y ─ ★ ─ z …
                                                   ↑ query lands here after
                                                     ~log(N) hops, checking
                                                     ~0.01% of all vectors
```

## 🔬 How it works

- **Exact search doesn't scale:** brute force is **O(N × d)** per query. Fine for < ~1M vectors or offline jobs, hopeless for 50M at 3,000 QPS.
- **HNSW (graphs):** a multi-layer "small world" graph navigated greedily from coarse to fine. **High recall (95–99%) at ~1–10 ms**, tunable with `ef_search`. Memory-hungry: raw vectors + links, usually **in RAM**.
- **IVF and PQ (clusters and compression):** IVF clusters vectors and searches only the **nprobe** nearest clusters. **Product quantization** compresses each vector to ~32–128 bytes (**16–64× smaller**), with some recall loss, often fixed by **re-scoring** the top candidates with full vectors. Disk-based indexes (DiskANN-style) keep the graph on SSD for billion-scale data.
- **Filtering:** **post-filtering** (search, then filter) can return nothing when filters are selective. Prefer **filter-aware search** (the index skips non-matching nodes while walking), **partitioning** by a common filter (region, tenant), or pre-filtering to a small candidate set and brute-forcing it.
- **Operating it:** measure **recall@k** against brute force on a sample, plus p99 latency. Shard by ID or by partition, replicate for QPS and availability (Book 1 lessons 046, 049), and plan for **updates and deletes** (graph indexes degrade with heavy churn and need periodic rebuilds).
- **Where to put it:** a **Postgres extension** (pgvector) for millions of vectors next to your relational data, a **search engine** with vector support for hybrid search, or a **dedicated vector database** at large scale. Or a library (FAISS) inside your own service.

## 🧩 Worked example

**Sizing 50M vectors, 1,024 dims:**

```
Raw float32:    50M × 1,024 × 4 B       = 205 GB
Raw float16:                             = 102 GB
HNSW links:     50M × 32 links × 4 B    ≈ 6.4 GB
PQ (64 B/vec):  50M × 64 B              = 3.2 GB  (+ full vectors on SSD for re-scoring)

Option A: HNSW, float16, 4 shards × 3 replicas → ~27 GB per node, p99 ~5 ms, recall@10 ≈ 0.97
Option B: IVF-PQ, in RAM → ~4 GB per node, p99 ~8 ms, recall@10 ≈ 0.90 (≈ 0.96 with re-scoring)
```

**The Lisbon bug, fixed:**

| Approach | "vegan comfort food near me" in Lisbon |
|---|---|
| Global top-100, then filter | 0 results ❌ |
| Partition by region (one index per country), filter-aware on `vegan` | 23 results in **4 ms** ✅ |

## ⚖️ Trade-offs

| Choice | You gain | You pay |
|---|---|---|
| HNSW | High recall, low latency | RAM-hungry, slower inserts, churn degrades it |
| IVF-PQ | Tiny memory footprint | Lower recall unless you re-score |
| Disk-based ANN | Billion-scale on SSDs | Higher latency |
| Higher `ef_search` / `nprobe` | Better recall | Slower queries |
| Partition by filter | Correct filtered results | Uneven partition sizes |

## 🌍 Real world

- **FAISS** (Meta) popularized IVF and PQ. **HNSW** (Malkov & Yashunin) is the default index in many vector databases. **DiskANN** (Microsoft) brought billion-scale ANN to SSDs.
- **pgvector** brings HNSW and IVF indexes to Postgres. Elasticsearch and OpenSearch combine vector and keyword search. Dedicated vector databases add sharding, filtering, and managed operations.
- Production teams report **recall@k** alongside latency, because "fast but wrong" is invisible otherwise.

## 📌 Cheat card

> - **Brute force ≤ ~1M vectors.** Beyond that, **ANN**.
> - **HNSW:** graph, high recall, RAM-hungry. **IVF:** clusters + nprobe. **PQ:** 16–64× smaller.
> - **Measure recall@k vs brute force**, not just latency.
> - **Post-filtering can return nothing.** Use filter-aware search or partitions.
> - **Shard + replicate. Rebuild after heavy churn.**

## 🧪 Feynman check

Explain the librarian: why following "see also" signs is fast but can miss a book, and why your filters must be told before she starts walking.

⚠️ **Common confusion:** "The vector database returns the nearest neighbours." It returns **approximately** the nearest, with recall that depends on tuning, and silently less under heavy filtering or churn. If you never measure recall, you'll never notice the missing books.

## ⚡ Quick recall

1. Why use approximate nearest-neighbour search?
<details><summary>Reveal Answer</summary>

Exact search compares the query with every vector (O(N × d)), which is far too slow at tens of millions of vectors and thousands of queries per second.
</details>

2. What does product quantization trade?
<details><summary>Reveal Answer</summary>

It compresses vectors 16–64×, saving memory, at the cost of some recall (often recovered by re-scoring top candidates with full vectors).
</details>

3. Why can post-filtering return zero results?
<details><summary>Reveal Answer</summary>

The index retrieves the top-k nearest overall, and if few of them match a selective filter, nothing survives.
</details>

## 🎤 Interview practice

**Q. "Design a vector search service for 1 billion image embeddings (768 dims) at 10,000 QPS, with p99 under 50 ms and filters on category and region."**
<details><summary>Model answer</summary>

- **Size:** 1B × 768 × 2 B (float16) ≈ **1.5 TB** raw → too big for all-RAM HNSW economically.
- **Index:** IVF-PQ in RAM (1B × 64 B = **64 GB** of codes) or a disk-based graph index on NVMe, with **re-scoring** the top ~200 candidates using full vectors from SSD.
- **Partitioning:** shard by region (the most common filter), then by ID hash within large regions, each shard ~50–100M vectors. Category handled by **filter-aware search** with bitmap filters.
- **Query path:** a router fans out to the shards for the region, each returns its top-k, and the router merges them. Replicate shards **×3** for QPS and availability.
- **Throughput:** 10k QPS × fan-out → size the replicas from per-shard benchmark QPS at the target recall.
- **Freshness:** a streaming insert path into small in-memory segments, merged into the main index periodically. Deletes via tombstones, with rebuilds off-peak.
- **Quality:** measure **recall@k** on a held-out sample against brute force, per shard, and alert on drops.
- **Likely follow-up:** "How do you cut cost further?" → lower dimensions, int8 vectors, cache popular queries, and a tiered index (hot items in RAM, the long tail on disk).
</details>

## 📖 Teaser

> 📖 *Search now finds the real cardamom bun in four milliseconds, and the model still has to read it, believe it, and quote it, instead of improvising another lamb curry.*

---

⬅️ [019 · Embeddings](019-embeddings.md) · 🗺️ [Phase map](README.md) · ➡️ [✅ Checkpoint 40%](checkpoint-40.md)

✅ **Safe stopping point.** Tick lesson 020 in [PROGRESS.md](../../PROGRESS.md), then do the checkpoint!
