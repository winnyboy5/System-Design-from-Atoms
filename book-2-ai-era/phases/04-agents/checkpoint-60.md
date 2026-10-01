# ✅ Checkpoint 60%: From Data to Hands

> ⏱ 20 min · Covers lessons **026–030** · 📈 You're at **60%**
>
> `████████████░░░░░░░░` 🎉 Three-fifths! You can prepare data, choose how to adapt models, and give them safe tools.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *The chef has hands now, and a budget on its loop. Before it gets memory and a crash-proof spine, let's make sure yours is solid.*

---

## ⚡ Part 1: Recall (5 questions)

1. What does a point-in-time join protect against?
<details><summary>Reveal Answer</summary>

Leakage: training on feature values that weren't available at the time of the example.
</details>

2. Name the stages of a training-data pipeline.
<details><summary>Reveal Answer</summary>

Consent filtering, PII scrubbing, deduplication, quality filtering, decontamination against eval sets, and versioning with lineage.
</details>

3. Facts that change daily: prompting, RAG, or fine-tuning?
<details><summary>Reveal Answer</summary>

RAG: fine-tuning is for behaviour, and can't stay current, cite, or forget.
</details>

4. Who validates and executes a tool call, and with whose permissions?
<details><summary>Reveal Answer</summary>

Your executor code, with the user's own scoped permissions, never the model or a master key.
</details>

5. Which four budgets bound an agent loop?
<details><summary>Reveal Answer</summary>

Steps, tokens, time, and money.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 3-minute timer. Explain to an imaginary 12-year-old:

> "How can an AI safely do things in the real world, like adding food to your basket, without going wrong or going on forever?"

Aim to naturally use: **the form at the service desk (tool calls)**, **the clerk who checks it (validation and permissions)**, and **the personal shopper with a plan and a budget (agent loop)**.

---

## 🛠️ Part 3: Mini-design

**"Reorder my usual."** A customer says "reorder my usual Friday dinner". The agent must find their usual order, check what's available today, propose substitutions, and place the order.

On paper:
1. The tools (read vs write) and their key schema constraints.
2. The plan and its budgets.
3. What's deterministic, and what's the model's job?

<details><summary>One good answer</summary>

- **Tools:** read: `get_order_history(weeks)`, `check_availability(item_ids)`, `search_menu(query)`. Write: `add_to_cart(item_id, qty 1–20)` (idempotent), `place_order(cart_id)` (customer confirmation tier, lesson 036).
- **Plan:** find the "usual" (deterministic: the most frequent Friday basket in history) → check availability → propose substitutions for missing items → confirm with the customer → place the order. Budgets: 8 steps, 20k tokens, 20 s, $0.05.
- **Deterministic:** the usual basket, availability, prices, totals. **Model:** understanding the request, choosing sensible substitutions, and explaining them warmly.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Why might a fine-tuned model ace your eval but disappoint users?"**
<details><summary>Model answer</summary>

Contamination (eval items in training), leakage, a non-representative random split, or training/serving prompt differences. Fix with decontamination, time-based holdouts, lineage, and matching serving conditions.
</details>

**Q2. "The model passes a quantity as the string '4 2'. Whose bug is it?"**
<details><summary>Model answer</summary>

The system's: the executor must validate against a strict schema (integer, 1–20) and return an actionable error. The model only proposes requests.
</details>

**Q3. "An agent loops 30 times on a task it usually does in 6. What controls do you add?"**
<details><summary>Model answer</summary>

Plan-then-execute, hard budgets in code, duplicate-call and no-progress detection, one re-plan then ask the user, deterministic tools for arithmetic, and partial results on exhaustion.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [031 · Agent Memory & State](031-agent-memory.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [028](../03-retrieval-data/028-prompting-rag-fine-tuning.md) and [029](029-tool-calling.md), then retry tomorrow. |

---

⬅️ [030 · Agent Loops & Planning](030-agent-loops.md) · 🗺️ [Phase map](README.md) · ➡️ [031 · Agent Memory & State](031-agent-memory.md)

✅ Tick **Checkpoint 60%** in [PROGRESS.md](../../PROGRESS.md). 🎉
