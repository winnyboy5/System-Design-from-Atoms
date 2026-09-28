# 🧭 Coverage Map

> Every standard system design topic (drawn from common syllabi: *Designing Data-Intensive Applications*, the System Design Primer, and popular interview guides), mapped to the lesson(s) that teach it.
> Use it to **find a topic fast**, to **fill gaps** found in checkpoints, and to confirm that nothing important is missing.

## 🧱 Fundamentals & estimation

| Topic | Lesson(s) |
|---|---|
| What system design is; trade-off thinking | [001](phases/01-foundations/001-what-is-system-design.md) |
| Client–server model, request lifecycle | [002](phases/01-foundations/002-life-of-a-request.md) |
| Latency numbers, percentiles (p50/p99), tail latency, hedged requests | [003](phases/01-foundations/003-latency-numbers-and-percentiles.md) |
| Latency vs throughput vs bandwidth, Little's law, queueing & utilization | [004](phases/01-foundations/004-latency-throughput-bandwidth.md) |
| Back-of-envelope estimation (QPS, storage, bandwidth, servers) | [005](phases/01-foundations/005-back-of-envelope-estimation.md) |
| Availability, nines, series/parallel math, MTBF/MTTR | [006](phases/01-foundations/006-availability-and-nines.md) |
| SLA / SLO / SLI, error budgets | [007](phases/01-foundations/007-sla-slo-sli.md) |
| Functional vs non-functional requirements | [008](phases/01-foundations/008-requirements.md) |
| Performance vs scalability | [001](phases/01-foundations/001-what-is-system-design.md) · [017](phases/03-scaling-basics/017-vertical-vs-horizontal-scaling.md) |

## 🌐 Networking & APIs

| Topic | Lesson(s) |
|---|---|
| OSI/TCP-IP layers, IP addressing, ports, NAT, packets, MTU | [009](phases/02-networking/009-ip-ports-packets.md) |
| TCP vs UDP, handshakes, head-of-line blocking, QUIC | [010](phases/02-networking/010-tcp-vs-udp.md) |
| DNS resolution, record types, TTL, GeoDNS, Anycast | [011](phases/02-networking/011-dns.md) |
| HTTP methods, status codes, headers, HTTP/1.1 vs 2 vs 3, keep-alive | [012](phases/02-networking/012-http-and-status-codes.md) |
| HTTPS/TLS, certificates, TLS termination, mTLS | [013](phases/02-networking/013-https-and-tls.md) |
| REST design, pagination (offset/cursor), versioning, ETags, idempotent methods | [014](phases/02-networking/014-rest-api-design.md) |
| RPC/gRPC, GraphQL, JSON vs Protobuf, BFF | [015](phases/02-networking/015-rest-grpc-graphql.md) · [022](phases/03-scaling-basics/022-api-gateway.md) |
| Polling, long polling, SSE, WebSockets | [016](phases/02-networking/016-real-time-communication.md) |
| Webhooks | [056](phases/07-async-messaging/056-sync-vs-async.md) · [096](phases/11-advanced-designs/096-design-payment-system.md) |

## 📈 Scaling the application tier

| Topic | Lesson(s) |
|---|---|
| Vertical vs horizontal scaling | [017](phases/03-scaling-basics/017-vertical-vs-horizontal-scaling.md) |
| Stateless services, sessions, sticky sessions | [018](phases/03-scaling-basics/018-stateless-services.md) |
| Load balancers, health checks, connection draining | [019](phases/03-scaling-basics/019-load-balancers.md) |
| Load-balancing algorithms (RR, weighted, least-conn, hashing, P2C) | [020](phases/03-scaling-basics/020-load-balancing-algorithms.md) |
| L4 vs L7 load balancing, reverse & forward proxies | [021](phases/03-scaling-basics/021-l4-l7-and-proxies.md) |
| API gateway, BFF | [022](phases/03-scaling-basics/022-api-gateway.md) |
| CDN (push/pull, cache-control, invalidation, edge compute) | [023](phases/03-scaling-basics/023-cdn.md) |
| Rate limiting algorithms (token/leaky bucket, fixed/sliding windows) | [024](phases/03-scaling-basics/024-rate-limiting.md) · [075](phases/09-core-case-studies/075-design-rate-limiter.md) |
| Autoscaling, containers, Kubernetes, serverless | [025](phases/03-scaling-basics/025-autoscaling-and-containers.md) |
| Monolith vs microservices, modular monolith, strangler fig, Conway's law | [026](phases/03-scaling-basics/026-monolith-vs-microservices.md) |
| Service discovery, service mesh, sidecars | [071](phases/08-reliability-ops/071-service-discovery-and-mesh.md) |

## ⚡ Caching

| Topic | Lesson(s) |
|---|---|
| Cache layers (browser → CDN → proxy → app → distributed → DB) | [027](phases/04-caching/027-caching-basics.md) |
| Cache-aside, read-through | [028](phases/04-caching/028-cache-aside-and-read-through.md) |
| Write-through, write-back (write-behind), write-around | [029](phases/04-caching/029-cache-write-strategies.md) |
| Eviction: LRU, LFU, TTL, ARC/W-TinyLFU | [030](phases/04-caching/030-cache-eviction.md) |
| Cache invalidation, CDC-driven invalidation, stale-set races | [031](phases/04-caching/031-cache-invalidation.md) |
| Cache stampede / thundering herd, hot keys, penetration, avalanche | [032](phases/04-caching/032-cache-stampede-and-hot-keys.md) |
| Redis vs Memcached, Redis Cluster, persistence (RDB/AOF) | [033](phases/04-caching/033-distributed-caches.md) |

## 🗄️ Databases & storage

| Topic | Lesson(s) |
|---|---|
| SQL vs NoSQL, NewSQL | [034](phases/05-databases/034-sql-vs-nosql.md) |
| ACID, BASE | [035](phases/05-databases/035-acid.md) · [053](phases/06-scaling-data/053-consistency-models.md) |
| Transactions, isolation levels, anomalies (dirty read, lost update, write skew, phantoms) | [036](phases/05-databases/036-transactions-and-isolation.md) |
| Locking: pessimistic vs optimistic concurrency control | [036](phases/05-databases/036-transactions-and-isolation.md) · [039](phases/05-databases/039-normalization-vs-denormalization.md) |
| Indexes: B-tree, hash, composite, covering, partial | [037](phases/05-databases/037-indexes.md) |
| NoSQL families: key-value, document, wide-column, graph | [038](phases/05-databases/038-nosql-families.md) |
| Normalization vs denormalization, materialized views | [039](phases/05-databases/039-normalization-vs-denormalization.md) |
| Access patterns, N+1 queries, connection pooling | [040](phases/05-databases/040-access-patterns-and-pooling.md) |
| OLTP vs OLAP, data warehouses, data lakes, lakehouse, ETL/ELT, star schema, columnar storage | [041](phases/05-databases/041-oltp-vs-olap.md) |
| Object/blob storage, block vs file storage, pre-signed URLs, multipart upload | [042](phases/05-databases/042-object-storage.md) |
| Full-text search, inverted index, BM25, Elasticsearch | [043](phases/05-databases/043-search-and-inverted-index.md) |
| Time-series DBs, geospatial indexes, vector DBs, coordination stores | [044](phases/05-databases/044-specialized-databases.md) |
| Choosing a database; polyglot persistence | [045](phases/05-databases/045-choosing-a-database.md) |
| Storage engines: B-tree vs LSM tree, SSTables, compaction, WAL | [081](phases/10-deep-internals/081-btree-lsm-and-wal.md) |
| MVCC, vacuum/garbage collection | [082](phases/10-deep-internals/082-mvcc.md) |

## 🧬 Distributed data

| Topic | Lesson(s) |
|---|---|
| Leader–follower replication, sync vs async, failover | [046](phases/06-scaling-data/046-leader-follower-replication.md) |
| Replication lag, read-your-writes, monotonic reads | [047](phases/06-scaling-data/047-replication-lag.md) |
| Multi-leader & leaderless replication, conflict resolution, CRDTs | [048](phases/06-scaling-data/048-multi-leader-and-leaderless.md) |
| Sharding/partitioning strategies, rebalancing | [049](phases/06-scaling-data/049-sharding.md) |
| Shard keys, hotspots, secondary indexes (local/global), scatter-gather | [050](phases/06-scaling-data/050-shard-keys-and-hotspots.md) |
| Consistent hashing, virtual nodes | [051](phases/06-scaling-data/051-consistent-hashing.md) |
| CAP theorem | [052](phases/06-scaling-data/052-cap-theorem.md) |
| PACELC | [083](phases/10-deep-internals/083-pacelc.md) |
| Consistency models (linearizable, sequential, causal, eventual, session) | [053](phases/06-scaling-data/053-consistency-models.md) |
| Quorums (R + W > N), sloppy quorums, hinted handoff, read repair | [054](phases/06-scaling-data/054-quorums.md) · [092](phases/11-advanced-designs/092-design-key-value-store.md) |
| Idempotency, deduplication | [055](phases/06-scaling-data/055-idempotency.md) |
| Clocks: NTP skew, Lamport, vector clocks, HLC, TrueTime | [084](phases/10-deep-internals/084-time-and-clocks.md) |
| Consensus: Raft, Paxos, ZAB | [085](phases/10-deep-internals/085-consensus-raft.md) |
| Leader election, distributed locks, leases, fencing tokens | [086](phases/10-deep-internals/086-leader-election-and-locks.md) |
| Gossip protocols, failure detection (phi accrual, SWIM) | [087](phases/10-deep-internals/087-gossip-and-failure-detection.md) |
| Distributed transactions: 2PC, 3PC, sagas | [088](phases/10-deep-internals/088-2pc-vs-sagas.md) |
| Anti-entropy, Merkle trees | [090](phases/10-deep-internals/090-probabilistic-data-structures.md) · [092](phases/11-advanced-designs/092-design-key-value-store.md) |

## 📬 Asynchronous processing & data flow

| Topic | Lesson(s) |
|---|---|
| Sync vs async communication, 202 + job status | [056](phases/07-async-messaging/056-sync-vs-async.md) |
| Message queues, competing consumers, visibility timeouts, DLQs | [057](phases/07-async-messaging/057-message-queues.md) |
| Pub/sub, fan-out | [058](phases/07-async-messaging/058-pub-sub-and-fan-out.md) |
| Log-based streaming (Kafka), partitions, consumer groups, offsets, retention | [059](phases/07-async-messaging/059-log-based-streaming.md) |
| Delivery semantics (at-most/at-least/exactly-once) | [060](phases/07-async-messaging/060-delivery-semantics.md) |
| Backpressure, load shedding, admission control | [061](phases/07-async-messaging/061-backpressure-and-load-shedding.md) |
| Event-driven architecture, choreography vs orchestration, transactional outbox, CDC | [062](phases/07-async-messaging/062-event-driven-and-outbox.md) |
| Event sourcing, CQRS | [089](phases/10-deep-internals/089-event-sourcing-and-cqrs.md) |
| Batch vs stream processing, MapReduce, Spark, Flink, windows, watermarks, Lambda/Kappa | [091](phases/11-advanced-designs/091-batch-vs-stream-processing.md) |

## 🛡️ Reliability, operations & security

| Topic | Lesson(s) |
|---|---|
| Timeouts, retries, exponential backoff, jitter, retry budgets | [063](phases/08-reliability-ops/063-timeouts-and-retries.md) |
| Circuit breakers, bulkheads, fallbacks, graceful degradation | [064](phases/08-reliability-ops/064-circuit-breakers-and-bulkheads.md) |
| Redundancy, failover, SPOFs, active-active vs active-passive, chaos engineering | [065](phases/08-reliability-ops/065-redundancy-and-failover.md) |
| Multi-region, disaster recovery, RPO/RTO, backups, PITR | [066](phases/08-reliability-ops/066-multi-region-and-disaster-recovery.md) |
| Observability: metrics, logs, traces, golden signals, alerting | [067](phases/08-reliability-ops/067-observability.md) |
| Deployment strategies: rolling, blue-green, canary, feature flags, schema migrations | [068](phases/08-reliability-ops/068-deployment-strategies.md) |
| Authentication, authorization, OAuth 2.0, OIDC, JWT, RBAC/ABAC/ReBAC | [069](phases/08-reliability-ops/069-authn-authz-oauth-jwt.md) |
| Security: encryption, KMS, secrets, least privilege, zero trust, OWASP, DDoS, privacy/GDPR, PCI | [070](phases/08-reliability-ops/070-security-essentials.md) |
| Unique ID generation: UUID, ULID, Snowflake | [072](phases/08-reliability-ops/072-unique-id-generation.md) |

## 🧮 Probabilistic & specialized structures

| Topic | Lesson(s) |
|---|---|
| Bloom filters, counting/cuckoo filters | [090](phases/10-deep-internals/090-probabilistic-data-structures.md) |
| HyperLogLog | [090](phases/10-deep-internals/090-probabilistic-data-structures.md) |
| Count-min sketch, heavy hitters | [090](phases/10-deep-internals/090-probabilistic-data-structures.md) · [099](phases/11-advanced-designs/099-design-top-k-trending.md) |
| Tries, prefix search | [093](phases/11-advanced-designs/093-design-autocomplete.md) |
| Geohash, quadtree, S2/H3, R-tree | [095](phases/11-advanced-designs/095-design-proximity-service.md) |
| LRU cache implementation | [030](phases/04-caching/030-cache-eviction.md) |

## 🏗️ Classic design problems

| Topic | Lesson(s) |
|---|---|
| Interview framework | [073](phases/09-core-case-studies/073-design-framework.md) |
| URL shortener / Pastebin | [073](phases/09-core-case-studies/073-design-framework.md) · [074](phases/09-core-case-studies/074-design-url-shortener.md) |
| Rate limiter | [075](phases/09-core-case-studies/075-design-rate-limiter.md) |
| News feed / timeline | [076](phases/09-core-case-studies/076-design-news-feed.md) |
| Chat / messaging | [077](phases/09-core-case-studies/077-design-chat-app.md) |
| Notification system | [078](phases/09-core-case-studies/078-design-notification-system.md) |
| Video streaming (YouTube/Netflix) | [079](phases/09-core-case-studies/079-design-video-streaming.md) |
| File storage & sync (Dropbox/Drive) | [080](phases/09-core-case-studies/080-design-file-sync.md) |
| Distributed key-value store | [092](phases/11-advanced-designs/092-design-key-value-store.md) |
| Search autocomplete | [093](phases/11-advanced-designs/093-design-autocomplete.md) |
| Web crawler | [094](phases/11-advanced-designs/094-design-web-crawler.md) |
| Proximity service / ride-sharing (Yelp/Uber) | [095](phases/11-advanced-designs/095-design-proximity-service.md) |
| Payment system | [096](phases/11-advanced-designs/096-design-payment-system.md) |
| Distributed job scheduler | [097](phases/11-advanced-designs/097-design-job-scheduler.md) |
| Ticket booking / flash sale | [098](phases/11-advanced-designs/098-design-ticket-booking.md) |
| Top-K / trending / leaderboard | [099](phases/11-advanced-designs/099-design-top-k-trending.md) · [033](phases/04-caching/033-distributed-caches.md) |
| Unique ID generator | [072](phases/08-reliability-ops/072-unique-id-generation.md) |
| Metrics/monitoring system | [044](phases/05-databases/044-specialized-databases.md) · [067](phases/08-reliability-ops/067-observability.md) |
| Real-time collaboration (Docs), stock exchange, game backend, ad click aggregation, LLM chat (capstone prompts) | [100](phases/11-advanced-designs/100-capstone.md) |

---

**111 syllabus topics mapped across 100 lessons.** Also see the [📌 cheatsheets](cheatsheets/NUMBERS.md) and the [📖 glossary](GLOSSARY.md).

🏠 [Back to README](README.md) · 📊 [Progress](PROGRESS.md)
