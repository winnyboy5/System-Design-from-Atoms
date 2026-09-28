# ✅ Checkpoint 30%: 🎉 Level-Up! The Speed Layer

> ⏱ 15 min · Covers lessons **026–030** · 📈 You're at **30%**
>
> `██████░░░░░░░░░░░░░░` 🎉 **30%, nearly a third!** Caching is one of the highest-leverage skills in system design, and you're in the middle of it.

**Rules:** answer out loud or on paper **before** opening answers.

---

## ⚡ Part 1: Recall (5 questions)

1. When should a startup choose a modular monolith over microservices?
<details><summary>Answer</summary>

When the team is small, the product is changing fast, and there's no clear need for independent scaling or deploys. It's simpler, with ACID transactions and low ops overhead.
</details>

2. List the caching layers from the user to the database.
<details><summary>Answer</summary>

Browser → CDN → reverse proxy → app in-process (L1) → distributed cache (L2, Redis) → DB buffer cache.
</details>

3. Describe cache-aside reads and writes.
<details><summary>Answer</summary>

Read: check the cache. On a miss, read the DB and set the cache (with a TTL). Write: update the DB, then delete the cache key.
</details>

4. Which write strategy is fastest, and what's the risk?
<details><summary>Answer</summary>

Write-back. Data is lost if the cache fails before it flushes to the DB.
</details>

5. LRU vs LFU in one sentence each.
<details><summary>Answer</summary>

LRU evicts what hasn't been used for the longest time. LFU evicts what's been used the fewest times.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "Why do websites keep 'copies' of information, and what can go wrong with copies?"

Must include: **hit/miss, hit ratio, staleness, eviction, TTL**.

---

## 🛠️ Part 3: Mini-design

**A recipe website**: 5M recipes, 50k reads/s, about 100 recipe edits per minute. Each recipe is 10 KB. 80% of views go to 5% of recipes.

Decide: what to cache, where, the write strategy, the eviction policy and TTL, and the cache size.

<details><summary>One good answer</summary>

- Hot set: 5% × 5M = 250k recipes × 10 KB = **2.5 GB** → fits in a small Redis cluster (with replicas).
- **CDN** for images and public recipe pages (short `s-maxage`, e.g., 60 s).
- **Redis cache-aside** for recipe data. On edit: update the DB → delete the key (and purge the CDN by tag).
- Eviction: **allkeys-lfu** (popular recipes stay popular). TTL of 1 h + jitter as a safety net.
- Expected: a ~95%+ hit ratio → the DB sees ~2.5k reads/s, which is manageable.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Why delete the cache key on update instead of setting the new value?"**
<details><summary>Model answer</summary>

Concurrent writers can race: writer A sets v1 after writer B set v2, so the cache holds stale v1 indefinitely. Deleting means the next read loads the current DB value. The remaining small races are bounded by the TTL.
</details>

**Q2. "Your cache cluster restarts and everything slows to a crawl. Why, and how do you prevent it?"**
<details><summary>Model answer</summary>

A cold cache → 100% misses → the DB is overwhelmed (maybe cascading). Prevent it with replicas and persistence (Redis AOF/RDB), rolling restarts, cache warming, request coalescing, and DB protection (rate limiting, load shedding).
</details>

**Q3. "How do you size a cache?"**
<details><summary>Model answer</summary>

Estimate the hot working set (e.g., 20% of daily-accessed data × item size), add overhead (~1.5–2× for keys and metadata), add replicas, then validate with hit-ratio monitoring and evictions/s.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [031 · Cache Invalidation](031-cache-invalidation.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [028](028-cache-aside-and-read-through.md), [029](029-cache-write-strategies.md), [030](030-cache-eviction.md) |

🏆 **Level-up reward:** 30%. Time for a proper break. You've earned it.

---

⬅️ [030 · Cache Eviction](030-cache-eviction.md) · 🗺️ [Phase map](README.md) · ➡️ [031 · Cache Invalidation](031-cache-invalidation.md)

✅ Tick **Checkpoint 30%** in [PROGRESS.md](../../PROGRESS.md). 🎉
