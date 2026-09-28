# 🧠 Mnemonics & Memory Tricks

> Silly is good. Silly is memorable.

---

| Topic | Trick | Meaning |
|---|---|---|
| **ACID** [035] | "**A**ll **C**hanges **I**n **D**atabase" stay safe | **A**tomic (all or nothing), **C**onsistent (rules hold), **I**solated (no peeking at half-done work), **D**urable (survives crashes) |
| **BASE** [053] | Acid vs Base, like chemistry opposites | **B**asically **A**vailable, **S**oft state, **E**ventually consistent |
| **CAP** [052] | "**C**hoose **A** **P**artner": when the network **P**artitions, pick **C** or **A** | Partitions *will* happen, so the real choice is C vs A during one |
| **PACELC** [083] | "**P**artition? **A** or **C**. **E**lse? **L** or **C**" | Even without partitions, you trade latency vs consistency |
| **Latency ladder** [003] | "**100, 100, half, 100**" | RAM ~100 ns, SSD ~100 µs, datacenter RTT ~0.5 ms, cross-continent ~100 ms |
| **Day in seconds** [005] | "**A day is a hundred grand**" | 1 day ≈ 10⁵ s |
| **QPS from daily** [005] | "**A million a day is a dozen a second**" | 1M/day ≈ 12 QPS |
| **Nines** [006] | "**3 nines ≈ 9 hours, 4 nines ≈ 1 hour, 5 nines ≈ 5 minutes** per year" | Each extra nine = 10× less downtime |
| **Quorum** [054] | "**R + W > N** means the Reader meets the Writer" | Read and write sets overlap, so reads see the latest write |
| **Cache write strategies** [029] | "**Through** = together, **Back** = later, **Around** = skip" | Write-through: cache and DB together. Write-back: DB later. Write-around: skip the cache. |
| **Scaling order** [017] | "**Cache, Clone, Split, Shard**" | Add a cache → clone stateless servers → split services → shard data |
| **Reliability toolkit** [063–065] | "**TRCB-R**: **T**imeouts, **R**etries, **C**ircuit **B**reakers, **R**edundancy" | The four basic defences |
| **Observability** [067] | "**MLT**, like a sandwich" | **M**etrics, **L**ogs, **T**races |
| **The 4 golden signals** [067] | "**LETS**" | **L**atency, **E**rrors, **T**raffic, **S**aturation |
| **Monitoring methods** [067] | "**RED** for services, **USE** for resources" | RED = Rate, Errors, Duration. USE = Utilization, Saturation, Errors. |
| **Interview steps** [073] | "**RED-HD-W**": **R**equirements, **E**stimates, **D**ata/API, **H**igh-level, **D**eep dive, **W**rap-up | The design interview flow |
| **Delivery semantics** [060] | "**Most** may lose, **least** may duplicate, **exactly** is a myth (without idempotency)" | At-most-once, at-least-once, "exactly-once" |
| **Snowflake ID** [072] | "**41-10-12**" | 41 bits time, 10 bits machine, 12 bits sequence |
| **HTTP status codes** [012] | "**2 good, 3 go elsewhere, 4 you messed up, 5 we messed up**" | 2xx success, 3xx redirect, 4xx client error, 5xx server error |
| **TCP vs UDP** [010] | "TCP is a **phone call**, UDP is **throwing postcards**" | Connection + guaranteed order vs fire-and-forget |
| **Isolation levels** [036] | "**R**eally **R**eally **R**ead **S**afely": RU → RC → RR → S | Read Uncommitted → Read Committed → Repeatable Read → Serializable (weakest → strongest) |
| **Consistent hashing** [051] | "**Walk clockwise to the next server**" | Keys and servers sit on a ring, and each key belongs to the next server clockwise |
| **Rate limiting** [024] | "**Token bucket = coins in a jar**" | Tokens refill at a steady rate, each request spends one, and bursts are allowed up to the jar size |
| **Saga** [088] | "**Every step has an undo button**" | Compensating transactions instead of a global lock |
| **RPO vs RTO** [066] | "**P**oint = how much data you lose, **T**ime = how long you're down" | Recovery Point Objective vs Recovery Time Objective |

---

⬅️ [INTERVIEW-FRAMEWORK.md](INTERVIEW-FRAMEWORK.md) · 🏠 [README](../README.md)
