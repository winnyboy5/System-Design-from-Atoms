# 🏁 Checkpoint 80%: The Practical Mastery Gate

> ⏱ 30–45 min · Covers **all of Part A (lessons 001–080)** · 📈 You're at **80%**
>
> `████████████████░░░░` 🏆🏆🏆 **YOU MADE IT TO 80%.** This is the realistic mastery target: you can now handle most system design interviews and real-world design discussions.

This gate is a **full mock interview** plus a **self-audit**. Treat it like the real thing: set a timer, talk out loud, and draw on paper.

---

## ⚡ Part 1: Rapid-fire recall (10 questions, 10 minutes)

1. The latency ladder in four numbers?
<details><summary>Answer</summary>

RAM ~100 ns, SSD ~100 µs, same-DC round trip ~0.5 ms, cross-continent ~100 ms.
</details>

2. 1B requests/day ≈ how many QPS?
<details><summary>Answer</summary>

~12k/s average (10⁹ ÷ 10⁵ = 10k, and the precise figure is ~11.6k), with a peak ~2–3×.
</details>

3. Three things that make a service horizontally scalable?
<details><summary>Answer</summary>

Statelessness (state in Redis/DB/S3), a load balancer with health checks, and idempotent/retry-safe operations (also: externalized config and no local cron).
</details>

4. Cache-aside write path?
<details><summary>Answer</summary>

Update the DB, then delete the cache key (with a TTL as a safety net).
</details>

5. When do you shard, and what makes a good shard key?
<details><summary>Answer</summary>

When one node can't hold the data or handle the write load. A good key has high cardinality, is evenly distributed, answers common queries in one shard, and is stable.
</details>

6. CAP in one sentence?
<details><summary>Answer</summary>

During a network partition, you must choose between consistency and availability.
</details>

7. R + W > N means?
<details><summary>Answer</summary>

Read and write quorums overlap, so reads see the latest successful write.
</details>

8. How do you get exactly-once effects from an at-least-once queue?
<details><summary>Answer</summary>

Idempotent consumers: dedupe by message ID in the same transaction as the state change.
</details>

9. The resilience stack around a remote call?
<details><summary>Answer</summary>

Timeout → retry with backoff + jitter (idempotent only) → circuit breaker → bulkhead → fallback.
</details>

10. Fan-out on write vs read for a feed, and what about celebrities?
<details><summary>Answer</summary>

Push precomputes feeds (fast reads, expensive writes). Pull assembles at read time. Hybrid: push for normal users, pull for celebrities.
</details>

**Score: ___ / 10**

---

## 🎤 Part 2: Full mock interview (45 minutes, timed)

Pick **one** prompt you haven't studied as a case study. Use the [framework](073-design-framework.md) and the [time budget](../../cheatsheets/INTERVIEW-FRAMEWORK.md).

| Prompt | Hints (only if you get stuck!) |
|---|---|
| **A. Design Instagram** (photos, follows, feed) | Media → S3 + CDN · feed = hybrid fan-out · counters = write-back · IDs = Snowflake |
| **B. Design a food-delivery app** (order, match courier, live tracking) | Orders = SQL + transactions · couriers' locations = Redis GEO · matching service · events via Kafka · push notifications |
| **C. Design a collaborative task board** (Trello-like) | Postgres by workspace · WebSockets for live updates · optimistic concurrency (versions) · activity feed via events |
| **D. Design a ticket booking system for concerts** | Seat holds with TTL · strong consistency on seats · virtual waiting room · idempotent payments |

**Record yourself** (audio is fine) and play it back. You'll hear the gaps instantly. That's the Feynman loop at interview scale.

---

## 📋 Part 3: Self-audit rubric

Score each 0 (missed), 1 (partial), or 2 (strong):

| Skill | Score |
|---|---|
| Clarified functional + non-functional requirements (with numbers) | /2 |
| Did quick estimates and **stated the implications** | /2 |
| Defined a clean API and data model, and justified the storage choice | /2 |
| Drew a simple high-level design, walking one read path + one write path | /2 |
| Identified the real bottleneck(s) and scaled them (cache, shard, async) | /2 |
| Discussed consistency choices explicitly (where strong, where eventual) | /2 |
| Handled failures (retries, idempotency, redundancy, degradation) | /2 |
| Stated trade-offs out loud ("A gives X, costs Y, I choose A because…") | /2 |
| Mentioned observability and security | /2 |
| Communicated clearly, checked in, and managed time | /2 |

**Total: ___ / 20**

| Total | Meaning |
|---|---|
| 16–20 | 🏁 **Practical mastery.** You're interview-ready for most roles. Part B is your senior/staff edge. |
| 11–15 | 🟡 Solid base. Redo one more mock with a different prompt, focusing on your 0/1 rows. |
| ≤ 10 | 🔁 Revisit the phase cheatsheets for your weak rows, then retry the mock in a few days. Totally normal. |

---

## 🗺️ Part 4: Where your weak spots map

| Weak row | Revisit |
|---|---|
| Requirements / estimates | [005](../01-foundations/005-back-of-envelope-estimation.md), [008](../01-foundations/008-requirements.md) |
| Storage choice / data model | [Phase 05 cheatsheet](../05-databases/CHEATSHEET.md), [045](../05-databases/045-choosing-a-database.md) |
| Scaling | [Phase 03](../03-scaling-basics/CHEATSHEET.md), [Phase 04](../04-caching/CHEATSHEET.md), [Phase 06](../06-scaling-data/CHEATSHEET.md) cheatsheets |
| Consistency | [052](../06-scaling-data/052-cap-theorem.md), [053](../06-scaling-data/053-consistency-models.md) |
| Failure handling | [Phase 08 cheatsheet](../08-reliability-ops/CHEATSHEET.md) |
| Async design | [Phase 07 cheatsheet](../07-async-messaging/CHEATSHEET.md) |

---

## 🎉 Celebrate!

You've covered the **core 80%** of system design: the part that shows up in nearly every interview and most real projects. Seriously, take a victory lap. 🏆

**What's next?**
- **Part B (81–100%)** goes deeper: database internals, consensus, clocks, and specialist designs (payments, geo, crawlers, schedulers). It's great for senior/staff interviews and infrastructure roles.
- Or pause here, and do **one mock interview per week** to keep your skills sharp. Spaced practice beats cramming.

---

⬅️ [080 · File Storage & Sync](080-design-file-sync.md) · 🗺️ [Phase map](README.md) · ➡️ [🅱️ 081 · B-Trees, LSM Trees & WAL](../10-deep-internals/081-btree-lsm-and-wal.md)

✅ Tick **🏁 Checkpoint 80%** in [PROGRESS.md](../../PROGRESS.md). 🏆
