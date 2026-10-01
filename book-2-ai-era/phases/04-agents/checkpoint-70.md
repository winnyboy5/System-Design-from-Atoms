# ✅ Checkpoint 70%: Agents You Can Trust

> ⏱ 25 min · Covers lessons **031–035** · 📈 You're at **70%**
>
> `██████████████░░░░░░` 🎉 Seventy percent! You can design agents that remember, survive crashes, cooperate, plug in, and output clean data.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *Maya's agents now resume after crashes and talk through one standard plug. Before she puts a human signature on the riskiest actions, let's check your agent instincts.*

---

## ⚡ Part 1: Recall (5 questions)

1. What belongs in long-term memory, and what never does?
<details><summary>Reveal Answer</summary>

Typed, sourced preferences and context belong there. The state of the world (carts, orders, allergies) never does: it's read from systems of record.
</details>

2. After a crash, what happens to a durable workflow's completed steps?
<details><summary>Reveal Answer</summary>

They're replayed from the recorded history without re-running LLM or tool calls. Side effects still carry idempotency keys.
</details>

3. When is a multi-agent design worth it?
<details><summary>Reveal Answer</summary>

When subtasks are parallel, need separate focused contexts, or need different tools, permissions, or models.
</details>

4. What does an MCP server expose, and how should remote servers authorize calls?
<details><summary>Reveal Answer</summary>

Tools, resources, and prompts. Remote servers use OAuth with per-user scopes.
</details>

5. What are the three layers of a correct structured output?
<details><summary>Reveal Answer</summary>

It parses, it matches the schema (both guaranteed by constrained decoding), and it makes business sense (validated in code).
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 4-minute timer. Explain to an imaginary 12-year-old:

> "How does a long, complicated AI task survive the computer crashing halfway through, without doing anything twice?"

Aim to naturally use: **the recipe card with tick boxes (durable history)**, **the invoice number (idempotency)**, and **the job folder vs the fridge (task state vs systems of record)**.

---

## 🛠️ Part 3: Mini-design

**Catering-quote agent.** A business customer asks for a quote for a 200-person event: menu options, dietary needs, supplier availability, pricing, and a PDF quote. It takes 5–20 minutes and may need the customer to answer questions mid-way.

On paper:
1. Single agent or multi-agent? Why?
2. How does it survive a deploy mid-task, and wait for the customer's answers?
3. What output format does the pricing step use?

<details><summary>One good answer</summary>

- **Shape:** an orchestrator with **parallel workers** for supplier availability (one per supplier, isolated contexts), and a single agent for menu reasoning. Pricing is **deterministic code**.
- **Durability:** a durable workflow. Each LLM/tool call is a recorded activity. Supplier holds use idempotency keys. Questions to the customer are **signals** with a 48 h timer and reminders.
- **Format:** the menu proposal and line items use **constrained JSON** with a strict schema, validated against the catalogue and price list, then rendered to a PDF by a template (not by the model).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "The agent remembered a user's joke as a fact. How do you prevent memory poisoning?"**
<details><summary>Model answer</summary>

A memory write policy: an extraction step plus rules (humour and hypothetical detection, plausibility checks), confirmation for critical facts written to the profile, first-party user statements only (never retrieved content), and user-visible, editable memories.
</details>

**Q2. "Your group-chat multi-agent design costs 9× more with no quality gain. What now?"**
<details><summary>Model answer</summary>

Measure against a single-agent baseline. Collapse to one agent with a plan, or to an orchestrator with parallel workers and typed contracts only where parallelism or isolation helps. Budget each agent and trace every hop.
</details>

**Q3. "Strict JSON mode is on. Can outputs still be wrong?"**
<details><summary>Model answer</summary>

Yes: they're well-formed but can have wrong values (bad references, dates, quantities). Add business-rule validation against data, a bounded repair loop, and a human or deterministic fallback.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [036 · Human-in-the-Loop Approvals](036-human-in-the-loop.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [031](031-agent-memory.md) and [032](032-durable-agent-execution.md), then retry tomorrow. |

---

⬅️ [035 · Structured Outputs & Validation](035-structured-outputs.md) · 🗺️ [Phase map](README.md) · ➡️ [036 · Human-in-the-Loop Approvals](036-human-in-the-loop.md)

✅ Tick **Checkpoint 70%** in [PROGRESS.md](../../PROGRESS.md). 🎉
