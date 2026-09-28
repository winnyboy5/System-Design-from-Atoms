# 🎤 Phase 02 Interview Question Bank: Networking Essentials

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. TCP vs UDP: give one use case for each. · [010]
<details><summary>Model answer</summary>

TCP: web/APIs, databases, file transfers (need reliable, ordered delivery). UDP: video calls, games, DNS (need low latency, and can tolerate loss).
</details>

### 🟢 2. What's the difference between PUT and PATCH? · [012, 014]
<details><summary>Model answer</summary>

PUT replaces the whole resource (idempotent). PATCH applies a partial update (idempotent or not, depending on how it's designed).
</details>

### 🟢 3. What is a DNS TTL and why lower it before a migration? · [011]
<details><summary>Model answer</summary>

It's how long answers are cached. Lowering it ahead of time means that when you switch the record, clients pick up the new IP within minutes instead of hours.
</details>

### 🟢 4. What does TLS termination mean? · [013]
<details><summary>Model answer</summary>

Decrypting HTTPS at a proxy or load balancer, and forwarding to backends over plain HTTP or re-encrypted connections.
</details>

### 🟡 5. Offset vs cursor pagination: when does offset break down? · [014]
<details><summary>Model answer</summary>

Deep offsets force the DB to scan and discard many rows (slow), and inserts or deletes between page loads cause duplicates or skipped items. Cursor/keyset pagination uses an indexed `WHERE id > last` and stays fast and stable.
</details>

### 🟡 6. How would you design a public API so retries never double-charge? · [012, 014]
<details><summary>Model answer</summary>

Require an `Idempotency-Key` on POSTs that create or charge. Store key → result (with a TTL). On a duplicate key, return the stored result. Use a DB unique constraint to guard against races. Clients retry with backoff and jitter.
</details>

### 🟡 7. REST, gRPC, or GraphQL for a mobile app with complex screens? · [015]
<details><summary>Model answer</summary>

GraphQL or a BFF, so each screen fetches exactly what it needs in one round trip (important on mobile networks). Internally, services talk gRPC. Mention the costs: GraphQL caching, N+1 (DataLoader), and query-cost limits.
</details>

### 🟡 8. SSE vs WebSockets for a live sports score page? · [016]
<details><summary>Model answer</summary>

SSE. Data flows only server → client, it's simpler, works over standard HTTP infrastructure, and auto-reconnects. WebSockets are only needed for two-way interaction.
</details>

### 🟡 9. A service returns lots of 502s after a deploy. What are the likely causes? · [012]
<details><summary>Model answer</summary>

The upstream is crashing or closing connections (a bad build, a port mismatch, a failing health check), keep-alive timeout mismatches between the LB and the app, or the app not listening yet (readiness). Check the app logs, the LB target health, and the readiness probes.
</details>

### 🔴 10. How do you scale WebSocket connections to 10M users? · [016]
<details><summary>Model answer</summary>

A gateway fleet (tuned for ~50–100k+ connections each, with file-descriptor and memory tuning), an L4 LB that supports long-lived connections, a connection registry (Redis) mapping user → gateway, pub/sub or direct RPC to deliver cross-gateway messages, heartbeats to clean up dead connections, graceful draining and jittered reconnects on deploy, and a fallback to push notifications for offline users.
</details>

### 🔴 11. Explain HTTP/1.1 → HTTP/2 → HTTP/3 and why each change happened. · [010, 012]
<details><summary>Model answer</summary>

HTTP/1.1: one in-flight request per connection, so browsers opened about 6 connections per host. HTTP/2: binary framing, multiplexing many streams over one TCP connection, header compression. But TCP head-of-line blocking stalls all streams on packet loss. HTTP/3: QUIC over UDP with independent streams, built-in TLS 1.3, faster handshakes, and connection migration.
</details>

### 🔴 12. How would you route users to the nearest region and fail over if one dies? · [011]
<details><summary>Model answer</summary>

Latency-based/GeoDNS or Anycast to reach the closest healthy edge, with health checks that drop failed regions from DNS answers (with low TTLs) or withdraw Anycast routes. Regional LBs sit behind that. Data must be replicated across regions for failover to be useful (lesson 066). Mention DNS caching delays, and that Anycast fails over faster.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 03 Scaling basics](../03-scaling-basics/README.md)
