# 027 · Training & Fine-Tuning Data Pipelines

> ⏱ 13 min · 📈 54% · 🅰️ AI-era core · Phase 03: Retrieval & Data for AI
>
> `██████████░░░░░░░░░░` 54% of Book 2
>
> 🧬 **Atoms used:** batch processing [B1·091] · object storage [B1·042] · probabilistic data structures (MinHash) [B1·090] · security essentials [B1·070] · [024] · [026]

---

## 📖 Story

Maya wants the small 8B model to handle support chats **in Pantry's own voice**, cheaply. She has **five million** past support conversations. The plan: **fine-tune** on them.

Version one of the dataset is a straight export. Before training, she samples **200 rows** to read, and her stomach drops:

- **Card numbers, phone numbers, and home addresses**, typed by customers into the chat.
- The same "where's my order?" conversation **40,000 times**, nearly word for word.
- Agents giving **wrong answers**: refunds promised that policy forbids.
- Chats from customers who had **opted out** of their data being used.
- And **312** conversations that are near-copies of questions in her **golden eval set** (lesson 007). Train on those, and the exam score goes up because the model **memorized the answers**.

The trained model would have repeated card numbers, overweighted "where's my order?", copied bad advice, and broken a privacy promise, while acing a contaminated test.

I told Maya that training data is the **one ingredient you can never take back out of the dish**. Once it's baked into the weights, you can't delete a single card number. So the pipeline that prepares it deserves more care than the training itself. Let me show you the stages.

## 🎯 One-sentence idea

**A training-data pipeline turns raw logs into a trustworthy, versioned dataset through consent filtering, PII scrubbing, deduplication, quality filtering, and decontamination against eval sets, with full lineage, because whatever enters the weights can't be removed later, and contaminated data makes evals lie.**

## 🧸 Analogy

**Sourcing ingredients for a signature dish** that will be canned and shipped worldwide:

- Only buy from **farmers who agreed** to sell (consent).
- **Wash off** anything harmful (PII scrubbing).
- Don't fill the can with **forty thousand identical potatoes** (deduplication).
- Throw out the **bruised** produce (quality filtering).
- Never use the **judges' tasting samples** as ingredients (decontamination).
- Label every can with **which farm and which day** it came from (lineage). Once it's canned, you can't un-can a bad potato.

## 🖼️ Visual

*Diagram brief:* a funnel of pipeline stages from raw chat logs to a versioned dataset, with the row count shrinking at each stage. Side arrows show rejected data going to quarantine, and a lineage record attached to the final dataset links it to source snapshots and the code version.

```
Raw support chats ............................ 5.0M  🗂️
  ├─ consent / opt-out filter ................ 3.4M  (−1.6M opted out)
  ├─ PII scrub (cards, phones, addresses) .... 3.4M  (redacted, not dropped)
  ├─ near-dedupe (MinHash) ................... 2.3M  (−1.1M duplicates)
  ├─ quality filter (resolved, policy-correct) 0.9M  (−1.4M)
  ├─ decontaminate vs golden/eval sets ....... 0.9M  (−312 near-matches)
  └─ dataset v7 + lineage manifest ........... 0.9M chats ≈ 315M tokens ✅
```

## 🔬 How it works

- **Consent and purpose first:** include only data you're **allowed** to train on, by terms, region, and per-user opt-outs. Tag every record with its source and consent basis, and **re-check opt-outs at every dataset build** (lesson 044).
- **Scrub PII:** detect and redact cards, phones, emails, and addresses (pattern detectors + NER models), replacing them with typed placeholders (`<PHONE>`). Never rely on the model "not memorizing": rare strings **do** get memorized.
- **Deduplicate:** exact hashes for identical rows, **MinHash/LSH** for near-duplicates (Book 1 lesson 090). Duplicates overweight common patterns and increase memorization.
- **Filter for quality:** keep examples that represent the behaviour you want: resolved chats, high ratings, **policy-correct** answers (check with rules or a judge model), and balanced intents. Remove or relabel bad agent answers.
- **Decontaminate and split honestly:** remove near-matches of **every eval set** from training. Split train/validation by **time or by user**, not randomly, so validation reflects the future.
- **Version and lineage:** immutable dataset versions in object storage, with a **manifest** (source snapshots, filters, code version, counts). Every trained model points to its dataset version, so you can answer "what was this model trained on?", and rebuild without data that must be removed.

## 🧩 Worked example

**Training cost for a LoRA fine-tune of the 8B model on dataset v7:**

```
Tokens: 0.9M chats × ~350 tokens ≈ 315M tokens per epoch
Training FLOPs ≈ 6 × params × tokens = 6 × 8B × 315M ≈ 1.5 × 10¹⁹
One GPU at ~400 TFLOPS effective → 1.5e19 / 4e14 ≈ 38,000 s ≈ 10.5 GPU-hours
Two epochs on 8 GPUs ≈ 3 hours, ≈ $60 of GPU time
```

**The cheap part is the training. The expensive part is the data.**

**Results, contaminated vs clean:**

| Dataset | Golden-set score | Fresh holdout (last month's chats) | PII probes leaked |
|---|---|---|---|
| v1 raw export | 94% 🤨 | 79% | 11 of 1,000 |
| v7 cleaned + decontaminated | 88% | **87%** | **0** of 1,000 |

**So this means:** the raw dataset's 94% was a **memorization mirage**. The clean model scores lower on the contaminated exam and **higher** on genuinely new chats, which is the only number that matters.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Aggressive filtering | Cleaner behaviour, less memorization | Smaller dataset, risk of losing rare cases |
| Redact vs drop PII rows | Keeps useful context | Imperfect detectors miss some PII |
| Near-deduplication | Balanced data, less memorization | Compute for MinHash/LSH |
| Time-based splits | Honest validation | Fewer recent examples for training |
| Full lineage | Audits, rebuilds, deletions | Storage and process discipline |

## 🌍 Real world

- Large-model builders publish data pipelines with **dedup, quality filtering, and decontamination** steps, because benchmark contamination inflates scores.
- Research has shown models can **memorize and regurgitate** rare training strings, including personal data.
- Data-governance rules increasingly require **documenting training data sources** and honouring opt-outs.

## 📌 Cheat card

> - **Consent → PII scrub → dedupe → quality filter → decontaminate → version.**
> - **What's in the weights can't be deleted.** Clean before training.
> - **Decontaminate against every eval set.** Split by time or user.
> - **Lineage manifest per dataset.** Models point to datasets.
> - **Training FLOPs ≈ 6 × params × tokens.** Data prep costs more than GPUs.

## 🧪 Feynman check

Explain sourcing ingredients for a canned dish: why each washing and sorting step matters, and why using the judges' tasting samples makes the competition meaningless.

⚠️ **Common confusion:** "More data is always better." **Duplicated, low-quality, contaminated, or non-consented** data makes models worse, riskier, or legally unusable. A smaller, clean, honest dataset routinely beats a bigger messy one.

## ⚡ Quick recall

1. What is eval contamination?
<details><summary>Reveal Answer</summary>

When eval examples (or near-copies) appear in the training data, so the model scores well by memorizing rather than generalizing.
</details>

2. Why deduplicate training data?
<details><summary>Reveal Answer</summary>

Duplicates overweight common patterns and increase memorization, without adding new information.
</details>

3. Why keep a lineage manifest for every dataset version?
<details><summary>Reveal Answer</summary>

To know exactly what each model was trained on, reproduce builds, audit data use, and rebuild without data that must be removed.
</details>

## 🎤 Interview practice

**Q. "Design the pipeline that turns user feedback (thumbs, edits, and chats) into monthly fine-tuning datasets for a support model."**
<details><summary>Model answer</summary>

- **Collection:** feedback events with request IDs → the lake, joined with the logged prompt, retrieved context, model version, and response.
- **Eligibility:** consent and region filters, opt-outs re-checked at build time, and exclusions for deleted users (via the deletion log).
- **Processing (batch, idempotent):**
  - PII redaction with typed placeholders.
  - Near-dedupe with MinHash.
  - Quality selection: thumbs-up answers and **human-edited** answers as targets. Thumbs-down examples go to preference data (chosen vs rejected) or to the eval set, never as targets.
  - Policy checks by a judge model, plus human review of a sample.
  - Decontamination against all eval sets.
- **Output:** immutable dataset `v{month}` with a manifest. Train/validation split by time.
- **Training and gating:** fine-tune (often LoRA, lesson 028), then eval on golden + fresh holdout + safety + PII probes. Ship only if there's no regression on any critical slice, and canary it (lesson 044).
- **Likely follow-up:** "A user asks to delete their data after it's been trained on." → it's excluded from all future datasets, and the next scheduled retrain removes its influence. That's why the retraining cadence and lineage matter.
</details>

## 📖 Teaser

> 📖 *The clean dataset is ready, and then the menu team asks to fine-tune the model on "all our menus" so it knows them by heart, and Maya has to explain why that's the wrong tool for the job.*

---

⬅️ [026 · Feature Stores](026-feature-stores.md) · 🗺️ [Phase map](README.md) · ➡️ [028 · Prompting vs RAG vs Fine-Tuning](028-prompting-rag-fine-tuning.md)

✅ **Safe stopping point.** Tick lesson 027 in [PROGRESS.md](../../PROGRESS.md).
