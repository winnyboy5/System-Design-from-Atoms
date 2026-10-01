# ✅ Checkpoint 20%: Trust, Targets & the Machine Room

> ⏱ 20 min · Covers lessons **006–010** · 📈 You're at **20%**
>
> `████░░░░░░░░░░░░░░░░` 🎉 A fifth of the way! You can now reason about quality, requirements, and GPUs.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *Maya's serving fleet finally hums. Before I let her near retrieval, let's check that you'd have written the same requirements and picked the same GPUs.*

---

## ⚡ Part 1: Recall (5 questions)

1. Why isn't temperature 0 a correctness guarantee?
<details><summary>Reveal Answer</summary>

It only makes outputs more consistent (and not perfectly, because batching changes GPU math). A consistent answer can still be wrong. Correctness needs grounding and verification.
</details>

2. Name the three families of AI SLOs, with one SLI each.
<details><summary>Reveal Answer</summary>

Service (p95 TTFT), quality (golden-set score or sampled groundedness), cost ($ per conversation).
</details>

3. What's the gate question for any AI feature?
<details><summary>Reveal Answer</summary>

Could rules, search, or a lookup do this reliably and more cheaply?
</details>

4. Which GPU number sets decode speed, and which metric should replace "GPU util %"?
<details><summary>Reveal Answer</summary>

Memory bandwidth. Replace utilization % with tokens/s per GPU and $ per 1M tokens (plus MBU/MFU).
</details>

5. Why does continuous batching beat static batching?
<details><summary>Reveal Answer</summary>

Sequences join and leave at every decode step, so slots stay full and short answers aren't blocked behind long ones.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 3-minute timer. Explain to an imaginary 12-year-old:

> "Why can an AI give a different answer every time you ask, and how can a company still make sure it never gets the important things wrong?"

Aim to naturally use: **the improv cook (sampling)**, **the recipe card (grounding)**, **the taster at the pass (verification)**, and **the inspector's three clipboards (SLOs)**.

---

## 🛠️ Part 3: Mini-design

**A "Which wine goes with this dish?" feature.** 500k questions/day. Answers must never recommend alcohol to users flagged under the legal age, and a wrong pairing is mildly embarrassing but harmless.

On paper:
1. Which parts are deterministic, and which are AI?
2. Error tolerance per failure type.
3. Two SLIs you'd alert on.

<details><summary>One good answer</summary>

- **Deterministic:** age eligibility (from the verified profile) gates the feature **before** any model call. The wine catalogue and prices come from the database.
- **AI:** choosing and explaining pairings among catalogue items (structured output of SKUs, validated against the catalogue).
- **Error tolerance:** underage exposure = **0** (enforced by code, not prompts). Non-existent wine = 0 (validated SKUs). Odd pairing ≤ 5% (judged on a golden set).
- **SLIs:** golden-set pairing score, and the share of answers with SKUs not in the catalogue (should be 0, so alert on any). Also p95 TTFT and $ per answer.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "A provider silently updated their model and your answers got worse. How would you have caught it in minutes?"**
<details><summary>Model answer</summary>

Pin dated snapshots, log model version per request, and run a golden set hourly (N runs per case) with quality burn-rate alerts. Sample live traffic with a calibrated judge. Alert on SLI shifts correlated with version changes.
</details>

**Q2. "A cheaper GPU costs 70% less per hour. Why might it raise your bill?"**
<details><summary>Model answer</summary>

If it has much lower memory bandwidth and capacity, it produces far fewer tokens per hour (smaller batches, slower decode), so the **cost per token** rises. Compare $ per 1M tokens at your SLO.
</details>

**Q3. "Interactive chat and bulk summarization share GPUs. How do you protect chat latency?"**
<details><summary>Model answer</summary>

Continuous batching with priority queues, admission by KV memory and a TPOT-based batch cap, chunked prefill so long bulk prompts don't stall decode, and preemption of bulk sequences under memory pressure.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [011 · The KV Cache & PagedAttention](011-kv-cache-and-pagedattention.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [006](../01-ai-foundations/006-non-determinism.md), [007](../01-ai-foundations/007-ai-slos.md), and [010](010-continuous-batching.md), then retry tomorrow. |

---

⬅️ [010 · Static vs Continuous Batching](010-continuous-batching.md) · 🗺️ [Phase map](README.md) · ➡️ [011 · The KV Cache & PagedAttention](011-kv-cache-and-pagedattention.md)

✅ Tick **Checkpoint 20%** in [PROGRESS.md](../../PROGRESS.md). 🎉
