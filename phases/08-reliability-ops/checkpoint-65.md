# ✅ Checkpoint 65%: Designing for Failure

> ⏱ 15 min · Covers lessons **061–065** · 📈 You're at **65%**
>
> `█████████████░░░░░░░` Nearly two-thirds! You now think like an SRE: assume everything fails, and design so users barely notice.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Pantry now survives single failures, and Priya's pager is quiet for once. Time to check what you've learned.*

---

## ⚡ Part 1: Recall (5 questions)

1. Backpressure vs load shedding?
<details><summary>Answer</summary>

Backpressure tells upstream to slow down or wait (bounded buffers, flow control). Load shedding deliberately rejects excess work (fast 429/503).
</details>

2. What problem does the transactional outbox solve?
<details><summary>Answer</summary>

The dual-write problem: keeping a DB change and its published event consistent despite crashes.
</details>

3. Write the full-jitter backoff formula.
<details><summary>Answer</summary>

`sleep = random(0, min(cap, base × 2^attempt))`
</details>

4. Name the three circuit breaker states.
<details><summary>Answer</summary>

Closed (normal), Open (fail fast), Half-open (trial requests).
</details>

5. What's a failure domain, and why does it matter for redundancy?
<details><summary>Answer</summary>

A set of components that fail together (a rack, AZ, or region). Redundant copies must live in different domains, or one event kills them all.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "Why does a website stay mostly working even when some of its parts break?"

Must include: **timeouts, retries with random waits, circuit breaker, bulkheads, redundant copies**.

---

## 🛠️ Part 3: Mini-design

**A travel-booking page** calls: flights search (critical), hotel search (critical), weather widget (nice to have), personalized deals (nice to have). The weather API is often slow.

Design the resilience configuration: timeouts, retries, breakers, bulkheads, fallbacks, and redundancy.

<details><summary>One good answer</summary>

- **Flights/hotels:** timeout ~800 ms each (called in parallel), 1 retry with jitter (idempotent searches), a breaker per provider with a fallback to cached results ("prices may have changed"), a dedicated pool (bulkhead) of 50 each, and redundant providers where possible.
- **Weather:** timeout 200 ms, no retry, breaker opens quickly, fallback hides the widget, tiny pool of 5.
- **Deals:** timeout 300 ms, fallback to generic deals, small pool.
- **Redundancy:** app servers across 3 AZs, and a multi-AZ cache for search results.
- The page renders critical content first. Non-critical widgets load async client-side.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "What is a cascading failure, and how do you prevent one?"**
<details><summary>Model answer</summary>

One component's failure or slowness propagates: callers' resources are exhausted waiting → they fail → their callers fail. Prevent it with timeouts, bulkheads, circuit breakers, load shedding, backoff with jitter, and capacity headroom.
</details>

**Q2. "Active-active vs active-passive for a database?"**
<details><summary>Model answer</summary>

Active-passive (primary + standby) is typical: simple consistency, with a failover delay of seconds to minutes. Active-active multi-writer needs conflict handling (multi-leader, CRDTs) or a distributed consensus database (Spanner/CockroachDB), at higher complexity and latency.
</details>

**Q3. "Retries caused an outage. How?"**
<details><summary>Model answer</summary>

Synchronized, unbounded retries (possibly multiplied across layers) pushed the load above capacity after a small blip, creating a self-sustaining overload (a metastable failure). Fix with backoff + jitter, retry budgets, single-layer retries, and circuit breakers.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [066 · Multi-Region & Disaster Recovery](066-multi-region-and-disaster-recovery.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [063](063-timeouts-and-retries.md), [064](064-circuit-breakers-and-bulkheads.md), [065](065-redundancy-and-failover.md) |

---

⬅️ [065 · Redundancy & Failover](065-redundancy-and-failover.md) · 🗺️ [Phase map](README.md) · ➡️ [066 · Multi-Region & Disaster Recovery](066-multi-region-and-disaster-recovery.md)

✅ Tick **Checkpoint 65%** in [PROGRESS.md](../../PROGRESS.md). 🎉
