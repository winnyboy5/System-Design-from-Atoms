# ✅ Checkpoint 10%: Thinking in Tokens

> ⏱ 20 min · Covers lessons **001–005** · 📈 You're at **10%**
>
> `██░░░░░░░░░░░░░░░░░░` 🎉 First Book 2 checkpoint! You now speak the language of models.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking. Peeking turns recall into rereading.

> 📖 *Maya taped her GPU napkin next to the old one from Book 1. Before the story goes on, I want to see your napkin too.*

---

## ⚡ Part 1: Recall (5 questions)

1. Name the four facts that make a model a "strange component".
<details><summary>Reveal Answer</summary>

Priced per token, latency in seconds (streamed), probabilistic correctness, and bound by scarce GPU memory.
</details>

2. Which phase sets TTFT, and which sets TPOT? What limits each?
<details><summary>Reveal Answer</summary>

Prefill sets TTFT and is compute-bound. Decode sets TPOT and is memory-bandwidth-bound.
</details>

3. Why does a 40-turn chat cost far more than 40 × one message?
<details><summary>Reveal Answer</summary>

Every turn re-sends the whole history as input, so input tokens grow quadratically with the number of turns.
</details>

4. What makes up TTFT at peak, and which part dominates the tail?
<details><summary>Reveal Answer</summary>

Queue wait + prefill (+ network). Queueing dominates the tail under load.
</details>

5. Weights of a 13B model in FP16? KV cache formula?
<details><summary>Reveal Answer</summary>

13B × 2 B = **26 GB**. KV per token = 2 × layers × KV heads × head size × bytes per value.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 3-minute timer. Explain to an imaginary 12-year-old:

> "Why does an AI chatbot start answering quickly but sometimes take ages to finish, and why does a long chat cost so much more than a short one?"

Aim to naturally use: **reading the order slip (prefill)**, **plating one course at a time (decode)**, **the order slip that's rewritten every turn (context)**, and **paying the chef by the word (tokens)**. If you get stuck, reread that lesson's 🧸 analogy.

---

## 🛠️ Part 3: Mini-design

**A recipe-summary feature.** Every one of Pantry's 2M recipes gets a 150-token AI summary, regenerated when the recipe changes (~20k recipes/day). Recipes average 1,200 tokens. Prices: $0.30 per 1M input tokens and $1.20 per 1M output tokens (a small model).

On paper:
1. The cost of the initial backfill, and the daily cost after that.
2. Should this run in the interactive chat fleet? Why or why not?
3. One sentence: "So this means the design needs ___."

<details><summary>One good answer</summary>

- **Backfill:** input 2M × 1,200 = 2.4B tokens × $0.30/1M = **$720**. Output 2M × 150 = 300M × $1.20/1M = **$360**. **≈ $1,080 once.**
- **Daily:** 20k recipes → 1% of that ≈ **$11/day**.
- **Fleet:** no. It's **batch work with no latency SLO**, so it runs in a **separate low-priority queue** (or a provider's discounted batch API) and must never steal slots from interactive chat at dinner time (lesson 004).
- **So this means:** a queue-driven batch pipeline triggered by recipe-change events, idempotent per `(recipe_id, version)`, with summaries cached and served from the database.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Estimate the daily cost of an LLM chat feature with 1M DAU, 8 messages each, 2,000 input and 300 output tokens per message, at $1 and $4 per 1M tokens."**
<details><summary>Model answer</summary>

8M messages/day. Input: 16B tokens × $1/1M = **$16k**. Output: 2.4B × $4/1M = **$9.6k**. **≈ $25.6k/day (~$770k/month).** Biggest lever: input tokens. Trim and cache the prompt, and summarize the history. Follow-up: self-hosting break-even depends on keeping GPUs busy (lesson 005).
</details>

**Q2. "Why might doubling a GPU's FLOPS barely improve token generation speed?"**
<details><summary>Model answer</summary>

Decode is **memory-bandwidth-bound**: each step reads all weights and the KV cache. With the same bandwidth, TPOT barely changes. More FLOPS helps **prefill** (TTFT) and large batches, not single-stream decode.
</details>

**Q3. "A conversation hit the context limit and the model forgot a critical instruction. What went wrong and how do you fix it?"**
<details><summary>Model answer</summary>

The prompt was truncated from the front, dropping the system prompt. Fix: **budget the context by section**, pin the system prompt and critical user facts (stored outside the chat), keep a sliding window, summarize older turns, and reserve output space.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [006 · Non-Determinism](006-non-determinism.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [002](002-life-of-an-llm-request.md) and [005](005-gpu-napkin-math.md), then retry tomorrow. That's normal, not failure. |

---

⬅️ [005 · GPU Napkin Math](005-gpu-napkin-math.md) · 🗺️ [Phase map](README.md) · ➡️ [006 · Non-Determinism & Probabilistic Outputs](006-non-determinism.md)

✅ Tick **Checkpoint 10%** in [PROGRESS.md](../../PROGRESS.md). 🎉
