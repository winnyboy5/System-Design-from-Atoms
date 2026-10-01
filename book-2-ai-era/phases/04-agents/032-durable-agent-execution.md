# 032 · Durable Execution for Agents

> ⏱ 13 min · 📈 64% · 🅰️ AI-era core · Phase 04: Agents & Orchestration
>
> `████████████░░░░░░░░` 64% of Book 2
>
> 🧬 **Atoms used:** idempotency [B1·055] · sagas [B1·088] · event sourcing [B1·089] · job scheduler [B1·097] · [029] · [030] · [031]

---

## 📖 Story

"Plan My Week" now runs as a **12-step** agent: gather preferences, search, plan, price, build the cart, **place the order**, schedule delivery, send the summary. It takes about **six minutes**, with one step waiting for a supplier API that's often slow.

Tuesday, 18:20. A routine deploy rolls the agent servers. A run at **step 9** dies mid-flight. The retry logic does what it was told: **start the task again from the top**. Steps 1–8 re-run: **$0.40** of model calls repeated. Step 6 re-runs too: **a second grocery order** is placed. The customer is charged **twice**.

Meanwhile, another run is waiting for the customer to **approve a substitution**. The customer replies **two hours later**. The agent's process is long gone, and so is the conversation's state.

I told Maya that a long-running agent is a **distributed transaction that thinks**. Book 1 taught her sagas, idempotency, and job schedulers, and every one of them applies. The modern packaging is called **durable execution**: the task's progress is **written down after every step**, so a crash means **resuming**, never **restarting**. Let me show you.

## 🎯 One-sentence idea

**Durable execution runs an agent as a workflow whose every step (each LLM call and each tool call) is recorded in a persistent history, so after a crash it replays completed steps from the record instead of re-running them, with retries, timers, and waits for human input handled by the workflow engine, and idempotency keys on every side effect.**

## 🧸 Analogy

A **recipe card with tick boxes**, in a kitchen where cooks change shifts:

- Every time a step is done, the cook **ticks it and notes the result** ("onions caramelized at 18:04").
- If the cook goes home mid-recipe, the next cook **reads the ticks** and continues from the first unticked step. They don't caramelize the onions again.
- "**Wait for the customer to choose a sauce**" is a step too: the card waits on the rail, for hours if needed, then continues.
- And the step "**pay the supplier**" carries an **invoice number**, so even if it's accidentally done twice, the supplier only charges once.

## 🖼️ Visual

*Diagram brief:* a workflow timeline of twelve steps, each a box with a green tick and a stored result. A crash marker at step 9 is followed by "worker 2 replays history 1–8 from the log (no re-execution), runs step 9". Step 6 (place order) has an idempotency-key label. A pause between steps 10 and 11 is labelled "wait for signal: customer approval (up to 24 h)".

```
HISTORY (persisted after every step)
[1 prefs ✓][2 search ✓][3 plan ✓][4 price ✓][5 cart ✓][6 ORDER ✓ idem=wk881-6][7 slot ✓][8 notify ✓]
                                                                     💥 worker 1 dies during step 9
worker 2: replay 1–8 from history (0 model calls, 0 side effects) → run 9 → [10 ✓]
          ⏸️ step 10½: await signal "substitution_approved" (timer: 24 h → default: skip item)
          → [11 ✓][12 ✓]
```

## 🔬 How it works

- **Workflow = durable code:** the agent's control flow is a **workflow** whose state is an **event history** (Book 1 lesson 089): every step's input and result is persisted before moving on. Workers are stateless and interchangeable.
- **LLM and tool calls are recorded steps** ("activities"): non-deterministic work runs **once**, and its result is stored. On recovery, the engine **replays** the history: completed steps return their recorded results instantly, with **no new model calls or side effects**.
- **Side effects stay idempotent anyway:** each write step carries an idempotency key such as `task_id + step_no` (Book 1 lesson 055), because a step can crash **after** acting but **before** its result is recorded.
- **Retries, timeouts, and compensation:** per-step retry policies with backoff, step timeouts, and **saga-style compensations** for multi-step failures (cancel the order if delivery can't be scheduled, Book 1 lesson 088).
- **Waiting is free:** durable **timers** and **signals** let a workflow wait hours or days for a human approval or a callback without holding a process or a GPU (lesson 036).
- **Version your workflows:** a running task must continue on the code version it started with, so deploys don't break in-flight histories.

## 🧩 Worked example

**Tuesday's crash, replayed with durable execution:**

| | Restart from the top | Durable execution |
|---|---|---|
| Steps re-executed after the crash | 1–9 | only step 9 |
| Repeated model spend | $0.40 | $0.00 |
| Duplicate grocery orders | 1 (charged twice) | 0 (step 6 replayed from history + idem key) |
| Time to recover | 4 min 50 s | 3 s |

**The two-hour approval wait:**

```python
@workflow
def plan_my_week(task):
    prefs  = step(gather_prefs, task)                  # LLM call, recorded
    plan   = step(make_plan, prefs)                    # LLM call, recorded
    cart   = step(build_cart, plan, idem=f"{task.id}:cart")
    if cart.substitutions:
        ok = wait_for_signal("subs_approved", timeout="24h", default=False)
        if not ok: cart = step(remove_substitutions, cart)
    order  = step(place_order, cart, idem=f"{task.id}:order")
    step(schedule_delivery, order, compensate=cancel_order)
```

**At Pantry's scale:** 300,000 weekly-plan workflows a day, each with ~15 history events → ~4.5M events/day of workflow history, retained for 30 days for audits and debugging.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Durable workflow engine | Crash-proof, resumable, auditable agents | A new platform, workflow versioning discipline |
| Recording every LLM call | No repeated spend, exact replays for debugging | Storage of prompts and outputs |
| Idempotency keys on side effects | Exactly-once effects | Key design per step |
| Durable timers and signals | Cheap long waits for humans | Thinking in workflows, not request handlers |
| Plain retries from the top | Simple | Duplicated actions and spend |

## 🌍 Real world

- **Temporal** (and its predecessor Cadence), **AWS Step Functions**, **Azure Durable Functions**, and similar engines provide durable execution, and are widely used to run AI agents.
- Agent frameworks add **checkpointers** that persist agent state after every step, so threads can resume and be inspected.
- Payment and order systems have long used the same ideas: **sagas, idempotency keys, and event histories**.

## 📌 Cheat card

> - **A long-running agent is a distributed transaction that thinks.**
> - **Persist every step's result.** Crash → **replay**, not restart.
> - **LLM + tool calls = recorded activities.** Idempotency keys on side effects anyway.
> - **Retries, timeouts, compensations per step.**
> - **Durable timers + signals** for human waits. Version workflows.

## 🧪 Feynman check

Explain the recipe card with tick boxes: why the next cook doesn't redo ticked steps, why "wait for the customer" can be a step, and why the supplier payment needs an invoice number even so.

⚠️ **Common confusion:** "We have retries, so the agent is reliable." Retrying a **whole** multi-step agent re-runs every step, repeating spend and **side effects**. Reliability for agents means **resuming from recorded progress**, with each side effect protected by its own idempotency key.

## ⚡ Quick recall

1. What happens to completed steps when a durable workflow recovers from a crash?
<details><summary>Reveal Answer</summary>

They're replayed from the persisted history, returning their recorded results without re-executing the LLM or tool calls.
</details>

2. Why do side effects still need idempotency keys?
<details><summary>Reveal Answer</summary>

A step can crash after performing the action but before its result is recorded, so it may run again: the key makes the second run harmless.
</details>

3. How does a durable workflow wait two hours for a human?
<details><summary>Reveal Answer</summary>

With a durable timer or signal: the engine persists the waiting state, so no process or GPU is held, and the workflow resumes when the signal arrives or the timer fires.
</details>

## 🎤 Interview practice

**Q. "Design the execution layer for agents that run 1–30 minute tasks with real-world side effects (bookings, payments), at 100,000 tasks per hour."**
<details><summary>Model answer</summary>

- **Engine:** durable workflows (Temporal-style). Each task = a workflow with an event history in a replicated datastore, sharded by `task_id`. Stateless workers poll task queues per activity type.
- **Activities:** LLM calls (through the model gateway, lesson 015), tool calls, and human-approval signals. Each has a timeout, a retry policy (backoff, retry only safe errors), and a **heartbeat** for long calls.
- **Side effects:** idempotency keys `task_id:step` on every write, and saga compensations for multi-step commitments (cancel the booking if payment fails).
- **Throughput:** 100k tasks/h × ~20 events ≈ 2M history events/hour (~550/s): partition the history store and scale workers per activity queue. GPU capacity, not the engine, is usually the bottleneck.
- **Operations:** workflow versioning for deploys, a visibility store to search tasks by status, replay for debugging, and retention policies (30–90 days).
- **Limits:** per-task budgets (lesson 030) enforced inside the workflow. Stuck workflows alert after their SLA.
- **Likely follow-up:** "LLM outputs are non-deterministic. Doesn't replay break?" → no: LLM calls are activities whose recorded results are reused during replay. Only the workflow's control code must be deterministic.
</details>

## 📖 Teaser

> 📖 *The agent is crash-proof now, so Maya gets ambitious: six specialist agents, chatting with each other to plan, shop, budget, and cook, and the token bill multiplies by nine.*

---

⬅️ [031 · Agent Memory & State](031-agent-memory.md) · 🗺️ [Phase map](README.md) · ➡️ [033 · Multi-Agent Systems](033-multi-agent-systems.md)

✅ **Safe stopping point.** Tick lesson 032 in [PROGRESS.md](../../PROGRESS.md).
