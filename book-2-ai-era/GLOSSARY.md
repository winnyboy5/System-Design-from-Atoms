# 📖 Glossary (Book 2)

> Plain-English definitions, linked to the lesson that teaches each one. For classic terms, see [Book 1's glossary](../GLOSSARY.md).

| Term | Plain-English meaning | Lesson |
|---|---|---|
| **A/B test** | Randomized comparison of two versions on real users, judged by outcome metrics. | [038](phases/05-quality-safety-ops/038-online-evals.md) |
| **Agent** | A loop where a model chooses actions (tool calls), observes results, and repeats until a goal or budget is reached. | [030](phases/04-agents/030-agent-loops.md) |
| **ANN (approximate nearest neighbour)** | Index that finds nearly the closest vectors quickly by checking only a fraction of them. | [020](phases/03-retrieval-data/020-vector-databases-ann.md) |
| **BM25** | Classic keyword-relevance scoring that rewards rare matching terms. | [023](phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| **Chunk** | A piece of a document (~200–600 tokens) that is embedded and retrieved on its own. | [022](phases/03-retrieval-data/022-chunking-and-ingestion.md) |
| **Constrained decoding** | Restricting generated tokens so output must match a grammar or JSON Schema. | [035](phases/04-agents/035-structured-outputs.md) |
| **Context window** | Maximum tokens (prompt + output) a model can handle in one request. | [003](phases/01-ai-foundations/003-tokens-and-context-windows.md) |
| **Continuous batching** | Scheduling that adds and removes sequences at every decode step to keep the GPU batch full. | [010](phases/02-model-serving/010-continuous-batching.md) |
| **Cross-encoder (reranker)** | Model that scores a query and a document together for precise relevance. | [023](phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| **Decode** | The token-by-token generation phase. Memory-bandwidth-bound. Sets TPOT. | [002](phases/01-ai-foundations/002-life-of-an-llm-request.md) |
| **Durable execution** | Running workflows whose step results are persisted, so crashes resume instead of restarting. | [032](phases/04-agents/032-durable-agent-execution.md) |
| **Embedding** | A vector representing meaning, so similar texts are close together. | [019](phases/03-retrieval-data/019-embeddings.md) |
| **Eval / golden set** | A versioned set of test cases with graders used to measure quality. | [037](phases/05-quality-safety-ops/037-offline-evals.md) |
| **Faithfulness** | Whether an answer sticks to the provided sources. | [021](phases/03-retrieval-data/021-rag-architecture.md) |
| **Feature store** | System serving the same feature definitions offline (training) and online (serving). | [026](phases/03-retrieval-data/026-feature-stores.md) |
| **Fine-tuning** | Further training a model to change its behaviour (format, tone, narrow tasks). | [028](phases/03-retrieval-data/028-prompting-rag-fine-tuning.md) |
| **Goodput** | Throughput of requests that meet all latency SLOs. | [004](phases/01-ai-foundations/004-ai-latency-numbers.md) |
| **Guardrail** | An independent check on inputs or outputs with a defined action (block, rewrite, escalate). | [040](phases/05-quality-safety-ops/040-guardrails-and-moderation.md) |
| **Hallucination** | A fluent, confident output that is false or unsupported. | [006](phases/01-ai-foundations/006-non-determinism.md) |
| **HNSW** | A graph-based ANN index: fast, high recall, memory-hungry. | [020](phases/03-retrieval-data/020-vector-databases-ann.md) |
| **Hybrid search** | Combining keyword and vector search, usually fused by rank (RRF). | [023](phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| **KV cache** | Stored keys and values of past tokens, kept in GPU memory during generation. | [011](phases/02-model-serving/011-kv-cache-and-pagedattention.md) |
| **LLM-as-judge** | Using a model to grade outputs against a rubric. Must be calibrated against humans. | [038](phases/05-quality-safety-ops/038-online-evals.md) |
| **LoRA** | Low-rank adapters: small trainable matrices that fine-tune a shared base model. | [028](phases/03-retrieval-data/028-prompting-rag-fine-tuning.md) |
| **MCP (Model Context Protocol)** | Open protocol for AI applications to discover and call tools, resources, and prompts on servers. | [034](phases/04-agents/034-mcp-and-tool-protocols.md) |
| **Mixture of experts (MoE)** | Model where each token activates only some expert sub-networks: less compute, same memory. | [013](phases/02-model-serving/013-model-parallelism.md) |
| **PagedAttention** | Managing the KV cache in fixed-size blocks with block tables, like virtual memory. | [011](phases/02-model-serving/011-kv-cache-and-pagedattention.md) |
| **Prefill** | Processing the whole prompt in parallel. Compute-bound. Sets most of TTFT. | [002](phases/01-ai-foundations/002-life-of-an-llm-request.md) |
| **Prefix caching** | Reusing the KV of an identical prompt beginning across requests. | [018](phases/02-model-serving/018-prompt-and-semantic-caching.md) |
| **Product quantization (PQ)** | Compressing vectors into short codes for memory-efficient ANN search. | [020](phases/03-retrieval-data/020-vector-databases-ann.md) |
| **Prompt injection** | Untrusted text that the model follows as instructions. Indirect when hidden in retrieved content. | [041](phases/05-quality-safety-ops/041-prompt-injection-security.md) |
| **Quantization** | Storing weights, activations or KV in fewer bits (FP8, INT4). | [012](phases/02-model-serving/012-quantization.md) |
| **RAG** | Retrieval-augmented generation: retrieve relevant passages, then answer from them with citations. | [021](phases/03-retrieval-data/021-rag-architecture.md) |
| **Recall@k** | Share of relevant items found in the top k results. | [020](phases/03-retrieval-data/020-vector-databases-ann.md) |
| **RRF (reciprocal rank fusion)** | Merging ranked lists by summing 1/(k + rank). | [023](phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| **Semantic cache** | Reusing a past answer for a similar question. Risky unless tightly scoped. | [018](phases/02-model-serving/018-prompt-and-semantic-caching.md) |
| **Speculative decoding** | Drafting several tokens cheaply and verifying them in one large-model pass. Lossless. | [017](phases/02-model-serving/017-speculative-decoding.md) |
| **Structured output** | Model output in a defined schema (usually JSON), validated in code. | [035](phases/04-agents/035-structured-outputs.md) |
| **Temperature** | Sampling setting: lower is more deterministic, higher is more varied. | [006](phases/01-ai-foundations/006-non-determinism.md) |
| **Tensor parallelism** | Splitting each layer across GPUs inside a server. Needs fast interconnect. | [013](phases/02-model-serving/013-model-parallelism.md) |
| **Token** | The sub-word unit a model reads and writes (~4 English characters). | [003](phases/01-ai-foundations/003-tokens-and-context-windows.md) |
| **Tool calling** | The model emits a structured request that your code validates and executes. | [029](phases/04-agents/029-tool-calling.md) |
| **TPOT** | Time per output token: the speed of streaming generation. | [004](phases/01-ai-foundations/004-ai-latency-numbers.md) |
| **TTFT** | Time to first token: queue wait + prefill. | [004](phases/01-ai-foundations/004-ai-latency-numbers.md) |
| **Turn detection / barge-in** | Deciding when a speaker finished. Stopping playback when they interrupt. | [049](phases/06-ai-case-studies/049-design-voice-assistant.md) |
| **Two-tower model** | Recommender with separate user and item encoders into one vector space for ANN retrieval. | [048](phases/06-ai-case-studies/048-design-recommendations.md) |
