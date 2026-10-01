# 🧩 AI Patterns: "If you hear X, reach for Y"

| If you hear… | Reach for… | Lesson |
|---|---|---|
| "It's slow before anything appears" | Reduce queueing and prefill: capacity, prefix caching, shorter prompts | [004](../phases/01-ai-foundations/004-ai-latency-numbers.md), [018](../phases/02-model-serving/018-prompt-and-semantic-caching.md) |
| "It's slow to finish" | Fewer output tokens, smaller/quantized model, speculative decoding | [012](../phases/02-model-serving/012-quantization.md), [017](../phases/02-model-serving/017-speculative-decoding.md) |
| "GPUs are expensive and half-idle" | Continuous batching + paged KV, $/1M tokens as the metric | [010](../phases/02-model-serving/010-continuous-batching.md), [011](../phases/02-model-serving/011-kv-cache-and-pagedattention.md) |
| "The model won't fit" | Quantize, then tensor parallelism in a server, pipeline across servers | [012](../phases/02-model-serving/012-quantization.md), [013](../phases/02-model-serving/013-model-parallelism.md) |
| "Every team calls providers directly" | A model gateway: quotas, routing, fallbacks, metering | [015](../phases/02-model-serving/015-model-gateway.md) |
| "It makes things up about our data" | RAG with citations and "I don't know" | [021](../phases/03-retrieval-data/021-rag-architecture.md) |
| "Search misses product codes" | Hybrid BM25 + vectors + rerank | [023](../phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| "Answers are stale" | CDC into the index, freshness SLI | [024](../phases/03-retrieval-data/024-index-freshness.md) |
| "Multi-tenant documents" | Identity-derived filters, namespaces, late ACL checks | [025](../phases/03-retrieval-data/025-permission-aware-retrieval.md) |
| "Teach the model our menu" | RAG for facts, fine-tuning for behaviour | [028](../phases/03-retrieval-data/028-prompting-rag-fine-tuning.md) |
| "The model should take actions" | Tool calling with strict schemas and user-scoped auth | [029](../phases/04-agents/029-tool-calling.md) |
| "The agent loops forever" | Plan-then-execute, budgets, loop detection | [030](../phases/04-agents/030-agent-loops.md) |
| "The agent did it twice after a crash" | Durable workflows + idempotency keys | [032](../phases/04-agents/032-durable-agent-execution.md) |
| "Let's add more agents" | One agent first. Orchestrator-workers only for parallel/isolated work | [033](../phases/04-agents/033-multi-agent-systems.md) |
| "Customers want our tools in their AI apps" | A remote MCP server with OAuth scopes | [034](../phases/04-agents/034-mcp-and-tool-protocols.md) |
| "JSON parsing fails" | Constrained decoding + business-rule validation | [035](../phases/04-agents/035-structured-outputs.md) |
| "It can spend money" | Approval tiers, action-bound tokens | [036](../phases/04-agents/036-human-in-the-loop.md) |
| "Is the new prompt better?" | Golden set, N runs, paired stats, CI gate | [037](../phases/05-quality-safety-ops/037-offline-evals.md) |
| "Offline looks great, users disagree" | A/B on outcomes, calibrated judges | [038](../phases/05-quality-safety-ops/038-online-evals.md) |
| "Why did it answer that?" | AI tracing with versions, chunks, tools, cost | [039](../phases/05-quality-safety-ops/039-llm-observability.md) |
| "It said something dangerous" | Independent input/output guardrails | [040](../phases/05-quality-safety-ops/040-guardrails-and-moderation.md) |
| "It obeyed text inside a document" | Contain injection: least privilege, quarantine, close channels | [041](../phases/05-quality-safety-ops/041-prompt-injection-security.md) |
| "The bill exploded" | Attribution, unit economics, levers, budgets, alerts | [042](../phases/05-quality-safety-ops/042-cost-engineering.md) |
| "The provider is down" | Bulkheads, TTFT timeouts, breakers, a fallback ladder | [043](../phases/05-quality-safety-ops/043-ai-reliability.md) |
| "We changed the prompt and it broke" | Versioned prompts, canary, auto-rollback | [044](../phases/05-quality-safety-ops/044-privacy-and-model-rollout.md) |
