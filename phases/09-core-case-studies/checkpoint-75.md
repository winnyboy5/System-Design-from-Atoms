# ✅ Checkpoint 75%: Three-Quarters! Your First Full Designs

> ⏱ 20 min · Covers lessons **071–075** · 📈 You're at **75%**
>
> `███████████████░░░░░` 🎉 **75%!** You've now run full designs end to end. Only 5 lessons to 🏁 Practical Mastery.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *The short links and the rate limiter are live and holding. Maya's reviewers are impressed. Let's see how you'd do.*

---

## ⚡ Part 1: Recall (5 questions)

1. Client-side vs server-side service discovery?
<details><summary>Answer</summary>

Client-side: the client queries the registry and picks an instance. Server-side: the client calls a stable address, and an LB or proxy picks the instance.
</details>

2. What's the Snowflake bit layout?
<details><summary>Answer</summary>

41 bits timestamp, 10 bits machine ID, 12 bits sequence (plus 1 sign bit).
</details>

3. The 4 steps of the design framework?
<details><summary>Answer</summary>

Requirements → estimates/API/data model → high-level design → deep dives (then wrap-up).
</details>

4. For a URL shortener, why prefer range allocation over hashing?
<details><summary>Answer</summary>

No collisions (so no DB check per write), and no per-request coordination. Servers take blocks of IDs.
</details>

5. Why is a Lua script used in a Redis rate limiter?
<details><summary>Answer</summary>

To make the check-and-update atomic across concurrent gateways.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

5-minute timer. Explain to a friend, **without notes**, the full URL shortener design: requirements, the numbers, how codes are generated, how redirects stay fast, and how analytics works.

---

## 🛠️ Part 3: Mini-design (timed: 15 minutes)

**Design Pastebin** using the framework. Write down:
1. Requirements (3 functional, 3 non-functional)
2. Estimates (writes/s, reads/s, storage/year)
3. API (3 endpoints)
4. High-level diagram
5. Two deep dives

<details><summary>Compare with a model answer</summary>

1. Create a paste, read it by URL, expiry. Read-heavy, 99.9% availability, durable.
2. 1M/day → ~12 writes/s. 10M reads/day → ~120/s (peak ~400). 10 KB avg → 10 GB/day → ~3.6 TB/year.
3. `POST /pastes`, `GET /p/{id}`, `DELETE /pastes/{id}`.
4. Client → LB → API → metadata DB (KV) + object storage (content). CDN + Redis for hot pastes.
5. ID generation (range allocation + base62, 7–8 chars). Expiry cleanup (lazy + sweeper + S3 lifecycle). Abuse prevention (size limits, rate limits, scanning).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "How would you scale the URL shortener to multiple regions?"**
<details><summary>Model answer</summary>

Redirects: regional read replicas or global tables of the KV store, plus regional caches and a CDN (reads stay local). Writes: give each region its own ID ranges (so there are no conflicts), and replicate the mappings asynchronously to other regions. A new link may take seconds to resolve in far regions, so fall back to the home region on a miss.
</details>

**Q2. "Rate limiter: global limit of 1,000 req/s per API key across 3 regions?"**
<details><summary>Model answer</summary>

Either split the budget per region (e.g., proportional to recent traffic, rebalanced periodically), which needs no cross-region calls per request, or use a central counter (adds cross-region latency, so avoid it). Accept approximate enforcement, and reconcile globally every few seconds.
</details>

**Q3. "In any design, what are your go-to deep-dive topics?"**
<details><summary>Model answer</summary>

Scaling the bottleneck (cache, shard key), hot spots, consistency choices, failure handling (idempotency, retries, failover), and one domain-specific algorithm (ID generation, ranking, geo).
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [076 · Design a News Feed](076-design-news-feed.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [072](../08-reliability-ops/072-unique-id-generation.md), [073](073-design-framework.md), [074](074-design-url-shortener.md) |

---

⬅️ [075 · Rate Limiter](075-design-rate-limiter.md) · 🗺️ [Phase map](README.md) · ➡️ [076 · News Feed](076-design-news-feed.md)

✅ Tick **Checkpoint 75%** in [PROGRESS.md](../../PROGRESS.md). 🎉
