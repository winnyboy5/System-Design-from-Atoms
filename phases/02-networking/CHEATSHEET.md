# 📌 Phase 02 Cheatsheet: Networking Essentials

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 009 | **IP = building, port = apartment, packets = envelopes, routers = post offices.** Keep DBs on private IPs. |
| 010 | **TCP = phone call** (reliable, ordered). **UDP = postcards** (fast, lossy). Ask: is late data worse than lost data? |
| 011 | **DNS = phone book**: resolver → root → TLD → authoritative, **cached by TTL**. Lower the TTL before migrations. |
| 012 | HTTP = method + path + headers + body → status + headers + body. **Stateless.** |
| 013 | TLS = **encryption + identity + integrity**. Terminate at the LB/CDN. mTLS inside for zero-trust. |
| 014 | REST: **nouns in URLs, verbs as methods**, cursor pagination, version from day one, idempotency keys. |
| 015 | **Public → REST · internal → gRPC · varied front-ends → GraphQL.** |
| 016 | **Polling → long polling → SSE (one-way) → WebSocket (two-way).** Sockets make servers stateful. |

## 🔢 Numbers & codes

```
Ports: 80 HTTP · 443 HTTPS · 22 SSH · 53 DNS · 5432 Postgres · 3306 MySQL · 6379 Redis · 9092 Kafka
MTU ≈ 1,500 B · IPv4 ≈ 4.3B addresses · ports 0–65,535
TCP handshake 1 RTT · TLS 1.3 +1 RTT (0-RTT resume) · TLS 1.2 +2 RTT
Uncached DNS ≈ 20–120 ms
Protobuf ≈ 3–10× smaller than JSON
```

| Code | Meaning | Code | Meaning |
|---|---|---|---|
| 200 | OK | 401 | Who are you? (unauthenticated) |
| 201 | Created | 403 | I know you, but no (forbidden) |
| 204 | No content | 404 | Not found |
| 301 | Moved permanently | 409 | Conflict |
| 302 | Found (temporary) | 429 | Too many requests |
| 304 | Not modified (use cache) | 500 | Server bug |
| 400 | Bad request | 502 / 503 / 504 | Bad gateway / unavailable / gateway timeout |

## 🪄 Tricks

- "**2 good, 3 go elsewhere, 4 you messed up, 5 we messed up.**"
- **Idempotent methods:** GET, PUT, DELETE. **POST is not**, so add an `Idempotency-Key`.
- **HTTP/1.1 → 2 (multiplexing) → 3 (QUIC over UDP, no head-of-line blocking).**
- **Cursor pagination:** `WHERE id > :last ORDER BY id LIMIT n`.
- WebSocket scaling = **gateways + connection registry + pub/sub**.

## ⚠️ Top mistakes

- Using DNS as a real-time failover mechanism (caching delays it).
- `200 OK` with an error in the body.
- Offset pagination on huge tables.
- Picking WebSockets when SSE or polling is enough.
- Forgetting certificate-expiry monitoring.

---

🗺️ [Phase map](README.md) · 🎤 [Interview questions](INTERVIEW-QUESTIONS.md)
