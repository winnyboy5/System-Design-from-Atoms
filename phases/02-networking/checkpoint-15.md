# ✅ Checkpoint 15%: Speaking the Web's Language

> ⏱ 15 min · Covers lessons **011–015** · 📈 You're at **15%**
>
> `███░░░░░░░░░░░░░░░░░` Three checkpoints down. You now know how names, requests, security, and APIs work.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Pantry now speaks the web's language fluently. Before customers start demanding live updates, let's review together.*

---

## ⚡ Part 1: Recall (5 questions)

1. What's the DNS resolution chain after the browser and OS caches miss?
<details><summary>Answer</summary>

Recursive resolver → root server → TLD server (.com) → authoritative server → answer cached per TTL.
</details>

2. What do 502, 503, and 504 each mean?
<details><summary>Answer</summary>

502: the upstream gave a bad or invalid response. 503: the service is unavailable/overloaded. 504: the upstream timed out.
</details>

3. What are TLS's three jobs?
<details><summary>Answer</summary>

Encryption (confidentiality), authentication (certificate), integrity (tamper detection).
</details>

4. Rewrite `POST /deleteUser?id=7` RESTfully.
<details><summary>Answer</summary>

`DELETE /users/7` → `204 No Content`.
</details>

5. When would you choose gRPC over REST?
<details><summary>Answer</summary>

Internal service-to-service calls needing speed, strict contracts, streaming, and deadlines.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "What happens, step by step, when my weather app asks a server for today's forecast, and how does the app know it's talking to the *real* weather company?"

Must include: **DNS, HTTPS/TLS certificate, HTTP GET, status code, JSON response**.

---

## 🛠️ Part 3: Mini-design

**Design the API for a simple event-ticketing app.** Users browse events, see one event, buy tickets, and list their tickets.

Write 4–5 endpoints with method, path, and success status. Include pagination and make "buy" safe to retry.

<details><summary>One good answer</summary>

```
GET  /v1/events?city=berlin&limit=20&cursor=...  → 200 {data, next_cursor}
GET  /v1/events/{id}                             → 200
POST /v1/events/{id}/orders                      → 201  (Idempotency-Key header!)
     body: {"quantity": 2, "seat_ids": [...]}
GET  /v1/me/tickets?limit=20&cursor=...          → 200
DELETE /v1/orders/{id}  (refund/cancel)          → 202 Accepted (processed async)
```
Also: `409 Conflict` if seats are already taken, and `429` if rate limited.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "How do you make a public API safe to evolve over years?"**
<details><summary>Model answer</summary>

Version from day one (`/v1`). Make only additive changes within a version. Document deprecations with sunset headers and timelines. Use consistent error formats. Contract tests. Never change the meaning of an existing field.
</details>

**Q2. "DNS or a load balancer for distributing traffic? Explain."**
<details><summary>Model answer</summary>

DNS spreads traffic coarsely (across regions or LBs) but is cached and health-unaware. A load balancer makes per-request, health-aware decisions. Use both: DNS/GeoDNS/Anycast → regional LB → servers.
</details>

**Q3. "What's the performance cost of HTTPS, and is it worth it?"**
<details><summary>Model answer</summary>

About 1 extra RTT for the TLS 1.3 handshake plus a little CPU, reduced further by keep-alive, session resumption, HTTP/2 or 3, and edge termination. It's absolutely worth it: privacy, integrity, SEO, and browser requirements.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [016 · Real-time Communication](016-real-time-communication.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [011](011-dns.md), [012](012-http-and-status-codes.md), [014](014-rest-api-design.md) |

---

⬅️ [015 · REST vs gRPC vs GraphQL](015-rest-grpc-graphql.md) · 🗺️ [Phase map](README.md) · ➡️ [016 · Real-time Communication](016-real-time-communication.md)

✅ Tick **Checkpoint 15%** in [PROGRESS.md](../../PROGRESS.md). 🎉
