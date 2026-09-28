# 🎓 Checkpoint 100%: Mastery

> ⏱ 45 min · Covers **everything (001–100)** · 📈 You're at **100%**
>
> `████████████████████` 🎓🎉🏆 **You finished the entire guide.** From "what is a server?" to consensus, sagas, and designing payment systems. That's a genuine achievement. Be proud.

> 📖 *Maya's story is over. She closes her laptop, but you're about to open yours. One last checkpoint.*

---

## ⚡ Part 1: The final recall (10 questions, one per phase)

1. **[Foundations]** Your system has 4 dependencies in series, each 99.9%. What's the availability, and how do you raise it?
<details><summary>Answer</summary>

~99.6%. Add redundancy (parallel copies), make non-critical dependencies soft (cache, fallbacks, async), and reduce the number of hard synchronous dependencies.
</details>

2. **[Networking]** When do you choose SSE over WebSockets?
<details><summary>Answer</summary>

When updates flow only server → client (notifications, live scores, LLM token streams). It's simpler and HTTP-friendly.
</details>

3. **[Scaling]** What does an API gateway do that a load balancer doesn't?
<details><summary>Answer</summary>

API-level policies across many services: auth, rate limiting, routing by API, transformations, and aggregated logging.
</details>

4. **[Caching]** Describe the stale-set race and one fix.
<details><summary>Answer</summary>

A reader loads the old value, a writer updates the DB and deletes the key, and then the reader sets the old value into the cache. Fix: leases, version-checked sets, delayed double delete, or a short TTL.
</details>

5. **[Databases]** Why do random UUID primary keys hurt write performance?
<details><summary>Answer</summary>

Random insert positions in the B-tree cause page splits, poor cache locality, and more I/O. Use time-ordered IDs.
</details>

6. **[Scaling data]** R + W > N: what does it guarantee, and what doesn't it?
<details><summary>Answer</summary>

Read and write quorums overlap, so reads see the latest successful write. It's not full linearizability under sloppy quorums, concurrent writes, or partial failures.
</details>

7. **[Async]** How do you publish an event reliably when updating a database?
<details><summary>Answer</summary>

The transactional outbox: write the event row in the same transaction, then relay it via polling or CDC. Consumers are idempotent.
</details>

8. **[Reliability]** Why add jitter to retries, and why retry at only one layer?
<details><summary>Answer</summary>

Jitter prevents synchronized retry waves. Retrying at multiple layers multiplies the load (3 layers × 3 retries = 27×).
</details>

9. **[Deep internals]** What problem do fencing tokens solve?
<details><summary>Answer</summary>

A stale lock holder (paused or partitioned past its lease) writing after another node acquired the lock. The resource rejects lower tokens.
</details>

10. **[Advanced designs]** How do you handle a payment API timeout?
<details><summary>Answer</summary>

Treat it as an unknown outcome: mark it pending, and retry with the same idempotency key or query the PSP / wait for the webhook. Never charge again with a new key. Reconcile daily.
</details>

**Score: ___ / 10**

---

## 🎤 Part 2: The final mock (45 minutes)

Pick a **capstone prompt** from [lesson 100](100-capstone.md) that you haven't done yet. Run it as a full interview, ideally with a friend playing interviewer, who injects **one twist** at minute 20. Score it with the [🏁 80% rubric](../09-core-case-studies/checkpoint-80.md).

| Rubric total | Level |
|---|---|
| 18–20 | 🏆 **Staff-level reasoning.** You can lead design discussions. |
| 15–17 | 🎓 **Senior-level mastery.** Keep practising the deep dives. |
| 11–14 | ✅ **Solid.** Revisit the weak rows and repeat in two weeks. |

---

## 🧭 Part 3: Staying sharp (spaced review plan)

Mastery fades without practice. Here's an ADHD-friendly maintenance plan:

| When | Do (≤ 30 min) |
|---|---|
| **Weekly** | Reread one phase's 📌 cheatsheet + answer its 🎤 🟡 questions out loud |
| **Every 2 weeks** | One 45-minute mock interview (rotate the prompts) |
| **Monthly** | One capstone design doc + teach it to someone |
| **Quarterly** | Skim [NUMBERS.md](../../cheatsheets/NUMBERS.md), [TRADEOFFS.md](../../cheatsheets/TRADEOFFS.md), [PATTERNS.md](../../cheatsheets/PATTERNS.md) |

---

## 📚 Where to go deeper next

- 📘 **"Designing Data-Intensive Applications"** (Martin Kleppmann): the definitive deep dive into Phases 5, 6, 7, and 10.
- 📗 **"System Design Interview" Vol. 1 & 2** (Alex Xu): more case studies in interview format.
- 📙 **Google SRE books** (free online): reliability, SLOs, and on-call (Phases 1 and 8).
- 📄 **Classic papers:** Dynamo, Bigtable, GFS, MapReduce, Spanner, Raft, Kafka, "The Tail at Scale".
- 🛠️ **Build something:** a toy KV store with Raft, a URL shortener with Redis + Postgres, or a Kafka consumer pipeline. **Building beats reading.**
- 📰 **Engineering blogs:** Netflix, Uber, Discord, Stripe, Cloudflare, Slack, Figma, Shopify.

---

## 🎉 Final words

You started with atoms: a request, a byte, a cache hit. You now see how they compose into systems serving billions of people. More importantly, you practised **explaining** each idea simply, which is the real test of understanding.

> "What I cannot create, I do not understand." (Richard Feynman)

Now go **create**. 🚀

---

⬅️ [100 · Capstone](100-capstone.md) · 🗺️ [Phase map](README.md) · 🏠 [Home](../../README.md)

✅ Tick **🎓 Checkpoint 100%** in [PROGRESS.md](../../PROGRESS.md). 🎓🎉🏆
