# ✅ Checkpoint 5%: Speaking the Language of Speed

> ⏱ 15 min · Covers lessons **001–005** · 📈 You're at **5%**
>
> `█░░░░░░░░░░░░░░░░░░░` 🎉 First checkpoint! Tiny wins add up.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking. Peeking turns recall into rereading.

> 📖 *Maya pinned her napkin math to the fridge. Before I go on with the story, I want to make sure you'd have done the same math.*

---

## ⚡ Part 1: Recall (5 questions)

1. Put these in order from fastest to slowest: SSD read, cross-continent round trip, RAM read, datacenter round trip.
<details><summary>Answer</summary>

RAM (~100 ns) → SSD (~100 µs) → datacenter RTT (~0.5 ms) → cross-continent RTT (~100 ms).
</details>

2. Why is p99 more useful than the average?
<details><summary>Answer</summary>

Averages hide slow outliers. p99 shows what the slowest 1% of requests experience, and at scale that's many real users.
</details>

3. State Little's Law and what it's used for.
<details><summary>Answer</summary>

L = λ × W (in-flight = throughput × latency). It's used to size connection pools, thread pools, and worker counts.
</details>

4. 20M DAU × 10 requests each per day = how many QPS on average, and at peak?
<details><summary>Answer</summary>

2 × 10⁸ ÷ 10⁵ = **2,000 QPS avg**, ~4,000–6,000 peak.
</details>

5. List the hops in the life of a web request.
<details><summary>Answer</summary>

DNS → TCP handshake → TLS handshake → HTTP request → (CDN/LB) → app server → cache/DB → response → render.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 3-minute timer. Explain to an imaginary 12-year-old:

> "Why does a website feel slow sometimes, even if the company has super-fast servers?"

Aim to naturally use: **distance (network latency)**, **number of hops**, **the tail (p99)**, and **busy servers (queueing)**. If you got stuck on one, reread that lesson's 🧸 analogy.

---

## 🛠️ Part 3: Mini-design

**A school's homework-submission site.** 50,000 students, each submits 2 files/day (2 MB each) and checks grades 10 times a day. Keep files for 1 year.

On paper, estimate:
1. Write QPS and read QPS (average and peak).
2. Storage per day and per year.
3. One sentence: "So this means the design needs ___."

<details><summary>One good answer</summary>

- Writes: 100k/day ÷ 10⁵ ≈ **1/s** (peak maybe 20–50/s right before deadlines, since spikes are sharp!).
- Reads: 500k/day ÷ 10⁵ ≈ **5/s**.
- Storage: 100k × 2 MB = **200 GB/day** → ~**73 TB/year** (fewer if weekends are quiet).
- **So this means:** traffic is tiny, and one app server + one DB is enough. Files belong in **object storage** (S3), not the DB. The real risk is the **deadline spike**, so handle bursts (direct-to-S3 uploads, autoscaling).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "What's the difference between latency and throughput? Give an example where improving one hurts the other."**
<details><summary>Model answer</summary>

Latency = time per request. Throughput = requests completed per second. **Batching** writes to a DB raises throughput (fewer round trips) but increases latency for each write, which waits for the batch to fill. Follow-up: when is that acceptable? For logs and analytics, yes. For a checkout button, no.
</details>

**Q2. "Your API's p50 is 20 ms, but p99 is 2 s. Where do you start?"**
<details><summary>Model answer</summary>

Look for things that only sometimes happen: cache misses, GC pauses, lock contention, slow downstream calls, retries, cold starts, and queueing at high utilization. Use tracing to find which span is slow in the p99 requests. Mitigate with timeouts, hedging, caching, and capacity headroom.
</details>

**Q3. "Estimate how many servers a 100k QPS API needs."**
<details><summary>Model answer</summary>

Assume ~5–10k QPS per server for a simple API → **10–20 servers** at full load. Target ~60% utilization and add redundancy → **~20–35 servers**. State assumptions and suggest a load test to validate.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [006 · Availability & the Nines](006-availability-and-nines.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [003](003-latency-numbers-and-percentiles.md) and [005](005-back-of-envelope-estimation.md), then retry tomorrow. That's normal, not failure. |

---

⬅️ [005 · Back-of-Envelope Estimation](005-back-of-envelope-estimation.md) · 🗺️ [Phase map](README.md) · ➡️ [006 · Availability & the Nines](006-availability-and-nines.md)

✅ Tick **Checkpoint 5%** in [PROGRESS.md](../../PROGRESS.md). 🎉
