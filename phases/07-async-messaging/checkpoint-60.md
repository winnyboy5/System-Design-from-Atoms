# ✅ Checkpoint 60%: 🎉 Level-Up! Systems That Talk Asynchronously

> ⏱ 15 min · Covers lessons **056–060** · 📈 You're at **60%**
>
> `████████████░░░░░░░░` 🎉 **60%!** Three-fifths done. Queues, pub/sub, and Kafka are now in your toolbox.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *The dinner rush no longer knocks Pantry over. Maya sleeps well tonight. Let's review before the next storm.*

---

## ⚡ Part 1: Recall (5 questions)

1. Which steps of a checkout should be synchronous?
<details><summary>Answer</summary>

Only what the user must wait for: validating the cart, reserving inventory, authorizing payment, and creating the order. Emails, analytics, and fulfilment go async.
</details>

2. In a queue, what happens if a worker crashes before acknowledging a message?
<details><summary>Answer</summary>

After the visibility timeout, the message becomes visible again and is redelivered to another worker (at-least-once).
</details>

3. Queue vs pub/sub?
<details><summary>Answer</summary>

A queue gives each message to one consumer (work distribution). Pub/sub gives every subscriber a copy (broadcast).
</details>

4. How does Kafka preserve order for a given order ID?
<details><summary>Answer</summary>

It uses the order ID as the message key, so all its events go to the same partition, which is strictly ordered.
</details>

5. How do you get exactly-once *effects*?
<details><summary>Answer</summary>

At-least-once delivery + idempotent processing (dedupe by message ID, ideally in the same transaction as the state change).
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "How can a shop take thousands of orders in one minute during a sale without its computers crashing, and without sending anyone two confirmation emails?"

Must include: **queue, workers, spike absorption, retries, duplicates, idempotency**.

---

## 🛠️ Part 3: Mini-design

**A ride-sharing app** emits `TripCompleted` events. Consumers: billing (charge the rider), driver payouts, receipts by email, analytics, and fraud scoring. Traffic: 5k trips/s at peak.

Pick the messaging technology, the partition key, the delivery semantics per consumer, and the duplicate handling.

<details><summary>One good answer</summary>

- **Kafka** topic `trip-events`, key = `trip_id` (ordered per trip), ~64 partitions.
- Consumer groups: `billing`, `payouts`, `receipts`, `analytics`, `fraud`, each reading independently.
- Billing and payouts: **at-least-once + idempotency** (a unique `trip_id` charge record, and the idempotency key sent to the payment provider).
- Receipts: at-least-once + a `receipt_sent(trip_id)` dedup table.
- Analytics: at-least-once, and duplicates are removed downstream (dedup by event_id in the warehouse).
- Fraud: at-least-once, with idempotent scoring.
- The producer publishes via the **outbox** (lesson 062).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "What happens if your message broker goes down?"**
<details><summary>Model answer</summary>

Producers can't publish: buffer locally or in an outbox table, retry with backoff, or fail the request if it's critical. Consumers pause. Durable, replicated brokers (Kafka RF=3, multi-AZ, managed SQS) make this rare. On recovery, the backlog drains, so watch the downstream load.
</details>

**Q2. "How do you handle a poison message that always fails?"**
<details><summary>Model answer</summary>

Limited retries with backoff → move it to a dead-letter queue/topic → alert → inspect, fix the bug, and replay. For Kafka, write to a DLQ topic and continue, so one bad message doesn't block the partition.
</details>

**Q3. "How do you choose the number of Kafka partitions?"**
<details><summary>Model answer</summary>

Target throughput ÷ per-consumer throughput gives the minimum parallelism. Add headroom for growth (increasing partitions later changes key mapping). Consider the broker limits and key distribution skew.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [061 · Backpressure & Load Shedding](061-backpressure-and-load-shedding.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [057](057-message-queues.md), [059](059-log-based-streaming.md), [060](060-delivery-semantics.md) |

🏆 **Level-up reward:** 60%. Only 20% of the core left before the 🏁 Practical Mastery gate!

---

⬅️ [060 · Delivery Semantics](060-delivery-semantics.md) · 🗺️ [Phase map](README.md) · ➡️ [061 · Backpressure & Load Shedding](061-backpressure-and-load-shedding.md)

✅ Tick **Checkpoint 60%** in [PROGRESS.md](../../PROGRESS.md). 🎉
