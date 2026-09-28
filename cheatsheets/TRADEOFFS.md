# ⚖️ Trade-offs Cheat Sheet: Every "X vs Y" in One Place

> System design is **choosing what to give up**. For each pair, know: *what do I gain, what do I pay, and when do I pick it?*
> The lesson number is in brackets, so you can jump back if a row feels fuzzy.

---

## 🧱 Fundamentals

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **Latency** | | **Throughput** [004] | Users wait on each request (APIs, UI) | Bulk work (batch jobs, analytics) |
| **Consistency** | | **Availability** [052] | Money, inventory, bookings | Feeds, likes, view counts |
| **Performance** | | **Cost** [001] | Revenue depends on speed | Tight budget, internal tools |
| **Simplicity** | | **Flexibility** [026] | Small team, early product | Many teams, complex domains |

## 🌐 Networking & APIs

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **TCP** | | **UDP** [010] | Every byte must arrive in order (web, DB, files) | Speed beats perfection (video calls, games, DNS) |
| **REST** | | **gRPC** [015] | Public APIs, browsers, simplicity | Internal service-to-service calls, speed, streaming |
| **REST** | | **GraphQL** [015] | Simple resources, easy caching | Many clients need different shapes of data |
| **Polling** | | **WebSockets** [016] | Updates are rare, simplicity matters | Real-time, two-way (chat, games) |
| **SSE** | | **WebSockets** [016] | Server → client only (live scores, notifications) | Both directions needed |
| **JSON** | | **Protobuf** [015] | Human-readable, public APIs | Compact, fast, typed internal traffic |

## 📈 Scaling

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **Vertical scaling** | | **Horizontal scaling** [017] | Early days, simple, DBs that are hard to split | Large scale, need redundancy |
| **Stateful** | | **Stateless** [018] | Must keep a connection (WebSockets, game sessions) | Almost everything else, since it's easy to scale |
| **L4 LB** | | **L7 LB** [021] | Raw speed, any protocol | Routing by URL/header, TLS termination |
| **Monolith** | | **Microservices** [026] | Small team, new product | Many teams, parts that need to scale independently |

## ⚡ Caching

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **Cache-aside** | | **Read-through** [028] | You want control, and the cache may be down | You want simpler app code |
| **Write-through** | | **Write-back** [029] | Cache must never be stale | Write speed matters and a little loss is OK |
| **LRU** | | **LFU** [030] | Recent items are the most likely to be reused | Some items are popular for a long time |
| **TTL expiry** | | **Explicit invalidation** [031] | Staleness is OK for a while | Must reflect changes immediately |

## 🗄️ Data

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **SQL** | | **NoSQL** [034] | Relations, transactions, ad-hoc queries | Huge scale, flexible schema, simple access patterns |
| **Normalize** | | **Denormalize** [039] | Write-heavy, need integrity | Read-heavy, need speed |
| **B-tree** | | **LSM tree** [081] | Read-heavy, point lookups | Write-heavy (logs, time series) |
| **OLTP** | | **OLAP** [041] | Many small transactions | Big analytical scans |
| **Strong consistency** | | **Eventual consistency** [053] | Correctness is critical | Scale and latency are critical |
| **Leader-follower** | | **Leaderless** [046, 048] | Simple, consistent writes | High write availability |
| **Range sharding** | | **Hash sharding** [049] | Range queries (by time, by name) | Even spread of load |
| **Sync replication** | | **Async replication** [046] | No data loss allowed | Low write latency |
| **2PC** | | **Saga** [088] | Short, few participants, strong atomicity | Long-running, many services |

## 📬 Async

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **Sync call** | | **Async message** [056] | Caller needs the answer now | Work can happen later, or spikes must be absorbed |
| **Queue** | | **Pub/sub** [057, 058] | One worker should handle each task | Many subscribers each need every event |
| **Traditional queue** | | **Log (Kafka)** [059] | Tasks are deleted once done | Replay, multiple consumers, ordering |
| **At-least-once** | | **At-most-once** [060] | Can't lose messages (make handlers idempotent) | Losing some is fine (metrics) |
| **Push (fan-out on write)** | | **Pull (fan-out on read)** [076] | Normal users, fast reads | Celebrities with millions of followers |
| **Batch** | | **Stream** [091] | Hourly/daily reports, cheap | Real-time dashboards, fraud detection |

## 🛡️ Reliability

| X | vs | Y | Pick X when… | Pick Y when… |
|---|---|---|---|---|
| **Active-passive** | | **Active-active** [065] | Simpler, some failover delay is OK | Zero downtime, better utilization |
| **Blue-green** | | **Canary** [068] | Instant full switch and rollback | Gradual, low-risk rollout |
| **Session cookie** | | **JWT** [069] | Easy revocation, server-side control | Stateless, many services verify it |
| **UUID** | | **Snowflake ID** [072] | Simple, no coordination | Sortable by time, compact 64-bit |

---

⬅️ [ESTIMATION-TRICKS.md](ESTIMATION-TRICKS.md) · ➡️ [PATTERNS.md](PATTERNS.md)
