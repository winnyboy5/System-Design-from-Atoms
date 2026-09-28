# ✅ Checkpoint 95%: The Final Stretch

> ⏱ 20 min · Covers lessons **091–095** · 📈 You're at **95%**
>
> `███████████████████░` Only 5 lessons left! You can now design specialist systems: data pipelines, storage engines, search, crawlers, and geo.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Pantry's global features are shipping. Maya's journey is nearly over, and so is yours.*

---

## ⚡ Part 1: Recall (5 questions)

1. What's a watermark in stream processing?
<details><summary>Answer</summary>

A signal that events up to a given event time are believed to have arrived, so windows can close despite out-of-order data.
</details>

2. Name three self-healing mechanisms in a Dynamo-style KV store.
<details><summary>Answer</summary>

Hinted handoff, read repair, and Merkle-tree anti-entropy.
</details>

3. How does autocomplete stay under 100 ms?
<details><summary>Answer</summary>

Precomputed top-K per prefix in an in-memory trie, CDN/browser caching of short prefixes, and client debouncing.
</details>

4. How does a crawler stay polite?
<details><summary>Answer</summary>

Per-host queues bound to specific workers, per-host delays (Crawl-delay), and obeying robots.txt.
</details>

5. Why search the neighbouring geohash cells?
<details><summary>Answer</summary>

Points near a cell boundary can be in adjacent cells with different prefixes.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

5-minute timer. Explain to a friend:

> "How does a ride-sharing app find the nearest driver in a couple of seconds, and make sure two people don't get the same driver?"

Must include: **geohash cells, in-memory locations, ETA ranking, atomic reservation**.

---

## 🛠️ Part 3: Mini-design (15 minutes)

**Design "Find nearby friends"**: users opt in to share their location, and the app shows friends within 5 km, updating every 30 s. 100M users, 10M sharing at any time.

<details><summary>One good answer</summary>

- **Location updates:** 10M / 30 s ≈ 333k writes/s → an in-memory store keyed by user (Redis Cluster), with a TTL of ~10 min (stale sharers disappear), plus a geohash per user.
- **Query:** for each user, check **their friends' locations** (friends lists are ~a few hundred) rather than a geo scan. Batch-get the friends' locations and filter by distance. That's cheaper than scanning cells, because the friend graph limits the candidates.
- **Push updates:** subscribe to friends' location channels via pub/sub (WebSocket gateways), and only while the app is open.
- **Privacy:** opt-in, coarse location options, per-friend visibility, and no history retention (or strict retention).
- Scale: shard by user ID, and fan out updates only to online friends.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Batch or stream for computing driver earnings?"**
<details><summary>Model answer</summary>

Stream for near-real-time earnings displays (approximate, updated per trip event). Batch for official payouts and statements (exact, reconciled with payments, recomputable). A classic Kappa/Lambda-style split by purpose.
</details>

**Q2. "Your KV store uses LWW and users report lost updates. What do you change?"**
<details><summary>Model answer</summary>

Switch to version vectors with sibling resolution or CRDTs for mergeable data. Route writes for a key through a single owner (a leader per partition). Or use conditional writes (compare-and-set on a version) for read-modify-write patterns.
</details>

**Q3. "How do you keep autocomplete from suggesting offensive or private queries?"**
<details><summary>Model answer</summary>

Filter at build time (blocklists, ML classifiers), only include queries above a frequency threshold from many distinct users (so one person's private query never becomes a suggestion), provide a manual removal pipeline, and personalize only from the user's own history.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [096 · Design a Payment System](096-design-payment-system.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [091](091-batch-vs-stream-processing.md), [092](092-design-key-value-store.md), [095](095-design-proximity-service.md) |

---

⬅️ [095 · Proximity Service](095-design-proximity-service.md) · 🗺️ [Phase map](README.md) · ➡️ [096 · Payment System](096-design-payment-system.md)

✅ Tick **Checkpoint 95%** in [PROGRESS.md](../../PROGRESS.md). 🎉
