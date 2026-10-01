# 📌 Phase 06 Cheatsheet: AI Case Studies

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 045 | **ChatGPT:** msgs/s × stream lifetime = concurrency. Tiers + token quotas, context builder, affinity routing, durable SSE. |
| 046 | **Enterprise Q&A:** connectors sync content + ACLs, hybrid index (PQ at scale), identity filters, late check, citations. |
| 047 | **Coding agent:** microVM sandbox per task, no secrets, search → line reads → diff edits → tests, PR + review. |
| 048 | **Recommendations:** two-tower + ANN → rank with features → rules and diversity, < 100 ms. LLMs offline. |
| 049 | **Voice:** VAD/turns → streaming ASR → fast LLM → sentence-chunked TTS, ~800 ms, barge-in, read-backs. |
| 050 | **Capstone:** should this be AI? → numbers → design → evals → safety → cost → teach → share. |

## 🔢 Numbers to know

```
ChatGPT-scale: 100M DAU × 15 msgs ≈ 17k/s avg, ~50k/s peak · ~775k concurrent streams
Prefix caching on multi-turn chat: often −60–70% prefill compute
Enterprise RAG: 1B chunks → PQ codes ~100 GB RAM + ~1.5 TB vectors on SSD
Coding agent: ~40 steps × ~30k tokens → cache it (~−85% cost)
Recommender budget: user tower 5 + ANN 10 + features 10 + rank 25 + rules 3 ≈ 60 ms
Voice budget: turn 200 + ASR 100 + TTFT 250 + TTS 120 + network 80 ≈ 750 ms
```

## 🧩 The AI design checklist (use it in every case study)

1. **Should this be AI?** Deterministic vs AI split.
2. **Requirements with numbers:** traffic in tokens, TTFT/TPOT, quality bar, error tolerance, $/unit.
3. **Napkin:** tokens/day, $/day, concurrency (Little's Law), GPUs (memory, prefill).
4. **Architecture:** gateway → context/retrieval → model routing → tools → streaming.
5. **Deep dives:** the dominant constraint (GPUs, permissions, sandboxing, latency per item, voice latency).
6. **Quality:** golden set, online evals, quality SLOs.
7. **Safety:** guardrails, injection containment, PII, approvals.
8. **Failure modes:** fallback ladder, degraded mode, bulkheads.
9. **Cost:** unit economics, levers, budgets.
