# 006 · Non-Determinism & Probabilistic Outputs

> ⏱ 12 min · 📈 12% · 🅰️ AI-era core · Phase 01: AI Foundations for System Designers
>
> `██░░░░░░░░░░░░░░░░░░` 12% of Book 2
>
> 🧬 **Atoms used:** requirements [B1·008] · caching [B1·027] · idempotency [B1·055] · [001] · [003]

---

## 📖 Story

QA runs the satay test ten times. **Nine** answers say "Contains peanuts." **One** says "Nut-free, enjoy!"

Maya runs it a hundred times: **seven** wrong answers. Same prompt. Same model. Same code. She sets `temperature = 0`, the setting that's supposed to make the model deterministic, and runs it a thousand times. **Three** wrong answers remain, and they appear only when the server is **busy**.

In Book 1, a bug was a bug: the same input always produced the same wrong output, so you reproduced it, fixed it, and wrote a test. This bug **can't be reproduced on demand**. At 2,300 messages a second, "7 in 100" means **161 wrong answers every second**.

I told Maya this isn't a bug to fix once. It's a **property** to design around, like network failures. You don't make the network reliable. You build **systems that tolerate an unreliable network**. Let me show you how.

## 🎯 One-sentence idea

**A model samples each token from a probability distribution, so the same input can yield different outputs (and even "temperature 0" isn't perfectly deterministic under batching), which means correctness must be engineered with grounding, constraints, verification, and statistical testing, rather than assumed.**

## 🧸 Analogy

A **talented improv cook**:

- Ask for "something with mushrooms" ten times and you get **ten slightly different dishes**. Usually all good.
- Turn the **creativity dial** down (temperature) and the dishes become more alike, but on a **busy night** a stray hand still changes the seasoning.
- So the restaurant doesn't trust memory: it gives the cook **the actual recipe card** (grounding), a **fixed plating template** (structured output), and a **taster at the pass** (verification).

## 🖼️ Visual

*Diagram brief:* a probability bar chart for the next token after "The satay sauce is", with tall bars for "made" and "not" and a small bar for "nut-free". A sampling arrow occasionally lands on the small bar. Below, the fix: a pipeline of retrieved facts → constrained output → a verifier that blocks contradictions.

```
Next-token probabilities after "The satay sauce is…"
 made      ████████████████████████  0.62
 not       ████████████            0.31
 nut-free  ███                     0.07  ← sampled 7% of the time 😬

Fix:  📚 retrieved allergen facts → 🧱 structured answer {contains:[...]} → ✅ verifier vs database → user
```

## 🔬 How it works

- **Sampling:** the model outputs a **probability for every possible next token**. **Temperature** sharpens or flattens the distribution, and **top-p** cuts off the unlikely tail. Then one token is drawn at random. Higher temperature = more variety and more risk.
- **Temperature 0 isn't a guarantee.** Greedy decoding picks the top token, but floating-point math on GPUs gives **slightly different results depending on batch size and kernel order**, so a near-tie can flip. Under load, batches change, and so do outputs.
- **Design for a distribution, not a value:** treat each answer as a **sample** with an error rate. Reduce the rate (grounding in retrieved facts, lower temperature, better prompts and models), **constrain** the shape (structured outputs, lesson 035), and **verify** high-stakes claims against a source of truth.
- **Facts come from systems of record, not the model:** allergens, prices, and order status are **looked up** and rendered. The model may phrase them, and a checker confirms the phrasing didn't change them.
- **Test statistically:** run each eval case **N times** and track the **pass rate**, with thresholds per risk level (lesson 037). Log the model version, parameters, and prompt with every answer so you can replay the distribution later.

## 🧩 Worked example

**Maya's three layers against the satay bug** (measured over 10,000 runs each):

| Setup | Wrong "nut-free" answers |
|---|---|
| Model alone, temperature 0.7 | 7.0% |
| + temperature 0.2 | 3.1% |
| + allergen facts retrieved from the menu DB into the prompt | 0.4% |
| + structured output `{"contains": [...]}` checked against the DB | **0.00%** (0 / 10,000) |

```python
answer = model.generate(prompt_with_facts, schema=AllergenAnswer, temperature=0.2)
truth = menu_db.allergens(dish_id)            # the system of record
if set(answer.contains) != set(truth):        # verifier: never trust, always check
    answer = render_from_db(truth)            # deterministic fallback
```

**So this means:** for allergens, the model is a **writer, not a source**. The database decides what's true, and the verifier makes the probability of a wrong allergen answer **zero by construction**, not just low.

## ⚖️ Trade-offs

| Technique | You gain | You pay |
|---|---|---|
| Lower temperature | Consistency | Blander, more repetitive answers |
| Grounding (retrieval) | Far fewer invented facts | Retrieval latency and freshness work |
| Structured output + verifier | Hard guarantees on key fields | Engineering per field, less flexible prose |
| Sample N times + vote | Better accuracy on hard questions | N× cost and latency |
| Cache approved answers | Identical answers, zero cost | Staleness, only for repeated questions |

## 🌍 Real world

- Provider docs warn that outputs are **not guaranteed deterministic**, even with a fixed seed, because of batching and hardware differences.
- **Self-consistency** (sampling several reasoning paths and voting) is a known way to trade cost for accuracy on hard problems.
- Regulated domains (health, finance) use LLMs to **draft and explain**, while decisions and facts come from **deterministic systems of record**.

## 📌 Cheat card

> - **Each token is sampled.** Temperature and top-p shape the dice.
> - **Temperature 0 ≠ deterministic** under batching.
> - **Error rate is a design parameter:** ground, constrain, verify.
> - **Facts come from systems of record.** The model phrases, a checker verifies.
> - **Test N times, track pass rates.** Log model + params + prompt.

## 🧪 Feynman check

Explain the improv cook: why ten requests give ten dishes, why turning the creativity dial down isn't enough, and why the restaurant still needs a taster at the pass.

⚠️ **Common confusion:** "Set temperature to 0 and the model becomes deterministic and correct." It becomes **more consistent**, not correct. A consistently wrong answer is still wrong, and batching can still flip near-ties. Correctness comes from **grounding and verification**.

## ⚡ Quick recall

1. What does temperature control?
<details><summary>Reveal Answer</summary>

How sharp or flat the next-token probability distribution is: low temperature favours the most likely tokens, high temperature gives more variety.
</details>

2. Why can outputs differ even at temperature 0?
<details><summary>Reveal Answer</summary>

GPU floating-point results vary slightly with batch composition and kernel ordering, which can flip near-tied tokens.
</details>

3. How do you make a safety-critical fact (like an allergen) reliable?
<details><summary>Reveal Answer</summary>

Look it up in the system of record, have the model output it in a structured field, and verify it against the database before showing it, with a deterministic fallback.
</details>

## 🎤 Interview practice

**Q. "Your LLM feature gives a wrong answer 2% of the time. How do you design the system so that's acceptable, or drive it to near zero where it matters?"**
<details><summary>Model answer</summary>

- **Classify by risk:** what does a wrong answer cost? Recipe ideas (low), prices and order status (medium), allergens and refunds (high).
- **Reduce the error rate:** ground answers in retrieved facts, lower the temperature, improve prompts, and use a stronger model for high-risk intents (routing, lesson 015).
- **Constrain and verify high-risk outputs:** structured outputs for the key fields, validated against the **system of record**, with a deterministic template fallback when they disagree.
- **Contain the remaining risk:** cite sources, show uncertainty, offer a human handoff, and keep actions behind confirmations (lesson 036).
- **Measure it continuously:** an eval set run N times per case, online sampling with a judge (lesson 038), and a **quality SLO** per risk tier (lesson 007).
- **Likely follow-up:** "Why not just vote across 5 samples everywhere?" → it multiplies cost and latency by 5. Use it only where accuracy is worth it, and prefer verification against data when a source of truth exists.
</details>

## 📖 Teaser

> 📖 *The allergen bug is dead, and Maya's dashboard glows green on every SLO she owns, on the very afternoon the answers quietly get worse.*

---

⬅️ [✅ Checkpoint 10%](checkpoint-10.md) · 🗺️ [Phase map](README.md) · ➡️ [007 · AI SLOs: Quality, Latency & Cost](007-ai-slos.md)

✅ **Safe stopping point.** Tick lesson 006 in [PROGRESS.md](../../PROGRESS.md).
