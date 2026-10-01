# 🎤 Phase 03 Interview Question Bank: Retrieval & Data

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 5 min.

---

### 🟢 1. What is RAG and why use it? · [021]
<details><summary>Model answer</summary>

Retrieve relevant passages from your data and have the model answer from them with citations. It grounds answers in current, private, citable facts without retraining.
</details>

### 🟢 2. What does recall@k measure? · [020, 023]
<details><summary>Model answer</summary>

The share of relevant items that appear in the top k results. For RAG, whether the needed passage reached the shortlist or prompt.
</details>

### 🟢 3. Why combine keyword and vector search? · [023]
<details><summary>Model answer</summary>

Keyword search nails exact terms and IDs. Vector search handles paraphrase and meaning. Together (fused by rank) they get higher recall than either alone.
</details>

### 🟢 4. RAG or fine-tuning for a frequently changing product catalogue? · [028]
<details><summary>Model answer</summary>

RAG: facts change, must be current and citable. Fine-tuning is for behaviour and format.
</details>

### 🟡 5. How do you choose a chunking strategy? · [022]
<details><summary>Model answer</summary>

Split along document structure, keep logical units whole, add context headers and metadata, and test sizes and parent-child retrieval against a golden set by recall@k. Boundaries matter more than size.
</details>

### 🟡 6. How do you keep a vector index consistent with a source database? · [024]
<details><summary>Model answer</summary>

CDC/outbox → stream → idempotent indexer that re-chunks and re-embeds changed docs, replaces chunk sets by doc and version, deletes leftovers, and tracks a freshness SLI. Blue/green for model upgrades.
</details>

### 🟡 7. How do you stop cross-tenant leaks in a multi-tenant RAG product? · [025]
<details><summary>Model answer</summary>

Tenant and ACL filters from the verified identity, enforced in filter-aware search (or namespaces), late permission checks on final chunks, permission-scoped caches and logs, and automated cross-tenant probe tests.
</details>

### 🟡 8. What is training/serving skew and how do you prevent it? · [026]
<details><summary>Model answer</summary>

Features computed differently in training vs production. Prevent it with one feature definition feeding both stores, point-in-time joins, and logging served features for training and drift checks.
</details>

### 🔴 9. Design a RAG assistant over 100M documents for 10,000 enterprise tenants. · [019–025]
<details><summary>Model answer</summary>

- **Ingestion:** connectors with CDC, layout-aware parsing, structural chunking with headers, ACL metadata, and hash-skip batch embedding.
- **Index:** hybrid (BM25 + ANN), namespaces for large tenants, mandatory tenant filters for the long tail, sharded and replicated, with PQ or disk-based ANN for scale.
- **Query path:** rewrite → filter-aware hybrid top 50 → rerank → late ACL check → grounded generation with citations → citation verification.
- **Operations:** freshness and deletion SLOs, a deletion log, blue/green upgrades.
- **Evals:** recall@k and faithfulness per tenant tier, plus leak probes.
</details>

### 🔴 10. Your fine-tuned model scored 94% offline but users say it's worse. Investigate. · [026, 027]
<details><summary>Model answer</summary>

Suspect contamination (eval items in training), leakage, or skew. Check dataset lineage for eval near-duplicates, rebuild with decontamination and time-based splits, compare on a fresh holdout from recent traffic, check PII/memorization probes, and verify the serving prompt and features match training.
</details>
