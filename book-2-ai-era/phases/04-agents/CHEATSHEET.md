# 📌 Phase 04 Cheatsheet: Agents & Orchestration

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 029 | **Model proposes, code disposes.** Strict schemas, user-scoped auth, idempotent writes, actionable errors. |
| 030 | **Plan → act → observe → check.** Budgets (steps, tokens, time, $) in code. Loop detection. Partial results. |
| 031 | **Context + task state + typed long-term memory.** Write policy. World state from systems of record. |
| 032 | **Durable workflows:** record every LLM/tool call, replay on crash, idempotency keys, timers, signals. |
| 033 | **One agent first.** Split for parallelism, isolation, or permissions. Typed contracts, not chat. |
| 034 | **MCP:** servers expose tools/resources/prompts. OAuth scopes. Treat servers as untrusted. |
| 035 | **Constrained decoding + schema + business rules + bounded repair.** |
| 036 | **Approval tiers in code.** Exact action + evidence. Action-bound expiring tokens. Reviewable volume. |

## 🔢 Numbers to know

```
Typical agent budget: 8–15 steps · 20–40k tokens · 15–60 s · $0.05–0.20 per task
Chain reliability: 0.95^5 ≈ 77% → fewer hops, validate each
Multi-agent token overhead: +50% (orchestrator-workers) to 9×+ (group chat)
Repair loop: max 1–2 attempts, then fallback or human
Approval tokens: bound to the action hash, TTL ~5–15 min
```

## 🧮 Formulas

| Formula | Use |
|---|---|
| End-to-end success = Π (step success rates) | Chain reliability |
| Agent cost ≈ Σ steps × (context tokens × in price + out tokens × out price) | Budget sizing |
| Parallel fan-out latency ≈ max(worker) + merge, vs Σ(workers) sequential | Multi-agent payoff |
| Idempotency key = task_id + step_no | Exactly-once effects |

## 🧩 Mnemonics

- **"A pen, not hands"**: tools are requests your code executes.
- **"Shopper with a list and a budget"**: planned, bounded loops.
- **"Open the fridge"**: read world state, don't remember it.
- **"Tick boxes and invoice numbers"**: durable execution + idempotency.
- **"Stations send plates, not opinions"**: typed multi-agent contracts.
- **"Sign the bill, not a summary"**: action-bound approvals.
