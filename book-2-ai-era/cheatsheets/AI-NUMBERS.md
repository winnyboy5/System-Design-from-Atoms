# 🔢 AI Numbers Worth Memorizing

> Order-of-magnitude numbers for AI system design (2026). Prices are illustrative, so check your providers. **Shapes and ratios matter more than exact values.**

## 📝 Tokens

| Fact | Number | Lesson |
|---|---|---|
| Characters per token (English) | ~4 | [003](../phases/01-ai-foundations/003-tokens-and-context-windows.md) |
| Words per token (English) | ~0.75 | 003 |
| Human reading speed | ~4–6 tokens/s | [004](../phases/01-ai-foundations/004-ai-latency-numbers.md) |
| Context windows | 128k–1M+ tokens | 003 |
| Output vs input price | output ~3–5× input | [001](../phases/01-ai-foundations/001-what-changes-in-the-ai-era.md) |
| Cached input discount (providers) | often ~50–90% off | [018](../phases/02-model-serving/018-prompt-and-semantic-caching.md) |
| Batch API discount | often ~50% off | [042](../phases/05-quality-safety-ops/042-cost-engineering.md) |

## ⏱️ Latency

| Operation | Typical | Lesson |
|---|---|---|
| Tokenize | < 1 ms | 004 |
| Embed a query | 10–50 ms | [019](../phases/03-retrieval-data/019-embeddings.md) |
| ANN search (top 50) | 1–20 ms | [020](../phases/03-retrieval-data/020-vector-databases-ann.md) |
| Rerank 50 candidates | 50–150 ms | [023](../phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| TTFT, small model | 100–300 ms | 004 |
| TTFT, large model, long prompt | 0.5–2 s | 004 |
| TPOT | 10–50 ms (20–100 tokens/s) | 004 |
| GPU server cold start (naive) | 5–15 min | [016](../phases/02-model-serving/016-gpu-autoscaling.md) |
| Voice-to-voice target | < ~800 ms | [049](../phases/06-ai-case-studies/049-design-voice-assistant.md) |

## 🖥️ Hardware (H100-class GPU, per GPU)

| Spec | Value | Lesson |
|---|---|---|
| HBM capacity | 80 GB (newer: 141–192+ GB) | [009](../phases/02-model-serving/009-gpus-and-accelerators.md) |
| HBM bandwidth | ~3.35 TB/s | 009 |
| Dense BF16 compute | ~1 PFLOPS (achieved ~40–60%) | [005](../phases/01-ai-foundations/005-gpu-napkin-math.md) |
| NVLink (GPU↔GPU in a server) | ~900 GB/s | [013](../phases/02-model-serving/013-model-parallelism.md) |
| Network between servers | ~50 GB/s per 400 Gb/s link | 013 |

## 🧠 Model memory

| Fact | Value | Lesson |
|---|---|---|
| FP16/BF16 · FP8 · INT4 | 2 · 1 · 0.5 bytes per parameter | [012](../phases/02-model-serving/012-quantization.md) |
| 8B model in FP16 | 16 GB | 005 |
| 70B model in FP16 · FP8 | 140 GB · 70 GB | 005 |
| KV per token, 8B model (GQA) | ~0.13 MB | 005 |
| KV per token, 70B model (GQA) | ~0.33 MB | 005 |
| Contiguous KV waste vs paged | 60–80% vs < 4% | [011](../phases/02-model-serving/011-kv-cache-and-pagedattention.md) |

## 📚 Retrieval

| Fact | Value | Lesson |
|---|---|---|
| Embedding dimensions | 384–3,072 | 019 |
| 1M × 1,024-dim float32 vectors | ~4 GB | 019 |
| PQ compression | 16–64× | 020 |
| HNSW recall | 95–99% at 1–10 ms | 020 |
| Chunk size | ~200–600 tokens, 10–15% overlap | [022](../phases/03-retrieval-data/022-chunking-and-ingestion.md) |
| RRF constant | k ≈ 60 | 023 |

## 📏 Quality and ops

| Fact | Value | Lesson |
|---|---|---|
| Eval noise, 100 cases at ~85% | ± ~7 points (95% CI) | [037](../phases/05-quality-safety-ops/037-offline-evals.md) |
| A/B users per arm (20% base, 1-pt δ) | ~25,600 | [038](../phases/05-quality-safety-ops/038-online-evals.md) |
| Judge–human agreement to trust | κ ≳ 0.6–0.7 | 038 |
| Trace metadata per request | ~1–2 KB | [039](../phases/05-quality-safety-ops/039-llm-observability.md) |
| Self-host break-even utilization | often ~25–40% | 042 |
