# 📌 Phase 01 Cheatsheet: Foundations

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 001 | System design = **atoms + trade-offs + requirements**. Start simple, then evolve. |
| 002 | Request path: **DNS → TCP → TLS → HTTP → LB → app → cache/DB → back**. Every hop costs time and can fail. |
| 003 | **Memory ≪ disk ≪ network.** Judge speed by **p99**, never by averages alone. |
| 004 | **Latency** = time for one. **Throughput** = done per second. **Bandwidth** = capacity. **L = λ × W.** |
| 005 | **Estimate in 2 minutes**: DAU → actions → QPS → storage → machines. End with "so this means…". |
| 006 | Each nine = **10× less downtime**. Series multiplies down, parallel adds up. |
| 007 | **SLI** measure → **SLO** target → **SLA** contract. **Error budget = 1 − SLO.** |
| 008 | **Functional = verbs, non-functional = adjectives.** Non-functionals choose the architecture. |

## 🔢 Numbers to know

```
RAM ~100 ns · SSD ~100 µs · DC round trip ~0.5 ms · cross-continent ~100 ms
1 day ≈ 10⁵ s · 1M/day ≈ 12 QPS · peak ≈ 2–3× avg · replicas ×3
99.9% ≈ 8.8 h/yr (43 min/mo) · 99.99% ≈ 53 min/yr · 99.999% ≈ 5 min/yr
1 Gbps ≈ 125 MB/s · humans: <100 ms instant, >1 s breaks flow
```

## 🧮 Formulas

| Formula | Use |
|---|---|
| QPS = daily requests ÷ 10⁵ | Traffic estimate |
| Storage = size × count/day × days × 3 | Capacity estimate |
| L = λ × W | Pool and worker sizing |
| Series: A × B · Parallel: 1 − (1 − A)(1 − B) | Availability math |
| Availability ≈ MTBF ÷ (MTBF + MTTR) | Why fast recovery matters |
| P(any slow) = 1 − (1 − p)ⁿ | Tail amplification in fan-out |

## 🪄 Tricks

- "**100, 100, half, 100**" → the latency ladder.
- "**A day is a hundred grand**" → 10⁵ seconds.
- "**A million a day is a dozen a second.**"
- Busy servers are slow servers. **Keep 30–50% headroom.**
- Always ask: **read-heavy or write-heavy? consistency or availability?**

## ⚠️ Top mistakes

- Using averages instead of percentiles.
- Designing before clarifying requirements.
- Vague non-functionals ("fast", "scalable") with no numbers.
- Confusing availability (up) with durability (not lost).

---

🗺️ [Phase map](README.md) · 🎤 [Interview questions](INTERVIEW-QUESTIONS.md)
