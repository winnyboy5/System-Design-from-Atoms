# 🎤 Phase 07 Interview Question Bank: Async & Messaging

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. Why use a message queue? · [057]
<details><summary>Model answer</summary>

To decouple producers from consumers, absorb traffic spikes (load levelling), retry failed work reliably, and scale workers independently.
</details>

### 🟢 2. Queue vs pub/sub? · [058]
<details><summary>Model answer</summary>

A queue delivers each message to one consumer (work distribution). Pub/sub delivers each message to all subscribers (broadcast).
</details>

### 🟢 3. What is a dead-letter queue? · [057]
<details><summary>Model answer</summary>

A queue where messages go after repeatedly failing processing, so they don't block the main flow and can be inspected or replayed.
</details>

### 🟢 4. What's consumer lag in Kafka? · [059]
<details><summary>Model answer</summary>

The difference between the latest offset in a partition and the consumer group's committed offset, which shows how far behind processing is.
</details>

### 🟡 5. Explain at-least-once vs at-most-once, and how you'd achieve exactly-once effects. · [060]
<details><summary>Model answer</summary>

At-most-once: ack before processing, so messages can be lost. At-least-once: ack after, so duplicates are possible. Exactly-once effects: at-least-once plus idempotent processing (dedupe IDs in the same transaction), or Kafka transactions for Kafka-to-Kafka pipelines.
</details>

### 🟡 6. How does Kafka guarantee ordering? · [059]
<details><summary>Model answer</summary>

Only within a partition. Use a message key (the entity ID) so related events land in the same partition, and one consumer per partition per group processes them in order.
</details>

### 🟡 7. What is the transactional outbox pattern and why is it needed? · [062]
<details><summary>Model answer</summary>

It solves the dual-write problem. Write the event to an outbox table in the same transaction as the state change, and a relay (polling or CDC) publishes it to the broker. It guarantees events aren't lost or phantom.
</details>

### 🟡 8. How do you handle a sudden 10× traffic spike on an async pipeline? · [057, 061]
<details><summary>Model answer</summary>

The queue absorbs the burst. Autoscale consumers on depth or lag. Protect downstream systems with concurrency limits. Shed or defer low-priority work. Make sure the retention or queue limits cover the backlog.
</details>

### 🟡 9. Choreography vs orchestration? · [062]
<details><summary>Model answer</summary>

Choreography: services react to events independently (decoupled, harder to see the whole flow). Orchestration: a coordinator directs the steps (explicit, easier error handling and compensation, more coupling to the orchestrator).
</details>

### 🔴 10. Design a notification pipeline that handles 1M events/minute with per-user ordering and no duplicates. · [057–060]
<details><summary>Model answer</summary>

Kafka topic keyed by user_id, with partitions sized for throughput. Consumer groups per channel (push, email, SMS). Per-user ordering from the partitions. Idempotent sends via a `(event_id, channel)` dedup table plus provider idempotency keys. Retries with backoff via retry topics, then a DLQ. Rate limiting per provider and per user (quiet hours, preferences). Monitor lag.
</details>

### 🔴 11. Why can unbounded queues cause total outages, and how do you design admission control? · [061]
<details><summary>Model answer</summary>

Under sustained overload, queues grow, wait times exceed client timeouts, and all the work gets wasted (goodput → 0) while memory fills. Design: bounded queues, concurrency limits (adaptive), priority classes, queue-time-based shedding, deadline propagation, fast 503/429 with Retry-After, and client backoff with jitter.
</details>

### 🔴 12. Compare RabbitMQ, SQS, and Kafka for three different use cases. · [057–059]
<details><summary>Model answer</summary>

Background jobs with retries and delays → SQS (managed) or RabbitMQ (routing, priorities). Many independent consumers, replay, CDC, event sourcing → Kafka. Complex routing (topic exchanges, per-message TTLs) at moderate throughput → RabbitMQ. Discuss ordering, retention, throughput, and the ops burden.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 08 Reliability, security & ops](../08-reliability-ops/README.md)
