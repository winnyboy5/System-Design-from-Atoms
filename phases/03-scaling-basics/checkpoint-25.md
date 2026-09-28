# ✅ Checkpoint 25%: A Quarter of the Way!

> ⏱ 15 min · Covers lessons **021–025** · 📈 You're at **25%**
>
> `█████░░░░░░░░░░░░░░░` 🥳 **25%!** You can now design the "front half" of almost any web system.

**Rules:** answer out loud or on paper **before** opening answers.

---

## ⚡ Part 1: Recall (5 questions)

1. What can an L7 load balancer do that an L4 one can't?
<details><summary>Answer</summary>

Route by URL path, host, header, or cookie. Retry per request. Do HTTP-aware health checks, rewrites, auth, and caching.
</details>

2. Name three jobs of an API gateway.
<details><summary>Answer</summary>

Any three of: auth, rate limiting, routing, logging/metrics, request transformation, caching, CORS.
</details>

3. How do versioned filenames help CDN caching?
<details><summary>Answer</summary>

Each change gets a new URL, so assets can be cached forever and never need purging.
</details>

4. In a token bucket, what do "rate" and "capacity" control?
<details><summary>Answer</summary>

Rate: the long-term allowed requests per second. Capacity: the maximum burst size.
</details>

5. What metric should you autoscale a queue-worker fleet on?
<details><summary>Answer</summary>

Queue depth or consumer lag.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "How does Netflix show you a movie fast, even though you live far from their main computers, and how do they stop one person from spamming their servers?"

Must include: **CDN edge, cache hit/miss, origin, rate limiting**.

---

## 🛠️ Part 3: Mini-design

**A news website** gets 100× traffic when breaking news hits. The pages are the same for every reader. There's also a comments API.

Sketch the design so the site survives the spike.

<details><summary>One good answer</summary>

- **CDN** caches article HTML with `s-maxage=30, stale-while-revalidate`. Almost all reads never touch the origin.
- Images and JS/CSS: object storage + CDN, with versioned filenames.
- **L7 LB / gateway** → article service (rarely hit) and comments service.
- **Comments:** rate limit per user/IP (token bucket at the gateway). Writes go to a **queue** so spikes are absorbed. Reads are cached for a few seconds.
- **Autoscaling** of the comments service on RPS, with a pre-scale hook when the editors publish a breaking story.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "What's the difference between an API gateway and a load balancer? Do you need both?"**
<details><summary>Model answer</summary>

An LB spreads traffic across instances of a service (L4/L7). A gateway is an API-aware front door for many services, adding auth, rate limits, and transforms. Often you use both: edge LB → gateway fleet → internal LBs or service discovery → services.
</details>

**Q2. "How do you keep a CDN from serving stale prices?"**
<details><summary>Model answer</summary>

Short `s-maxage` for price-bearing responses, tag-based purges when prices change, or keeping prices out of the cached shell and fetching them dynamically. The trade-off is hit ratio vs freshness.
</details>

**Q3. "Autoscaling added 100 pods and the database fell over. What went wrong, and how do you prevent it?"**
<details><summary>Model answer</summary>

Each pod opened its own DB connection pool, so the connection count exploded, or the query load grew beyond DB capacity. Prevent it with max replica caps, a connection pooler (PgBouncer), read replicas and caching, and load testing the whole chain, not just the app tier.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [026 · Monolith vs Microservices](026-monolith-vs-microservices.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [021](021-l4-l7-and-proxies.md), [023](023-cdn.md), [024](024-rate-limiting.md) |

---

⬅️ [025 · Autoscaling & Containers](025-autoscaling-and-containers.md) · 🗺️ [Phase map](README.md) · ➡️ [026 · Monolith vs Microservices](026-monolith-vs-microservices.md)

✅ Tick **Checkpoint 25%** in [PROGRESS.md](../../PROGRESS.md). 🎉
