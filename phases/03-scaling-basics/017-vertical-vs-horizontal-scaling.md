# 017 · Vertical vs Horizontal Scaling

> ⏱ 8 min · 📈 17% · 🅰️ Part A (core) · Phase 03: Scaling Basics
>
> `███░░░░░░░░░░░░░░░░░` 17% of the whole guide

---

## 🎯 One-sentence idea

**Vertical scaling means buying a bigger machine (simple, but there's a ceiling and it's a single point of failure). Horizontal scaling means adding more machines (almost no ceiling, and redundant, but the system must be designed for it).**

## 🧸 Analogy

Your bakery has too many orders:

- 💪 **Vertical (scale up):** hire one **super-baker** with a giant oven. Easy, and there's no coordination. But there's only one of them. If they're sick, the bakery closes, and there's a limit to how big an oven can get.
- 👯 **Horizontal (scale out):** hire **many normal bakers** with normal ovens. You can keep adding more, and if one's sick, others cover. But now you need a manager to hand out orders, and a shared recipe book.

## 🖼️ Visual

```mermaid
flowchart LR
    subgraph V["💪 Vertical: scale UP"]
        S1["🖥️ 4 CPU / 16 GB"] -->|"upgrade"| S2["🖥️🖥️ 64 CPU / 512 GB"]
    end
    subgraph H["👯 Horizontal: scale OUT"]
        LB["🚪 Load balancer"] --> A["🖥️"]
        LB --> B["🖥️"]
        LB --> C["🖥️"]
        LB --> D["🖥️ + more…"]
    end
```

## 🔬 How it works

- **Vertical scaling:** more CPU, RAM, and faster disks on the same machine.
  - ✅ No code changes, no distributed-systems problems, strong consistency stays easy.
  - ❌ **Hard ceiling** (the biggest cloud VMs top out), **cost grows faster than linearly** at the top end, **a single point of failure**, and upgrades often need downtime.
- **Horizontal scaling:** more machines behind a **load balancer**.
  - ✅ Near-linear growth, **redundancy** (lose one, keep serving), cheap commodity hardware, and it can scale **automatically** (lesson 025).
  - ❌ Needs **stateless** app servers (lesson 018), a load balancer, and data that can be split or replicated, and it brings the whole world of **distributed-systems problems** (consistency, partial failure).
- **Real systems do both:** scale up until it's awkward or expensive, then scale out. Databases often scale **up first** because they're hard to split.
- **The usual scaling order:** **cache → clone (stateless app servers behind an LB) → split (services) → shard (data)**.

## 🧩 Worked example

**A growing API, step by step:**

| Load | Move | Why |
|---|---|---|
| 500 req/s | 1 VM (4 CPU) | Fine |
| 3,000 req/s | Upgrade to 16 CPU (**vertical**) | Fastest fix, no code change |
| 15,000 req/s | 6 × 8-CPU VMs behind an LB (**horizontal**) | The single box is near its limit, and you want redundancy |
| DB at 90% CPU | Bigger DB instance (**vertical**) + read replicas | The DB is harder to scale out |
| DB at max size | **Shard** the DB (lesson 049) | Vertical ceiling reached |

**Cost intuition** (illustrative, but the shape is real): in the cloud, 2 × 16-CPU VMs cost about the same as 1 × 32-CPU VM. But you get **redundancy** for free with the two.

## ⚖️ Trade-offs

| | Vertical | Horizontal |
|---|---|---|
| Simplicity | 🟢 Very simple | 🔴 Distributed complexity |
| Ceiling | 🔴 Hard limit | 🟢 Very high |
| Fault tolerance | 🔴 SPOF | 🟢 Redundant |
| Downtime to scale | Often yes | No (add nodes live) |
| Best for | Databases, early stage, quick fixes | Stateless web/app tiers, large scale |

## 🌍 Real world

- **Stack Overflow** famously ran on a small number of very powerful servers for years (vertical), proving it's a legit strategy.
- **Google, Netflix** run on huge fleets of commodity machines (horizontal).
- Cloud VMs go up to **hundreds of vCPUs and multiple TB of RAM**. The vertical ceiling is higher than people think.

## 📌 Cheat card

> - **Up = bigger box. Out = more boxes.**
> - Vertical: **simple, but ceiling + SPOF**. Horizontal: **scalable + redundant, but complex**.
> - Scaling order: **Cache → Clone → Split → Shard.**
> - **App tier scales out easily** (if stateless). **The data tier is the hard part.**

## 🧪 Feynman check

Explain the super-baker vs many-bakers analogy, and why horizontal scaling *requires* a manager (load balancer) and a shared recipe book (shared state/database).

⚠️ **Common confusion:** "Horizontal is always better." It brings network calls, partial failures, and consistency problems. If one bigger box solves it for the next 2 years, that's often the right call.

## ⚡ Quick recall

1. Two downsides of vertical scaling?
<details><summary>Answer</summary>

A hardware ceiling, and a single point of failure (plus rising cost at the top end and possible downtime to upgrade).
</details>

2. What must be true of app servers to scale them horizontally easily?
<details><summary>Answer</summary>

They must be stateless: no user or session data stored only on one server.
</details>

3. Why do databases often scale vertically first?
<details><summary>Answer</summary>

Splitting data across machines (sharding) is complex. It breaks joins and transactions and needs rebalancing, so a bigger box buys time cheaply.
</details>

## 🎤 Interview practice

**Q1. "Your single-server app is at 90% CPU. Walk me through your options."**
<details><summary>Model answer</summary>

- **Profile first:** is it the app or the DB on the same box? Is there a hot endpoint or a missing index?
- **Quick wins:** caching, query optimization, moving the DB to its own server.
- **Vertical:** a bigger instance, with minimal risk and fast to do.
- **Horizontal:** make the app stateless (sessions to Redis, files to S3), put an LB in front, and run N copies. This also gives redundancy.
- **Likely follow-up:** "What changes in the code to allow horizontal scaling?" → externalize sessions, uploads, caches, and cron jobs (make sure only one runs), and make requests idempotent.
</details>

**Q2. "Can you scale a relational database horizontally?"**
<details><summary>Model answer</summary>

- **Reads:** yes, easily, with **read replicas** (lesson 046).
- **Writes:** via **sharding** (application-level or tools like Vitess/Citus) or **NewSQL** (Spanner, CockroachDB).
- Costs: cross-shard joins and transactions get hard, rebalancing, and operational complexity.
- Before that: vertical scaling, caching, archiving cold data, and separating read and write workloads.
- **Likely follow-up:** "How would you pick a shard key?" → lesson 050.
</details>

---

⬅️ [016 · Real-time Communication](../02-networking/016-real-time-communication.md) · 🗺️ [Phase map](README.md) · ➡️ [018 · Stateless Services](018-stateless-services.md)

✅ **Safe stopping point.** Tick lesson 017 in [PROGRESS.md](../../PROGRESS.md).
