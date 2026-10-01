# 🎤 Phase 04 Interview Question Bank: Agents & Orchestration

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 5 min.

---

### 🟢 1. What is tool calling? · [029]
<details><summary>Model answer</summary>

The model outputs a structured request (tool name + JSON arguments). Your code validates, authorizes, and executes it, then returns the result for the model to continue.
</details>

### 🟢 2. What is an agent loop? · [030]
<details><summary>Model answer</summary>

A loop where the model decides an action, calls a tool, observes the result, and repeats until the goal is met or a budget or exit condition triggers.
</details>

### 🟢 3. What is MCP? · [034]
<details><summary>Model answer</summary>

The Model Context Protocol: an open standard for AI applications (clients) to discover and call tools, resources, and prompts exposed by servers.
</details>

### 🟢 4. Why use constrained decoding? · [035]
<details><summary>Model answer</summary>

It guarantees outputs follow a grammar or JSON Schema, eliminating parse and structure failures.
</details>

### 🟡 5. How do you make an agent's side effects exactly-once? · [029, 032]
<details><summary>Model answer</summary>

Durable workflows record completed steps for replay, and every write carries an idempotency key (task + step), so retries and crashes can't duplicate actions.
</details>

### 🟡 6. Single agent or multi-agent for a support bot? · [033]
<details><summary>Model answer</summary>

Usually a single agent with intent-specific tools, plus handoffs to specialists where permissions differ (refunds). Multi-agent only for parallelizable or context-heavy subtasks, measured against the single-agent baseline.
</details>

### 🟡 7. How should agent memory handle safety-critical facts? · [031]
<details><summary>Model answer</summary>

Never as free-form memory: they're confirmed explicitly and written to the system of record (profile), then read via tools when needed.
</details>

### 🟡 8. Design approval tiers for an agent that can spend money. · [036]
<details><summary>Model answer</summary>

Auto for small, reversible, in-policy amounts. User confirmation up to a limit. Manager approval above it or on exceptions. Forbidden categories. Exact-action cards, action-bound expiring tokens, durable waits, and audit sampling.
</details>

### 🔴 9. Design an AI travel-booking agent (flights, hotels) for 1M users. · [029–036]
<details><summary>Model answer</summary>

- **Tools:** search (read), hold (write, idempotent, expiring), book (approval-gated), cancel (compensation).
- **Execution:** durable workflows with recorded LLM/tool activities, saga compensations (release holds if payment fails), and signals for user choices.
- **Loop control:** a plan per trip, budgets, and parallel searches via fan-out workers.
- **Data and memory:** preferences as typed memory. Prices and availability always read live.
- **Outputs and approvals:** constrained JSON itineraries validated against provider data. User confirmation shows the exact fare and policies before booking.
- **Scale:** GPU spend on planning, cheap models for extraction, and caching of searches.
</details>

### 🔴 10. An agent placed duplicate orders after an outage. Post-mortem and fixes. · [030, 032]
<details><summary>Model answer</summary>

**Root cause:** whole-task retries after the crash re-ran a write step, and the order API lacked idempotency. **Fixes:** a durable workflow engine (replay, not restart), idempotency keys `task:step` enforced by the order service, compensation for partial failures, per-task budgets, and alerts on duplicate writes per task. **Tests:** chaos-test worker kills at every step.
</details>
