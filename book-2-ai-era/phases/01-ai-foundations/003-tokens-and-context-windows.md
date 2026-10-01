# 003 · Tokens & Context Windows

> ⏱ 11 min · 📈 6% · 🅰️ AI-era core · Phase 01: AI Foundations for System Designers
>
> `█░░░░░░░░░░░░░░░░░░░` 6% of Book 2
>
> 🧬 **Atoms used:** estimation [B1·005] · caching basics [B1·027] · [001] · [002]

---

## 📖 Story

A loyal customer has been chatting with "Ask the Chef" all evening. Forty-one messages: a dinner party, a vegan cousin, a shellfish allergy mentioned in **message 2**.

At **message 42**, they ask for a dessert. The assistant suggests a **prawn-cracker crumble**.

Maya pulls the request log. The prompt has grown to **131,000 tokens**, just past the model's limit. Her code handled the overflow the simple way: it **chopped tokens off the front**. And the front was where the system prompt lived ("Always respect stated allergies"), along with message 2.

Worse, the bill for that one conversation is **$19**. Every message re-sent the **entire history** as input, so the conversation paid for message 1 forty-two times.

I told Maya that a context window isn't a bag you keep stuffing. It's a **budget**, and you allocate it like a principal engineer allocates memory. Let me show you how.

## 🎯 One-sentence idea

**A model sees only the tokens in its context window (its entire working memory for one request), and because the whole conversation is re-sent every turn, the context must be treated as a budget: split into fixed sections, trimmed by policy, and summarized or retrieved instead of growing forever.**

## 🧸 Analogy

A **chef's order slip** that has a fixed number of lines:

- The **top lines** are the house rules ("check allergies!"), and they must never be torn off.
- Every time the table speaks, the waiter **rewrites the whole slip** with the new line added, and the chef re-reads it all (re-sending history).
- When the slip is full, a sloppy waiter tears off the top. A good waiter **writes a short summary** of the old lines ("shellfish allergy, vegan cousin") and keeps the rules.

## 🖼️ Visual

*Diagram brief:* a horizontal bar representing a 128k-token context window, divided into labelled budget sections. Pinned sections (system prompt, safety rules) sit on the left, then retrieved facts, a summary of old turns, recent turns, and a reserved slice for the answer on the right.

```
┌──────────── 128,000-token context window ─────────────────────────────┐
│ System + rules │ Retrieved facts │ Summary of │ Last 10 turns │ Answer │
│   📌 pinned    │   (RAG, ≤ 4k)   │ old turns  │   (≤ 6k)      │reserve │
│    ~800        │                 │  (≤ 1k)    │               │ (≤ 1k) │
└───────────────────────────────────────────────────────────────────────┘
  never truncated   ranked, capped    rolling      sliding window   max_tokens
```

## 🔬 How it works

- **Tokens:** text is split into sub-word pieces. English ≈ **4 characters or ¾ of a word per token**. Code, numbers, and many non-English languages use **more tokens per word**, so they cost more.
- **The context window** is the maximum tokens per request (prompt **+** output), often **128k to 1M+** today. Everything the model "knows" about this conversation must fit inside it. There is **no memory between requests** unless you send it again.
- **Chat is re-sent every turn.** Turn *k* resends turns 1…k−1, so total input tokens grow **quadratically** with conversation length. Cost and TTFT grow with them.
- **Bigger isn't free, and isn't always better.** Prefill cost grows with prompt length, the KV cache grows with it (lesson 011), and models attend less reliably to facts buried in the **middle** of very long contexts.
- **Budget the window by section:** pin the system prompt and safety rules, cap retrieved facts, keep a **sliding window** of recent turns, **summarize** older turns (or store them as retrievable memory, lesson 031), and **reserve** room for the answer. Truncate by **policy**, never blindly from the front.

## 🧩 Worked example

**The cost of re-sending history** (system prompt 500 tokens, each turn adds 300 tokens):

```
Turn k input = 500 + 300 × (k − 1)
30 turns, total input = 30 × 500 + 300 × (0 + 1 + … + 29)
                      = 15,000 + 300 × 435 = 145,500 tokens
Actual new text typed + generated ≈ 30 × 300 = 9,000 tokens → 16× overhead
```

**Maya's budgeted prompt** (a hard cap of 12k input tokens):

| Section | Budget | Policy |
|---|---|---|
| System prompt + allergy rules | 800 | Pinned, never cut |
| Customer profile (allergies, diet) | 200 | Pinned, from the database, not from chat |
| Retrieved menu facts | ≤ 4,000 | Top-ranked chunks only (lesson 021) |
| Summary of older turns | ≤ 1,000 | Re-summarized every 10 turns |
| Recent turns | ≤ 6,000 | Sliding window, newest kept |

**Message 42, replayed:** the shellfish allergy now lives in the **pinned profile**, so the dessert suggestion is a mango sorbet. Input per message is capped at 12k tokens: **$19 → ~$1.50** for the whole 42-message evening.

## ⚖️ Trade-offs

| Strategy | You gain | You pay |
|---|---|---|
| Send the full history | Perfect recall (until it overflows) | Quadratic cost, slow TTFT, a hard limit |
| Sliding window | Bounded cost | Forgets older facts |
| Summarize old turns | Bounded cost, keeps the gist | Extra model call, details lost |
| Retrieve memories on demand | Scales to any history | Retrieval misses, more moving parts |

## 🌍 Real world

- Chat products **summarize or truncate history** behind the scenes. Long-running assistants keep **structured memory** (preferences, facts) outside the context.
- **"Lost in the middle"** research showed models recall facts at the start and end of long contexts better than the middle.
- Providers offer **token-counting endpoints and tokenizer libraries** so apps can budget before sending.

## 📌 Cheat card

> - **1 token ≈ 4 chars ≈ ¾ word** (English). Code and other languages cost more.
> - **Context = prompt + output**, and it's the model's entire memory for the request.
> - **Chat history re-sent each turn → quadratic input tokens.**
> - **Budget by section. Pin the rules. Summarize or retrieve the old stuff.**
> - **Never truncate blindly from the front.**

## 🧪 Feynman check

Explain the order slip with a fixed number of lines: why the waiter rewrites it every time, and why tearing off the top is the worst thing they can do.

⚠️ **Common confusion:** "A 1M-token context window means we can just send everything." You *can*, but you pay for every token on **every** turn, TTFT climbs, and recall of facts in the middle degrades. A big window is a ceiling, not a strategy.

## ⚡ Quick recall

1. Why does the cost of a chat grow quadratically with its length?
<details><summary>Reveal Answer</summary>

Every turn re-sends the entire previous history as input tokens, so turn k pays for all k−1 earlier turns again.
</details>

2. What should never be truncated from a prompt?
<details><summary>Reveal Answer</summary>

The system prompt and safety rules (and critical user facts like allergies), which should be pinned and stored outside the chat history.
</details>

3. Name two ways to keep a long conversation within budget.
<details><summary>Reveal Answer</summary>

Any two of: a sliding window of recent turns, summarizing older turns, or storing facts as memory and retrieving them when relevant.
</details>

## 🎤 Interview practice

**Q. "Design conversation memory for a chat assistant whose users have 500-message histories, with input capped at 16k tokens per request."**
<details><summary>Model answer</summary>

- **Budget the 16k:** system prompt (~1k, pinned), user profile facts (~0.5k, from a DB), retrieved knowledge (~4k), memory (~2k), recent turns (~7k), and a reserve for the answer.
- **Three memory tiers:**
  - **Short-term:** the last N turns verbatim, a sliding window.
  - **Rolling summary:** every ~20 turns, a small model compresses older turns into ~1k tokens, stored with the conversation.
  - **Long-term:** extract durable facts ("allergic to shellfish", "cooks for four") into a **structured profile**, plus embed past turns for **retrieval** when a new question relates to them.
- **Storage:** messages in a document/wide-column store keyed by `(user_id, conversation_id, seq)`. Summaries and facts are versioned so they can be audited and corrected.
- **Cost:** input per request is bounded at 16k regardless of history length, so cost is **linear in messages**, not quadratic.
- **Likely follow-up:** "What if the summary drops something important?" → safety-critical facts never live only in the summary: they're extracted into the pinned profile, and the user can view and edit them.
</details>

## 📖 Teaser

> 📖 *The prompt finally fits its budget, and the dashboard says the average answer time is "fine". So why are customers still tapping "send" three times?*

---

⬅️ [002 · The Life of an LLM Request](002-life-of-an-llm-request.md) · 🗺️ [Phase map](README.md) · ➡️ [004 · AI Latency Numbers: TTFT, TPOT & Tokens/s](004-ai-latency-numbers.md)

✅ **Safe stopping point.** Tick lesson 003 in [PROGRESS.md](../../PROGRESS.md).
