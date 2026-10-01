# 043 · AI Reliability: Fallbacks & Degraded Modes

> ⏱ 12 min · 📈 86% · 🅱️ Production & case studies · Phase 05: Quality, Safety & AI Ops
>
> `█████████████████░░░` 86% of Book 2
>
> 🧬 **Atoms used:** availability & nines [B1·006] · timeouts & retries [B1·063] · circuit breakers & bulkheads [B1·064] · multi-region [B1·066] · backpressure [B1·061] · [015] · [016]

---

## 📖 Story

Saturday, 19:14. The peak of the dinner rush. The cloud region hosting Pantry's large model **degrades**: requests don't fail, they **hang**. TTFT climbs to **40 seconds**.

And then something worse than a slow chef happens: **checkout freezes**. Months ago, someone added a friendly AI greeting to the cart page, "Great choice! Want a dessert with that?", and the page **waits for it** before rendering the "Place order" button. No greeting, no button.

For **23 minutes**, Pantry can't take orders. A **nice-to-have** AI feature took down the **must-have** core of the business. Meanwhile, the gateway's retries pile onto the struggling region, so it recovers **slower**.

I told Maya that every Book 1 resilience lesson applies to models, with one twist: AI features are usually **optional**, and the system should know it. A slow chef should mean a **simpler menu**, never a **locked front door**. Let me show you how to fail gracefully.

## 🎯 One-sentence idea

**AI reliability treats every model call as an unreliable, slow dependency: time out on TTFT, retry sparingly with jitter, trip circuit breakers, fall back through a chain of alternatives (another region, another provider, a smaller model, a cached or deterministic answer), and isolate AI features behind bulkheads so the core product works even with no AI at all.**

## 🧸 Analogy

A **restaurant whose star chef can fall ill mid-service**:

- The **front door, the till, and the menu** never depend on the star chef (bulkheads).
- If the star chef is slow, the **sous-chef** takes over with a **simpler menu** (fallback to a smaller model).
- If the kitchen is in chaos, the waiter offers **today's pre-made specials** (cached answers) or says "the chef's tasting menu is paused tonight" (degraded mode), and still **takes the order**.
- Nobody keeps **shouting orders** at the overwhelmed chef (breakers, no retry storms).

## 🖼️ Visual

*Diagram brief:* a ladder of fallbacks from top to bottom: the primary large model in region A, the same model in region B, a second provider, a small self-hosted model, cached or templated answers, and finally the non-AI experience. Each rung is labelled with its quality level and the trigger to move down. To the side, a bulkhead wall separates the "core" (browse, cart, checkout) from the "AI" features.

```
FALLBACK LADDER (move down when TTFT timeout / breaker open)
 1. Large model · region A        ★★★★★   primary
 2. Large model · region B        ★★★★★   +40 ms
 3. Provider B equivalent model   ★★★★☆   own prompt variant, eval'd
 4. Small self-hosted model       ★★★☆☆   simpler answers
 5. Cached / templated answers    ★★☆☆☆   FAQs, top recommendations
 6. No AI: classic search + menus ★☆☆☆☆   "The chef is resting. Search works!"

BULKHEAD:  [ core: browse · cart · checkout · payments ]  ║  [ AI: chat · suggestions · greetings ]
           never waits on AI                               ║  async, optional, time-boxed
```

## 🔬 How it works

- **Classify features:** **core** (must work with no AI: ordering, payments, tracking) vs **AI-enhanced** (better with AI, fine without). Core paths never block on a model. AI calls on core pages are **asynchronous and time-boxed** (render first, enhance if it arrives in time).
- **Timeouts that fit AI:** time out on **TTFT** (e.g., 2 s) and on **inter-token gaps** (a stalled stream), not just total time, because a slow model **hangs** rather than fails (Book 1 lesson 063).
- **Retries and breakers:** retry only retryable errors, **once**, with jitter, and never into an open breaker. **Circuit breakers** per (provider, region, model) open on error rate **or** latency, protecting both you and the struggling dependency (Book 1 lesson 064).
- **Fallback chains:** another region, another provider (with its own tested prompt, lesson 015), a smaller model, cached answers, templates, then the **non-AI experience**. Each rung is **eval'd** and **exercised regularly** (chaos tests), so it works on the bad day.
- **Load shedding and priorities:** under GPU or provider pressure, shed **batch** first, then low-value AI features (greetings), and protect high-value ones (support for live orders) (Book 1 lesson 061).
- **Availability math:** independent fallbacks multiply failure probabilities, but **correlated** failures (same provider, same region) don't. Diversify the rungs.

## 🧩 Worked example

**Availability of "some useful AI answer":**

```
Primary (region A):        99.5% available
Region B (independent):    99.5% → both down: 0.005 × 0.005 = 0.0025%
Small self-hosted model:   99.9% → all three down: 0.0025% × 0.1% ≈ 0.0000025%
"Some AI answer" availability ≈ 99.99999% (if failures are truly independent)
Same provider, both regions share a control plane? → correlated → maybe only ~99.7% 😬
```

**Saturday 19:14, replayed:**

| Time | Event | Behaviour |
|---|---|---|
| 19:14:00 | Region A TTFT climbs | 2 s TTFT timeouts start firing |
| 19:14:06 | Breaker A opens (p95 TTFT > 2 s for 5 s) | Traffic → region B |
| 19:15:30 | Region B saturates | Shed greetings + nightly jobs. Chat → small model for FAQ intents |
| All along | Cart page | Renders immediately. The greeting is async with an 800 ms budget, simply skipped |

**Result:** checkout downtime **23 min → 0**. Chat quality dips on some answers for 31 minutes. Customers barely notice.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Bulkheads (AI optional on core paths) | Core always works | Async UI complexity |
| TTFT and stream-gap timeouts | Fast failure instead of hangs | Occasional premature cut-offs |
| Multi-region / multi-provider | Survives outages | Prompt variants, evals, contracts |
| Small-model fallback | Always some answer | Lower quality during incidents |
| Regular chaos testing | Fallbacks that actually work | Engineering time, controlled risk |

## 🌍 Real world

- Major model providers publish **status pages** with regular partial outages and elevated latency. Production apps route across **regions and providers** to survive them.
- AI gateways offer **automatic fallbacks** and **latency-based routing** as standard features.
- Mature products design AI as **progressive enhancement**: the classic experience works, and AI makes it better when available.

## 📌 Cheat card

> - **Core never waits on AI.** AI is progressive enhancement, async and time-boxed.
> - **Time out on TTFT and stream gaps**, not just total time.
> - **One jittered retry. Breakers per provider × region × model.**
> - **Fallback ladder:** region → provider → small model → cache/template → no AI. Eval and exercise every rung.
> - **Shed batch and low-value AI first. Diversify against correlated failures.**

## 🧪 Feynman check

Explain the star chef falling ill mid-service: why the front door must never depend on them, what the sous-chef's simpler menu is for, and why nobody should keep shouting orders at an overwhelmed kitchen.

⚠️ **Common confusion:** "Our model provider has 99.9% uptime, so we're fine." Providers degrade by getting **slow**, not just by failing, and an AI call in a **critical path** turns their bad hour into **your** outage. Reliability comes from isolation and fallbacks, not from the provider's SLA.

## ⚡ Quick recall

1. Why time out on TTFT rather than only on total time?
<details><summary>Reveal Answer</summary>

Degraded models often hang before the first token. A TTFT timeout fails fast, while a total-time timeout would wait far too long (answers can legitimately take many seconds).
</details>

2. What should a core product path do if the AI feature on it is down?
<details><summary>Reveal Answer</summary>

Render and work normally without it: AI enhancements are async and time-boxed, never blocking.
</details>

3. Why must fallback rungs be diverse?
<details><summary>Reveal Answer</summary>

Correlated failures (same provider or region, shared control planes) take down several rungs at once, so the availability gain from fallbacks disappears.
</details>

## 🎤 Interview practice

**Q. "Your AI support assistant must be available 99.95% of the time, but your model provider only offers 99.5%. Design for it."**
<details><summary>Model answer</summary>

- **Define availability:** "a useful answer or a graceful handoff within 3 s TTFT", not "the primary model answered".
- **Fallback ladder:** primary provider (two regions) → a second provider with a tested prompt variant → a self-hosted smaller model → retrieval-only answers (top KB articles) → human handoff/ticket form. Each rung is evaluated for quality.
- **Mechanics:** gateway-level TTFT and stream-gap timeouts, one jittered retry, breakers per provider/region/model on errors **and** latency, and health-based routing.
- **Independence:** different providers and infrastructure for the rungs. Self-hosted capacity in a different region.
- **Isolation:** the assistant widget is async on the help center. Contact forms and order tracking never depend on it.
- **Operations:** chaos drills (kill the primary weekly in staging, monthly in production on a slice), dashboards per rung, and alerts on fallback rates.
- **Math:** 99.5% primary with independent 99.5% and 99.9% fallbacks → well beyond 99.95%, if independence holds.
- **Likely follow-up:** "Quality drops on fallbacks. Is that OK?" → yes, if measured and bounded: track quality SLIs per rung, and keep the time on fallbacks short and visible.
</details>

## 📖 Teaser

> 📖 *Pantry survives outages now, and a routine prompt update, shipped to every user at once on a Monday, raises refusals by 4%, while a compliance audit finds customers' home addresses inside prompts sent to a provider in the wrong country.*

---

⬅️ [042 · Cost Engineering & Token Budgets](042-cost-engineering.md) · 🗺️ [Phase map](README.md) · ➡️ [044 · Privacy, PII & Model Rollouts](044-privacy-and-model-rollout.md)

✅ **Safe stopping point.** Tick lesson 043 in [PROGRESS.md](../../PROGRESS.md).
