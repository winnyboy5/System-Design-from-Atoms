# 🗺️ Coverage Map (Book 2)

> Every AI-era system design topic, mapped to the lesson that teaches it. Use it to turn a gap into a lesson to reread.
> For classic topics (caching, sharding, queues, consensus…), see [Book 1's coverage map](../COVERAGE.md).

## Foundations

| Topic | Lesson |
|---|---|
| How AI changes system design | [001](phases/01-ai-foundations/001-what-changes-in-the-ai-era.md) |
| LLM request lifecycle: tokenize, prefill, decode | [002](phases/01-ai-foundations/002-life-of-an-llm-request.md) |
| Tokens, context windows, history budgeting | [003](phases/01-ai-foundations/003-tokens-and-context-windows.md) |
| TTFT, TPOT, goodput, percentiles | [004](phases/01-ai-foundations/004-ai-latency-numbers.md) |
| GPU memory and compute napkin math | [005](phases/01-ai-foundations/005-gpu-napkin-math.md) |
| Sampling, temperature, non-determinism | [006](phases/01-ai-foundations/006-non-determinism.md) |
| Quality, latency and cost SLOs | [007](phases/01-ai-foundations/007-ai-slos.md) |
| Requirements for AI features, 'should this be AI?' | [008](phases/01-ai-foundations/008-ai-requirements.md) |

## Serving

| Topic | Lesson |
|---|---|
| Accelerators: capacity, bandwidth, FLOPS, interconnect | [009](phases/02-model-serving/009-gpus-and-accelerators.md) |
| Static vs continuous batching, chunked prefill | [010](phases/02-model-serving/010-continuous-batching.md) |
| KV cache, PagedAttention, preemption | [011](phases/02-model-serving/011-kv-cache-and-pagedattention.md) |
| Quantization (FP8, INT4, KV) | [012](phases/02-model-serving/012-quantization.md) |
| Tensor, pipeline, expert parallelism, MoE | [013](phases/02-model-serving/013-model-parallelism.md) |
| Token streaming, SSE, resume, cancellation | [014](phases/02-model-serving/014-streaming-tokens.md) |
| Model gateway, routing, cascades, fallbacks | [015](phases/02-model-serving/015-model-gateway.md) |
| GPU autoscaling, cold starts, warm pools | [016](phases/02-model-serving/016-gpu-autoscaling.md) |
| Speculative decoding, latency tricks | [017](phases/02-model-serving/017-speculative-decoding.md) |
| Prefix, exact and semantic caching | [018](phases/02-model-serving/018-prompt-and-semantic-caching.md) |

## Retrieval & data

| Topic | Lesson |
|---|---|
| Embeddings, cosine similarity, multimodal | [019](phases/03-retrieval-data/019-embeddings.md) |
| Vector databases, HNSW, IVF, PQ, filtering | [020](phases/03-retrieval-data/020-vector-databases-ann.md) |
| RAG architecture, citations, faithfulness | [021](phases/03-retrieval-data/021-rag-architecture.md) |
| Parsing, chunking, ingestion pipelines | [022](phases/03-retrieval-data/022-chunking-and-ingestion.md) |
| Hybrid search, RRF, cross-encoder reranking | [023](phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| Index freshness, CDC, deletion fan-out, blue/green | [024](phases/03-retrieval-data/024-index-freshness.md) |
| Multi-tenancy, ACLs, permission-aware retrieval | [025](phases/03-retrieval-data/025-permission-aware-retrieval.md) |
| Feature stores, point-in-time correctness, skew | [026](phases/03-retrieval-data/026-feature-stores.md) |
| Training data pipelines, PII scrubbing, decontamination | [027](phases/03-retrieval-data/027-training-data-pipelines.md) |
| Prompting vs RAG vs fine-tuning, LoRA, distillation | [028](phases/03-retrieval-data/028-prompting-rag-fine-tuning.md) |

## Agents

| Topic | Lesson |
|---|---|
| Tool / function calling | [029](phases/04-agents/029-tool-calling.md) |
| Agent loops, planning, budgets | [030](phases/04-agents/030-agent-loops.md) |
| Agent memory and state | [031](phases/04-agents/031-agent-memory.md) |
| Durable execution, workflows, sagas for agents | [032](phases/04-agents/032-durable-agent-execution.md) |
| Multi-agent patterns | [033](phases/04-agents/033-multi-agent-systems.md) |
| MCP and tool protocols | [034](phases/04-agents/034-mcp-and-tool-protocols.md) |
| Structured outputs, constrained decoding, validation | [035](phases/04-agents/035-structured-outputs.md) |
| Human-in-the-loop approvals | [036](phases/04-agents/036-human-in-the-loop.md) |

## Quality, safety & ops

| Topic | Lesson |
|---|---|
| Offline evals, golden sets, statistics | [037](phases/05-quality-safety-ops/037-offline-evals.md) |
| A/B tests, LLM-as-judge, calibration | [038](phases/05-quality-safety-ops/038-online-evals.md) |
| LLM observability and tracing | [039](phases/05-quality-safety-ops/039-llm-observability.md) |
| Guardrails and moderation | [040](phases/05-quality-safety-ops/040-guardrails-and-moderation.md) |
| Prompt injection and AI security | [041](phases/05-quality-safety-ops/041-prompt-injection-security.md) |
| Cost engineering, token quotas, unit economics | [042](phases/05-quality-safety-ops/042-cost-engineering.md) |
| AI reliability, fallbacks, degraded modes | [043](phases/05-quality-safety-ops/043-ai-reliability.md) |
| Privacy, PII, residency, prompt/model rollouts | [044](phases/05-quality-safety-ops/044-privacy-and-model-rollout.md) |

## Case studies

| Topic | Lesson |
|---|---|
| ChatGPT-style assistant | [045](phases/06-ai-case-studies/045-design-chatgpt.md) |
| Enterprise document Q&A | [046](phases/06-ai-case-studies/046-design-enterprise-rag.md) |
| AI coding agent | [047](phases/06-ai-case-studies/047-design-coding-agent.md) |
| Embedding-based recommendations | [048](phases/06-ai-case-studies/048-design-recommendations.md) |
| Real-time voice assistant | [049](phases/06-ai-case-studies/049-design-voice-assistant.md) |
| Capstone | [050](phases/06-ai-case-studies/050-capstone.md) |

## 🔁 Book 1 atoms that reappear most

| Book 1 atom | Reappears in Book 2 as |
|---|---|
| [Caching (027)](../phases/04-caching/027-caching-basics.md) | Prefix, exact and semantic caches ([018](phases/02-model-serving/018-prompt-and-semantic-caching.md)) |
| [Rate limiting (024)](../phases/03-scaling-basics/024-rate-limiting.md) | Token-based quotas ([015](phases/02-model-serving/015-model-gateway.md), [042](phases/05-quality-safety-ops/042-cost-engineering.md)) |
| [Queues (057)](../phases/07-async-messaging/057-message-queues.md) | GPU scheduling, batch pipelines ([010](phases/02-model-serving/010-continuous-batching.md), [022](phases/03-retrieval-data/022-chunking-and-ingestion.md)) |
| [Idempotency (055)](../phases/06-scaling-data/055-idempotency.md) | Stream resume, agent side effects ([014](phases/02-model-serving/014-streaming-tokens.md), [032](phases/04-agents/032-durable-agent-execution.md)) |
| [Outbox / CDC (062)](../phases/07-async-messaging/062-event-driven-and-outbox.md) | Index freshness ([024](phases/03-retrieval-data/024-index-freshness.md)) |
| [SLOs (007)](../phases/01-foundations/007-sla-slo-sli.md) | Quality and cost SLOs ([007](phases/01-ai-foundations/007-ai-slos.md)) |
| [Circuit breakers (064)](../phases/08-reliability-ops/064-circuit-breakers-and-bulkheads.md) | Model fallback ladders ([043](phases/05-quality-safety-ops/043-ai-reliability.md)) |
| [Sagas (088)](../phases/10-deep-internals/088-2pc-vs-sagas.md) | Durable agent workflows ([032](phases/04-agents/032-durable-agent-execution.md)) |
| [Search (043)](../phases/05-databases/043-search-and-inverted-index.md) | Hybrid retrieval ([023](phases/03-retrieval-data/023-hybrid-search-reranking.md)) |
| [Deployments (068)](../phases/08-reliability-ops/068-deployment-strategies.md) | Prompt/model canaries ([044](phases/05-quality-safety-ops/044-privacy-and-model-rollout.md)) |
