# 📌 Phase 03 Cheatsheet: Retrieval & Data

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 019 | **Embedding = text → vector.** Same model for queries and docs. Finds candidates, not facts. |
| 020 | **ANN:** HNSW (graph, RAM), IVF (clusters), PQ (compression). **Measure recall@k. Filter-aware search.** |
| 021 | **RAG:** rewrite → retrieve ~50 → rerank ~5 → answer only from sources + cite. **Two failure piles.** |
| 022 | **Chunk by structure**, context headers, metadata, parent-child. **Idempotent, hash-skip pipeline.** |
| 023 | **Hybrid (BM25 + vector) + RRF + cross-encoder rerank.** Recall then precision. |
| 024 | **CDC into the index.** Replace chunk sets. Freshness SLI. **Deletes reach every copy.** |
| 025 | **Filters from the verified identity.** Namespaces or mandatory filters, chunk ACLs, late check. |
| 026 | **Feature store:** one definition, offline (point-in-time) + online (< 5 ms). Watch skew. |
| 027 | **Consent → PII scrub → dedupe → quality → decontaminate → version + lineage.** |
| 028 | **Prompting = now, RAG = what's true, fine-tune = how to behave.** LoRA adapters share a base. |

## 🔢 Numbers to know

```
Embedding call: 10–50 ms · ~$0.02 per 1M tokens · 1M × 1,024-dim float32 ≈ 4 GB
ANN (HNSW): 1–10 ms, recall 95–99% · brute force fine below ~1M vectors
PQ: 16–64× smaller vectors · rerank 50 candidates: 50–150 ms
RAG TTFT budget: rewrite 100 + embed 20 + search 15 + rerank 80 + TTFT 300 ≈ 500 ms
Chunks: ~200–600 tokens, ~10–15% overlap
Training FLOPs ≈ 6 × params × tokens · LoRA adapter ≈ tens–hundreds of MB
```

## 🧮 Formulas

| Formula | Use |
|---|---|
| cos(a, b) = a·b ÷ (‖a‖ ‖b‖) | Similarity |
| Vector memory = N × dims × bytes | Index sizing |
| RRF(d) = Σ 1 ÷ (60 + rank_i(d)) | Hybrid fusion |
| recall@k = relevant found in top k ÷ relevant total | Retrieval quality |
| Freshness = source commit → searchable (p95) | Index lag SLI |

## 🧩 Mnemonics

- **"Map, librarian, exam"**: embeddings, ANN, RAG.
- **"Cut along the layers"**: structural chunking.
- **"Two scouts and a judge"**: hybrid + rerank.
- **"The keycard, not a wish"**: permissions in code.
- **"Binder for facts, training for habits"**: RAG vs fine-tuning.
