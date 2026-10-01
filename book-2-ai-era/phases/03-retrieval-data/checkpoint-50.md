# ✅ Checkpoint 50%: Grounded, Fresh & Private

> ⏱ 25 min · Covers lessons **021–025** · 📈 You're at **50%**
>
> `██████████░░░░░░░░░░` 🎉🎉 **HALFWAY!** You can now design a production RAG system. That alone is a rare skill.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *The chef now reads before it speaks, and it reads only what it's allowed to. Halfway through the story, I want to see you build the same thing.*

---

## ⚡ Part 1: Recall (5 questions)

1. What are RAG's two independent failure piles, and one metric for each?
<details><summary>Reveal Answer</summary>

Retrieval miss (recall@k) and unfaithful generation (faithfulness).
</details>

2. Name three chunking practices that improve retrieval.
<details><summary>Reveal Answer</summary>

Any three of: chunk along document structure, keep logical units (lists, tables) whole, add contextual headers, attach metadata, use parent-child retrieval, use a small overlap.
</details>

3. What do fusion and reranking each fix?
<details><summary>Reveal Answer</summary>

Fusion (hybrid keyword + vector) fixes recall. Reranking (cross-encoder) fixes precision at the top.
</details>

4. Where must a deletion propagate?
<details><summary>Reveal Answer</summary>

Indexes, answer and semantic caches, conversation memories, training datasets, and snapshots (with a deletion log re-applied after restores).
</details>

5. Where must tenant and ACL filters come from?
<details><summary>Reveal Answer</summary>

From the verified identity, injected by code in the retrieval layer, never from the prompt or the model.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 4-minute timer. Explain to an imaginary 12-year-old:

> "How does an AI assistant answer questions about a company's own documents, correctly, without showing anyone something they're not allowed to see?"

Aim to naturally use: **the open-book exam (RAG)**, **cutting the cake along its layers (chunking)**, **two scouts and a judge (hybrid + rerank)**, **printed menus (freshness)**, and **the keycard (permissions)**.

---

## 🛠️ Part 3: Mini-design

**"Ask your contract."** Pantry for Business kitchens upload supplier contracts (PDFs, 5–80 pages, lots of tables). Questions like "what's the late-delivery penalty with our dairy supplier?" Answers must cite the clause. 12,000 tenants, ~40 contracts each.

On paper:
1. Ingestion: parsing, chunking, metadata.
2. Retrieval: isolation and ranking.
3. Two metrics you'd put on a dashboard.

<details><summary>One good answer</summary>

- **Ingestion:** layout-aware PDF parsing (tables kept as Markdown rows), chunks by clause/section with headers ("Dairy Co. contract › §7 Penalties"), metadata (tenant, supplier, contract dates, version), hash-skip re-ingests, a DLQ for unparseable files.
- **Retrieval:** shared index with a mandatory `tenant_id` filter (small tenants), hybrid search (clause numbers and supplier names → BM25), cross-encoder rerank, a late permission check, and an answer that cites clause IDs, or "not found in your contracts".
- **Metrics:** recall@5 on a golden set of contract questions, and faithfulness/citation accuracy (claims supported by the cited clause). Plus cross-tenant leak probes = 0 and freshness p95.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Your RAG answers are wrong 8% of the time. How do you find out why?"**
<details><summary>Model answer</summary>

Label a sample: was the right passage retrieved (top 50? top 5?), and was the answer faithful to it? Split into retrieval misses and generation failures, then fix each pile separately (query rewriting, chunking, hybrid + rerank vs grounding prompts, citations, and verification).
</details>

**Q2. "How do you upgrade the embedding model without downtime?"**
<details><summary>Model answer</summary>

Build a new index version in parallel, backfill with the new model while CDC dual-writes to both, compare recall on the golden set (and A/B test), switch reads via an alias, keep the old index for rollback, then retire it.
</details>

**Q3. "A user lost access to a document an hour ago. Can your assistant still quote it?"**
<details><summary>Model answer</summary>

Not if ACL changes stream into the index quickly (revocations prioritized), caches are scoped by permission set, and a late binding check verifies access to final chunks against the source before prompting.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [026 · Feature Stores](026-feature-stores.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [021](021-rag-architecture.md) and [025](025-permission-aware-retrieval.md), then retry tomorrow. |

---

⬅️ [025 · Permission-Aware Retrieval](025-permission-aware-retrieval.md) · 🗺️ [Phase map](README.md) · ➡️ [026 · Feature Stores](026-feature-stores.md)

✅ Tick **Checkpoint 50%** in [PROGRESS.md](../../PROGRESS.md). 🎉🎉
