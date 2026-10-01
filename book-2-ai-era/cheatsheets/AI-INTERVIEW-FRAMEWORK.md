# 🎤 The AI System Design Interview Framework

> Book 1's [4-step framework](../../cheatsheets/INTERVIEW-FRAMEWORK.md), extended for systems with models inside. **45 minutes.**

## ⏱️ Time budget

| Step | Time | What you do |
|---|---|---|
| 1. Scope & "should this be AI?" | 5 min | Users, features, **which parts are AI vs deterministic** |
| 2. Requirements with numbers | 5 min | Traffic, **TTFT/TPOT**, **quality bar**, **error tolerance by risk**, **$ per unit**, data/privacy |
| 3. Napkin math | 5 min | Tokens/day, $/day, **concurrency (Little's Law)**, GPUs (memory + prefill), storage |
| 4. High-level design | 10 min | Gateway → context/retrieval → model routing → tools → streaming → storage |
| 5. Deep dives | 15 min | The dominant constraint (2–3 of the list below) |
| 6. Wrap-up | 5 min | Evals + quality SLOs, safety, failure modes, cost levers, what you'd do next |

## 🔍 Deep-dive menu (pick what dominates)

- **GPU capacity & latency:** batching, paged KV, quantization, parallelism, caching, speculative decoding (Phase 02).
- **Grounding:** chunking, hybrid retrieval, reranking, freshness, permissions (Phase 03).
- **Actions:** tool design, budgets, durable execution, approvals (Phase 04).
- **Trust:** evals, guardrails, prompt injection, observability (Phase 05).
- **Economics:** routing, caching, batch, self-host break-even (lesson 042).
- **Resilience:** bulkheads, timeouts, fallback ladder (lesson 043).

## 🗣️ Phrases that signal seniority

- "Before choosing a model, let me separate what must be **deterministic**: prices, permissions, and allergens come from systems of record."
- "That's **~775k concurrent streams** by Little's Law, so GPU memory, not request rate, is the constraint."
- "I'd **measure retrieval recall and answer faithfulness separately**."
- "The prompt isn't a security boundary. **Permissions are enforced in code**, before retrieval."
- "Every change goes **eval gate → shadow → canary** with auto-rollback on quality and cost SLIs."
- "If the provider degrades, **checkout never waits**: the AI path is async with a fallback ladder."

## 🚩 Red flags to avoid

- Putting "the LLM" in the middle and drawing arrows to it.
- No numbers for tokens, cost, or concurrency.
- "We'll tell the model in the prompt not to…" as a safety or security control.
- No plan to measure quality.
- An agent with unbounded loops, broad credentials, or no idempotency.
