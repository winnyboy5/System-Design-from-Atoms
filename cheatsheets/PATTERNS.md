# 🧩 Patterns: "If You Hear X, Reach for Y"

> Interviews and real projects give you **symptoms**. This sheet maps each symptom to the **atom** that treats it.
> The number in brackets is the lesson that teaches it.

---

## 🐢 "It's slow"

| You hear… | Reach for… |
|---|---|
| "Same data read over and over" | **Cache** (cache-aside + TTL) [027, 028] |
| "Users are far away / global" | **CDN** for static content, **multi-region** for dynamic [023, 066] |
| "Database queries are slow" | **Index** → **read replicas** → **cache** → **denormalize** [037, 046, 039] |
| "Page does 100 DB calls" | **Fix N+1**: batch or join [040] |
| "Heavy work in the request path" | **Queue + background workers** [057] |
| "Search is slow with LIKE '%x%'" | **Inverted index** (Elasticsearch) [043] |
| "Tail latency (p99) is bad" | **Timeouts**, hedged requests, remove the slowest dependency [003, 063] |

## 📈 "It needs to handle more"

| You hear… | Reach for… |
|---|---|
| "One server can't keep up" | **Horizontal scaling + load balancer** [017, 019] |
| "Servers remember user sessions" | **Make them stateless**, and put sessions in Redis [018] |
| "Database is too big / too many writes" | **Sharding** [049] |
| "Adding a node reshuffles everything" | **Consistent hashing** [051] |
| "Traffic spikes, like a flash sale" | **Queue to absorb the spike** + **autoscaling** + **rate limiting** [057, 025, 024] |
| "One celebrity / one key gets all traffic" | **Hot-key handling**: replicate the key, add a random suffix, cache locally [032, 050] |
| "Billions of small counters" | **Approximate counting**: HyperLogLog, count-min sketch [090] |

## 💥 "It breaks"

| You hear… | Reach for… |
|---|---|
| "One machine dies and everything's down" | **Redundancy + failover**, remove the SPOF [065] |
| "A slow dependency takes down everything" | **Timeouts + circuit breaker + bulkheads** [063, 064] |
| "Retries make the outage worse" | **Exponential backoff + jitter**, retry budgets [063] |
| "Duplicate charges / double processing" | **Idempotency keys** [055] |
| "Whole region went down" | **Multi-region**, and define RPO/RTO [066] |
| "Messages got lost" | **Durable queue + at-least-once + idempotent consumer** [060] |
| "Two nodes both think they're the leader" | **Consensus / fencing tokens** [085, 086] |
| "Bad deploy broke prod" | **Canary / blue-green + feature flags** [068] |
| "We don't know why it's slow or broken" | **Observability**: metrics, logs, traces [067] |

## 🔗 "Things must happen together"

| You hear… | Reach for… |
|---|---|
| "Update 2 tables atomically" | **DB transaction (ACID)** [035] |
| "Update DB *and* send an event reliably" | **Transactional outbox** [062] |
| "Transaction across several services" | **Saga** with compensating actions [088] |
| "Need full history / audit / undo" | **Event sourcing** [089] |
| "Reads and writes have very different shapes" | **CQRS** [089] |
| "Only one worker should do this job" | **Distributed lock with a lease + fencing** [086] |

## 📡 "It needs to be real-time"

| You hear… | Reach for… |
|---|---|
| "Chat / multiplayer / live collaboration" | **WebSockets** [016, 077] |
| "Live score / stock ticker (server → client)" | **SSE** [016] |
| "Notify many services when X happens" | **Pub/sub** [058] |
| "Real-time analytics / fraud detection" | **Stream processing** (Kafka + Flink) [059, 091] |

## 🗂️ "It stores special data"

| You hear… | Reach for… |
|---|---|
| "Images, video, files" | **Object storage (S3) + CDN** [042, 023] |
| "Metrics over time" | **Time-series DB** [044] |
| "Friends of friends, recommendations" | **Graph DB** [038] |
| "Nearby drivers / restaurants" | **Geohash / quadtree** [095] |
| "Autocomplete as you type" | **Trie + cache of top results** [093] |
| "Has this URL been seen before?" (billions) | **Bloom filter** [090] |
| "Globally unique, sortable IDs" | **Snowflake IDs** [072] |
| "Analytics over huge datasets" | **Data warehouse / columnar store** [041] |

## 🔐 "It must be secure / fair"

| You hear… | Reach for… |
|---|---|
| "Stop abuse / bots / one user hogging resources" | **Rate limiting** (token bucket) [024, 075] |
| "Log in with Google" | **OAuth 2.0 / OIDC** [069] |
| "Many services must check who the user is" | **JWT** verified at the **API gateway** [069, 022] |
| "Protect data" | **TLS in transit, encryption at rest, secret manager** [013, 070] |

---

⬅️ [TRADEOFFS.md](TRADEOFFS.md) · ➡️ [DATABASE-CHOOSER.md](DATABASE-CHOOSER.md)
