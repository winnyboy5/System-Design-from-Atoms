# 📖 Glossary

> Plain-English definitions. Each links to the lesson that teaches it. **If a word in a lesson confuses you, look it up here.**

## A

- **ACID**: Atomic, Consistent, Isolated, Durable: the safety promises of database transactions. → [035](phases/05-databases/035-acid.md)
- **Active-active / active-passive**: All copies serve traffic vs a standby that only takes over on failure. → [065](phases/08-reliability-ops/065-redundancy-and-failover.md)
- **Adaptive bitrate (ABR)**: The video player switches quality per segment to match your connection. → [079](phases/09-core-case-studies/079-design-video-streaming.md)
- **Anti-entropy**: A background process where replicas compare data (often with Merkle trees) and fix differences. → [092](phases/11-advanced-designs/092-design-key-value-store.md)
- **API gateway**: A single front door for many services that handles auth, rate limits, and routing. → [022](phases/03-scaling-basics/022-api-gateway.md)
- **At-least-once / at-most-once**: Messages may be duplicated but never lost / may be lost but never duplicated. → [060](phases/07-async-messaging/060-delivery-semantics.md)
- **Autoscaling**: Automatically adding or removing servers based on load. → [025](phases/03-scaling-basics/025-autoscaling-and-containers.md)
- **Availability**: The percentage of time or requests a system works correctly. → [006](phases/01-foundations/006-availability-and-nines.md)

## B

- **B-tree**: A sorted, balanced tree used for database indexes. Fast lookups and ranges. → [037](phases/05-databases/037-indexes.md)
- **Back-of-envelope estimate**: Quick, rounded math to size a system (QPS, storage, servers). → [005](phases/01-foundations/005-back-of-envelope-estimation.md)
- **Backpressure**: Telling upstream producers to slow down when you can't keep up. → [061](phases/07-async-messaging/061-backpressure-and-load-shedding.md)
- **BASE**: Basically Available, Soft state, Eventually consistent: the looser alternative to ACID. → [053](phases/06-scaling-data/053-consistency-models.md)
- **Batch processing**: Processing a large, bounded set of data on a schedule. → [091](phases/11-advanced-designs/091-batch-vs-stream-processing.md)
- **Bloom filter**: A tiny structure that answers 'definitely not seen' or 'probably seen'. → [090](phases/10-deep-internals/090-probabilistic-data-structures.md)
- **Blue-green deployment**: Two identical environments, where you switch all traffic to the new one and can switch back instantly. → [068](phases/08-reliability-ops/068-deployment-strategies.md)
- **Bulkhead**: Isolating resources per dependency so one failure can't sink everything. → [064](phases/08-reliability-ops/064-circuit-breakers-and-bulkheads.md)

## C

- **Cache hit ratio**: The fraction of requests served from the cache. → [027](phases/04-caching/027-caching-basics.md)
- **Cache stampede**: Many requests missing the same expired key at once and hammering the DB. → [032](phases/04-caching/032-cache-stampede-and-hot-keys.md)
- **Cache-aside**: The app checks the cache, and on a miss loads from the DB and fills the cache. → [028](phases/04-caching/028-cache-aside-and-read-through.md)
- **Canary release**: Sending a small percentage of traffic to a new version first. → [068](phases/08-reliability-ops/068-deployment-strategies.md)
- **CAP theorem**: During a network partition, a system must choose consistency or availability. → [052](phases/06-scaling-data/052-cap-theorem.md)
- **CDC (change data capture)**: Streaming a database's changes by reading its log. → [031](phases/04-caching/031-cache-invalidation.md)
- **CDN**: A network of edge servers that keeps copies of content close to users. → [023](phases/03-scaling-basics/023-cdn.md)
- **Circuit breaker**: Stops calling a failing dependency for a while, failing fast instead. → [064](phases/08-reliability-ops/064-circuit-breakers-and-bulkheads.md)
- **Consistency model**: The rules for what reads can return after writes (strong, causal, eventual…). → [053](phases/06-scaling-data/053-consistency-models.md)
- **Consistent hashing**: Placing keys and servers on a ring so adding or removing a server moves few keys. → [051](phases/06-scaling-data/051-consistent-hashing.md)
- **Consumer group**: A set of consumers sharing a Kafka topic's partitions, each partition read by one member. → [059](phases/07-async-messaging/059-log-based-streaming.md)
- **Count-min sketch**: A fixed-size structure estimating how often items appear. It never under-counts. → [090](phases/10-deep-internals/090-probabilistic-data-structures.md)
- **CQRS**: Separating the write model from read models optimized for queries. → [089](phases/10-deep-internals/089-event-sourcing-and-cqrs.md)
- **CRDT**: A data type that merges concurrent edits automatically and consistently. → [048](phases/06-scaling-data/048-multi-leader-and-leaderless.md)

## D

- **Dead-letter queue (DLQ)**: Where messages go after repeatedly failing processing. → [057](phases/07-async-messaging/057-message-queues.md)
- **Denormalization**: Duplicating data to make reads faster. → [039](phases/05-databases/039-normalization-vs-denormalization.md)
- **DNS**: The internet's phone book: it turns names into IP addresses. → [011](phases/02-networking/011-dns.md)
- **Durability**: Once saved, data survives crashes. → [035](phases/05-databases/035-acid.md)

## E

- **Error budget**: The amount of failure an SLO allows (1 − SLO). → [007](phases/01-foundations/007-sla-slo-sli.md)
- **Event sourcing**: Storing every change as an event, and deriving state by replaying them. → [089](phases/10-deep-internals/089-event-sourcing-and-cqrs.md)
- **Eventual consistency**: Replicas agree eventually, and may briefly return stale data. → [053](phases/06-scaling-data/053-consistency-models.md)

## F

- **Fan-out on write / read**: Push content to followers when posted / assemble it when read. → [076](phases/09-core-case-studies/076-design-news-feed.md)
- **Feature flag**: A switch that turns a feature on or off without redeploying. → [068](phases/08-reliability-ops/068-deployment-strategies.md)
- **Fencing token**: An increasing number the storage checks, to block writes from stale lock holders. → [086](phases/10-deep-internals/086-leader-election-and-locks.md)

## G

- **Geohash**: Encoding a location as a string where a shared prefix means nearby. → [095](phases/11-advanced-designs/095-design-proximity-service.md)
- **Gossip protocol**: Nodes spreading information by telling random peers, like rumours. → [087](phases/10-deep-internals/087-gossip-and-failure-detection.md)
- **GraphQL**: An API where clients ask for exactly the data shape they need. → [015](phases/02-networking/015-rest-grpc-graphql.md)
- **gRPC**: A fast, typed RPC framework using HTTP/2 and Protobuf. → [015](phases/02-networking/015-rest-grpc-graphql.md)

## H

- **Health check**: A periodic probe that tells a load balancer whether a server can serve. → [019](phases/03-scaling-basics/019-load-balancers.md)
- **Hinted handoff**: Temporarily storing writes for a down replica and delivering them later. → [048](phases/06-scaling-data/048-multi-leader-and-leaderless.md)
- **Horizontal scaling**: Adding more machines. → [017](phases/03-scaling-basics/017-vertical-vs-horizontal-scaling.md)
- **Hot key / hotspot**: One key or shard receiving a disproportionate share of traffic. → [032](phases/04-caching/032-cache-stampede-and-hot-keys.md)
- **HTTP status codes**: 2xx success, 3xx redirect, 4xx client error, 5xx server error. → [012](phases/02-networking/012-http-and-status-codes.md)
- **HyperLogLog**: Estimates the number of distinct items using ~12 KB. → [090](phases/10-deep-internals/090-probabilistic-data-structures.md)

## I

- **Idempotency**: Doing an operation twice has the same effect as doing it once. → [055](phases/06-scaling-data/055-idempotency.md)
- **Idempotency key**: A unique ID sent with a request so retries aren't applied twice. → [055](phases/06-scaling-data/055-idempotency.md)
- **Index**: A lookup structure that finds rows without scanning the whole table. → [037](phases/05-databases/037-indexes.md)
- **Inverted index**: A map from each word to the documents containing it. → [043](phases/05-databases/043-search-and-inverted-index.md)
- **Isolation level**: How much concurrent transactions can see of each other. → [036](phases/05-databases/036-transactions-and-isolation.md)

## J

- **JWT**: A signed, self-contained token carrying identity claims. → [069](phases/08-reliability-ops/069-authn-authz-oauth-jwt.md)

## K

- **Kafka**: A distributed, partitioned, replayable log for event streaming. → [059](phases/07-async-messaging/059-log-based-streaming.md)

## L

- **L4 / L7 load balancing**: Routing by IP/port vs by HTTP content (path, headers). → [021](phases/03-scaling-basics/021-l4-l7-and-proxies.md)
- **Lamport clock**: A counter that orders events consistently with cause and effect. → [084](phases/10-deep-internals/084-time-and-clocks.md)
- **Latency**: How long one operation takes. → [004](phases/01-foundations/004-latency-throughput-bandwidth.md)
- **Leader election**: Choosing exactly one node to coordinate. → [086](phases/10-deep-internals/086-leader-election-and-locks.md)
- **Lease**: A lock that expires unless renewed. → [086](phases/10-deep-internals/086-leader-election-and-locks.md)
- **Linearizability**: Strong consistency: behaves like a single up-to-date copy. → [053](phases/06-scaling-data/053-consistency-models.md)
- **Little's Law**: In-flight work = arrival rate × time in the system. → [004](phases/01-foundations/004-latency-throughput-bandwidth.md)
- **Load balancer**: Spreads requests across servers and skips unhealthy ones. → [019](phases/03-scaling-basics/019-load-balancers.md)
- **Load shedding**: Deliberately rejecting excess work to protect the system. → [061](phases/07-async-messaging/061-backpressure-and-load-shedding.md)
- **LRU / LFU**: Evict the least recently / least frequently used item. → [030](phases/04-caching/030-cache-eviction.md)
- **LSM tree**: A write-optimized storage engine: memory buffer → sorted files → compaction. → [081](phases/10-deep-internals/081-btree-lsm-and-wal.md)

## M

- **Merkle tree**: A tree of hashes used to find differences between datasets quickly. → [090](phases/10-deep-internals/090-probabilistic-data-structures.md)
- **Message queue**: A durable to-do list between services. Each message goes to one worker. → [057](phases/07-async-messaging/057-message-queues.md)
- **Microservices**: Small, independently deployable services, each owning its data. → [026](phases/03-scaling-basics/026-monolith-vs-microservices.md)
- **mTLS**: Mutual TLS: both sides present certificates to authenticate each other. → [013](phases/02-networking/013-https-and-tls.md)
- **MVCC**: Keeping multiple row versions so readers don't block writers. → [082](phases/10-deep-internals/082-mvcc.md)

## N

- **N+1 query problem**: One query for a list plus one query per item. → [040](phases/05-databases/040-access-patterns-and-pooling.md)
- **Normalization**: Storing each fact once to avoid inconsistencies. → [039](phases/05-databases/039-normalization-vs-denormalization.md)

## O

- **OAuth 2.0 / OIDC**: Delegated authorization / login on top of OAuth. → [069](phases/08-reliability-ops/069-authn-authz-oauth-jwt.md)
- **Object storage**: Cheap, durable storage of whole files by key (e.g., S3). → [042](phases/05-databases/042-object-storage.md)
- **Observability**: Understanding a system from its metrics, logs, and traces. → [067](phases/08-reliability-ops/067-observability.md)
- **OLTP / OLAP**: Many small transactions / large analytical queries. → [041](phases/05-databases/041-oltp-vs-olap.md)
- **Outbox pattern**: Saving an event in the same DB transaction as the change, then publishing it reliably. → [062](phases/07-async-messaging/062-event-driven-and-outbox.md)

## P

- **PACELC**: If partitioned choose A or C, else choose latency or consistency. → [083](phases/10-deep-internals/083-pacelc.md)
- **Partition (network)**: Nodes that can't communicate with each other. → [052](phases/06-scaling-data/052-cap-theorem.md)
- **Percentile (p50/p99)**: The latency under which 50% / 99% of requests complete. → [003](phases/01-foundations/003-latency-numbers-and-percentiles.md)
- **Pre-signed URL**: A temporary URL letting clients upload or download directly to storage. → [042](phases/05-databases/042-object-storage.md)
- **Pub/sub**: Publishing events to a topic that every subscriber receives. → [058](phases/07-async-messaging/058-pub-sub-and-fan-out.md)

## Q

- **Quorum**: The minimum number of replicas that must agree (e.g., R + W > N). → [054](phases/06-scaling-data/054-quorums.md)

## R

- **Raft**: A consensus algorithm with one leader per term and majority commits. → [085](phases/10-deep-internals/085-consensus-raft.md)
- **Rate limiting**: Capping how many requests a client can make. → [024](phases/03-scaling-basics/024-rate-limiting.md)
- **Read replica**: A copy of the database that serves reads. → [046](phases/06-scaling-data/046-leader-follower-replication.md)
- **Read-your-writes**: A guarantee that you see your own updates immediately. → [047](phases/06-scaling-data/047-replication-lag.md)
- **Redis**: A fast in-memory data store used for caching, counters, queues, and more. → [033](phases/04-caching/033-distributed-caches.md)
- **Replication lag**: How far behind a replica is from the leader. → [047](phases/06-scaling-data/047-replication-lag.md)
- **Reverse proxy**: A server in front of your servers that forwards and protects them. → [021](phases/03-scaling-basics/021-l4-l7-and-proxies.md)
- **RPO / RTO**: Max acceptable data loss / downtime after a disaster. → [066](phases/08-reliability-ops/066-multi-region-and-disaster-recovery.md)

## S

- **Saga**: A multi-step transaction where each step has a compensating undo. → [088](phases/10-deep-internals/088-2pc-vs-sagas.md)
- **Service discovery**: How services find the current addresses of other services. → [071](phases/08-reliability-ops/071-service-discovery-and-mesh.md)
- **Service mesh**: Sidecar proxies that add mTLS, retries, and telemetry to service calls. → [071](phases/08-reliability-ops/071-service-discovery-and-mesh.md)
- **Shard key**: The field that decides which shard a row lives on. → [050](phases/06-scaling-data/050-shard-keys-and-hotspots.md)
- **Sharding**: Splitting data across machines, each holding a slice. → [049](phases/06-scaling-data/049-sharding.md)
- **SLA / SLO / SLI**: The promise / the target / the measurement of reliability. → [007](phases/01-foundations/007-sla-slo-sli.md)
- **Snowflake ID**: A 64-bit, time-sortable unique ID (time + machine + sequence). → [072](phases/08-reliability-ops/072-unique-id-generation.md)
- **SPOF**: A single point of failure: if it dies, everything dies. → [065](phases/08-reliability-ops/065-redundancy-and-failover.md)
- **SSE (Server-Sent Events)**: A one-way stream of updates from server to client over HTTP. → [016](phases/02-networking/016-real-time-communication.md)
- **Stateless service**: A server that keeps no per-user memory between requests. → [018](phases/03-scaling-basics/018-stateless-services.md)
- **Stream processing**: Continuously processing unbounded data as it arrives. → [091](phases/11-advanced-designs/091-batch-vs-stream-processing.md)

## T

- **TCP / UDP**: Reliable ordered connection / fast fire-and-forget datagrams. → [010](phases/02-networking/010-tcp-vs-udp.md)
- **Throughput**: How many operations complete per second. → [004](phases/01-foundations/004-latency-throughput-bandwidth.md)
- **TLS**: Encryption + identity + integrity for network connections (the S in HTTPS). → [013](phases/02-networking/013-https-and-tls.md)
- **Token bucket**: A rate-limit algorithm: tokens refill steadily, and each request spends one. → [024](phases/03-scaling-basics/024-rate-limiting.md)
- **Trie**: A prefix tree, used for autocomplete. → [093](phases/11-advanced-designs/093-design-autocomplete.md)
- **TTL**: Time to live: how long a cached item or DNS record stays valid. → [030](phases/04-caching/030-cache-eviction.md)
- **Two-phase commit (2PC)**: A coordinator makes all participants prepare, then commit together. → [088](phases/10-deep-internals/088-2pc-vs-sagas.md)

## V

- **Vector clock**: Per-node counters that detect whether events are ordered or concurrent. → [084](phases/10-deep-internals/084-time-and-clocks.md)
- **Vertical scaling**: Using a bigger machine. → [017](phases/03-scaling-basics/017-vertical-vs-horizontal-scaling.md)
- **Virtual nodes (vnodes)**: Many ring positions per server, for balance in consistent hashing. → [051](phases/06-scaling-data/051-consistent-hashing.md)

## W

- **Watermark (streaming)**: A signal that all events up to a time have probably arrived. → [091](phases/11-advanced-designs/091-batch-vs-stream-processing.md)
- **WebSocket**: A persistent, two-way connection between client and server. → [016](phases/02-networking/016-real-time-communication.md)
- **Write-ahead log (WAL)**: An append-only log written before data changes, for crash recovery. → [081](phases/10-deep-internals/081-btree-lsm-and-wal.md)
- **Write-back / write-through / write-around**: Cache writes: later / together / skip the cache. → [029](phases/04-caching/029-cache-write-strategies.md)

---

119 terms · 🏠 [README](README.md) · 🧭 [Coverage map](COVERAGE.md)
