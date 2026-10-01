# ✅ Checkpoint 90%: Production-Grade AI

> ⏱ 25 min · Covers lessons **041–045** · 📈 You're at **90%**
>
> `██████████████████░░` 🎉 Ninety percent! You can secure, budget, harden, and ship AI systems, and design one at ChatGPT scale.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *Maya walked out of her first design review with the panel's nods. Four cards to go. Let's make sure you'd have walked out the same way.*

---

## ⚡ Part 1: Recall (5 questions)

1. What combination must never share one model context?
<details><summary>Reveal Answer</summary>

Private data, untrusted content, and a channel to send data out.
</details>

2. Name four cost levers.
<details><summary>Reveal Answer</summary>

Any four of: routing to smaller models, caching (prefix/exact/semantic), shorter prompts, shorter outputs, batch APIs or off-peak capacity, self-hosting at high utilization.
</details>

3. Why time out on TTFT and stream gaps?
<details><summary>Reveal Answer</summary>

Degraded models hang rather than fail, and answers legitimately take seconds, so only TTFT and gap timeouts fail fast.
</details>

4. What are the stages of a safe prompt rollout?
<details><summary>Reveal Answer</summary>

Eval gate → shadow → canary (1/10/50/100%) behind a flag, with automatic rollback on quality, safety, latency, or cost SLIs.
</details>

5. How do you estimate concurrent streams for a chat assistant?
<details><summary>Reveal Answer</summary>

Peak messages per second × stream lifetime (TTFT + output tokens × TPOT).
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 4-minute timer. Explain to an imaginary 12-year-old:

> "Why can't you just tell an AI 'don't follow instructions hidden in emails', and what do you do instead?"

Aim to naturally use: **the gullible assistant**, **no keys, no stamps (least privilege)**, **the mail room (closed output channels)**, and **the manager's signature (approvals)**.

---

## 🛠️ Part 3: Mini-design

**"Summarize my week" for business customers.** Every Monday, each of 12,000 kitchens gets an AI summary of last week's orders, reviews, and supplier emails, delivered by email.

On paper:
1. Where's the injection risk, and how do you contain it?
2. How do you schedule and pay for it?
3. What happens if the model provider is down on Monday morning?

<details><summary>One good answer</summary>

- **Injection:** reviews and supplier emails are untrusted. Summarize them in **quarantined no-tools calls** producing structured outputs. The final summary step has no tools, no links or images from unknown domains, and only this tenant's data (filters from identity).
- **Scheduling and cost:** a durable batch workflow on Sunday night via a **batch API** or off-peak self-hosted capacity (~50% cheaper), idempotent per `(kitchen_id, week)`, metered per tenant. Order statistics computed deterministically, with the model only phrasing them.
- **Outage:** generated Sunday night, so Monday's delivery doesn't depend on the model. If generation fails, retry with a fallback model, and as a last resort send a deterministic stats-only email.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "A tenant's agent loop cost $41k before anyone noticed. What controls were missing?"**
<details><summary>Model answer</summary>

Per-task budgets in code, per-tenant token quotas, hourly spend anomaly alerts against a baseline, and attribution by tenant and feature at the gateway.
</details>

**Q2. "Design the fallback strategy for an AI feature on the checkout page."**
<details><summary>Model answer</summary>

Make it asynchronous and time-boxed so checkout never waits. TTFT timeouts, a breaker, a fallback to a smaller model or a cached suggestion, and simply hiding the feature when unavailable.
</details>

**Q3. "Why does conversation-affinity routing matter at ChatGPT scale?"**
<details><summary>Model answer</summary>

Follow-up turns reuse most of the previous prompt. Routing them to the replica holding that prefix's KV cache avoids recomputing it, cutting prefill compute (often by over half) and TTFT.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [046 · Design Enterprise Document Q&A](046-design-enterprise-rag.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [041](../05-quality-safety-ops/041-prompt-injection-security.md) and [043](../05-quality-safety-ops/043-ai-reliability.md), then retry tomorrow. |

---

⬅️ [045 · Design a ChatGPT-Style Assistant](045-design-chatgpt.md) · 🗺️ [Phase map](README.md) · ➡️ [046 · Design Enterprise Document Q&A](046-design-enterprise-rag.md)

✅ Tick **Checkpoint 90%** in [PROGRESS.md](../../PROGRESS.md). 🎉
