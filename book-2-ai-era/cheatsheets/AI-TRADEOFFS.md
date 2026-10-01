# ⚖️ AI-Era Trade-offs in One Place

| X vs Y | Choose X when… | Choose Y when… | Lesson |
|---|---|---|---|
| Large vs small model | Hard reasoning, high stakes | Easy, high-volume, latency-critical | [015](../phases/02-model-serving/015-model-gateway.md) |
| Hosted API vs self-hosting | Low or spiky volume, need frontier models | Steady high volume (> ~30–40% utilization), privacy/control | [042](../phases/05-quality-safety-ops/042-cost-engineering.md) |
| FP16 vs FP8 vs INT4 | Max quality | FP8: near-lossless savings · INT4: memory-bound, eval-proven | [012](../phases/02-model-serving/012-quantization.md) |
| Static vs continuous batching | Never in production | Always | [010](../phases/02-model-serving/010-continuous-batching.md) |
| Tensor vs pipeline parallelism | Lower latency inside a server | Fitting across servers | [013](../phases/02-model-serving/013-model-parallelism.md) |
| SSE vs WebSockets | One-way token streams | Two-way streams (voice, collaboration) | [014](../phases/02-model-serving/014-streaming-tokens.md) |
| Prefix vs semantic cache | Always (safe) | Scoped, low-risk, repetitive questions | [018](../phases/02-model-serving/018-prompt-and-semantic-caching.md) |
| HNSW vs IVF-PQ | Recall and latency, RAM available | Huge scale, tight memory | [020](../phases/03-retrieval-data/020-vector-databases-ann.md) |
| Vector vs keyword search | Meaning, paraphrase | Exact IDs, names, codes → use both | [023](../phases/03-retrieval-data/023-hybrid-search-reranking.md) |
| Small vs large chunks | Precise matching (+ parent context) | More context per hit | [022](../phases/03-retrieval-data/022-chunking-and-ingestion.md) |
| Namespace vs shared index | Big tenants, strong isolation | Many small tenants, efficiency | [025](../phases/03-retrieval-data/025-permission-aware-retrieval.md) |
| RAG vs fine-tuning | Facts that change, must be cited or deleted | Format, tone, narrow tasks, distillation | [028](../phases/03-retrieval-data/028-prompting-rag-fine-tuning.md) |
| Workflow vs free-form agent | Predictable, common intents | Long-tail, open-ended tasks (with budgets) | [030](../phases/04-agents/030-agent-loops.md) |
| Single vs multi-agent | Most tasks | Parallel, isolated, or differently permissioned subtasks | [033](../phases/04-agents/033-multi-agent-systems.md) |
| Auto vs approval | Cheap, reversible, in policy | Costly, irreversible, exceptions | [036](../phases/04-agents/036-human-in-the-loop.md) |
| Code grader vs LLM judge | Checkable properties | Subjective qualities (calibrated) | [037](../phases/05-quality-safety-ops/037-offline-evals.md) |
| Fail open vs fail closed | Low-risk enhancements | Safety, prohibited content | [043](../phases/05-quality-safety-ops/043-ai-reliability.md) |
| Cascaded voice pipeline vs speech-to-speech | Control, tools, inspectability | Lowest latency, natural prosody | [049](../phases/06-ai-case-studies/049-design-voice-assistant.md) |
