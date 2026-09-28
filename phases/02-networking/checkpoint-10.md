# ✅ Checkpoint 10%: 🎉 Level-Up! Foundations + Packets

> ⏱ 15 min · Covers lessons **006–010** (plus a pinch of 001–005) · 📈 You're at **10%**
>
> `██░░░░░░░░░░░░░░░░░░` 🎉 **10% done: a real level-up.** You can now talk about speed, uptime, requirements, and how bytes move.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Pantry's first faraway customers were happy. Take a breath with me, and let's check what you've learned so far.*

---

## ⚡ Part 1: Recall (5 questions)

1. How much downtime per month does a 99.9% SLO allow?
<details><summary>Answer</summary>

About **43 minutes**.
</details>

2. What's an error budget, and what happens when it runs out?
<details><summary>Answer</summary>

1 − SLO (the allowed failures). When it's used up, the team pauses risky releases and focuses on reliability.
</details>

3. Give two functional and two non-functional requirements for a food-delivery app.
<details><summary>Answer</summary>

Functional: order food, track the courier. Non-functional: 99.95% ordering availability, location updates within 5 s, and so on.
</details>

4. Which port does HTTPS use, and which does Postgres use?
<details><summary>Answer</summary>

443 and 5432.
</details>

5. Name one reason to pick UDP over TCP.
<details><summary>Answer</summary>

Real-time data where late data is useless (voice/video/games), tiny request-response exchanges (DNS), or no need for a handshake.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "How does a message from my phone reach a game server, and why do games use a different 'delivery method' than banking apps?"

Must include: **IP address, port, packets, TCP vs UDP**.

---

## 🛠️ Part 3: Mini-design

**A smart doorbell** sends: (a) a motion alert, (b) live video when you open the app, (c) a daily health report.

For each, choose **TCP or UDP** and write one sentence of reasoning. Then give an **availability target** for the alert path.

<details><summary>One good answer</summary>

- (a) Motion alert → **TCP** (HTTPS/MQTT). It must arrive. Target **99.9%+** delivery.
- (b) Live video → **UDP** (WebRTC). Low latency beats perfect frames.
- (c) Daily report → **TCP**. Reliability matters, and latency doesn't.
- Alert-path SLO example: "99.9% of alerts delivered to the phone within 5 s."
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Why is availability of a chain of services lower than each service's availability?"**
<details><summary>Model answer</summary>

A request succeeds only if *every* service in the chain succeeds, so the probabilities multiply: 0.999 × 0.999 × 0.999 ≈ 0.997. Mitigate with redundancy, soft dependencies (caching, fallbacks), and fewer synchronous hops.
</details>

**Q2. "Explain TCP's head-of-line blocking and how modern protocols address it."**
<details><summary>Model answer</summary>

TCP delivers bytes in order, so one lost packet stalls everything behind it, even data for unrelated HTTP/2 streams. QUIC (HTTP/3) runs independent streams over UDP, so a loss only stalls its own stream.
</details>

**Q3. "Define SLIs and SLOs for a login service."**
<details><summary>Model answer</summary>

SLI: successful logins (non-5xx) ÷ attempts; logins completing in under 500 ms ÷ total. SLOs: 99.95% availability and 99% under 500 ms over 30 days. Exclude user errors (wrong password → 401) from failures.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Onward to [011 · DNS](011-dns.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [006](../01-foundations/006-availability-and-nines.md), [007](../01-foundations/007-sla-slo-sli.md), [010](010-tcp-vs-udp.md) |

🏆 **Level-up reward:** 10% is where most people quit. You didn't. Take a real break.

---

⬅️ [010 · TCP vs UDP](010-tcp-vs-udp.md) · 🗺️ [Phase map](README.md) · ➡️ [011 · DNS](011-dns.md)

✅ Tick **Checkpoint 10%** in [PROGRESS.md](../../PROGRESS.md). 🎉
