# 🎤 Phase 09 Interview Question Bank: Core Case Studies

> These are **full design prompts** plus the **follow-ups interviewers love**. Practise each prompt in 35–45 minutes using the framework, then check the key points.
> 🟢 warm-up · 🟡 standard · 🔴 deep-dive

---

### 🟢 1. Design Pastebin. · [073, 074]
<details><summary>Key points</summary>

Metadata (KV) + content in object storage. Base62 IDs via range allocation. CDN + Redis for hot pastes. Expiry via lazy checks + a sweeper + lifecycle rules. Size and rate limits. Unguessable IDs for private pastes.
</details>

### 🟢 2. Follow-up: "301 or 302 for the URL shortener?" · [074]
<details><summary>Key points</summary>

301 is cached by browsers (less load, but you lose per-click analytics). 302 hits the service each time (analytics). The choice depends on the product goals.
</details>

### 🟡 3. Design Twitter's home timeline. · [076]
<details><summary>Key points</summary>

Posts sharded by Snowflake ID. The follow graph in both directions. Hybrid fan-out (push to active followers of normal accounts, pull for celebrities). Redis lists of post IDs. Batch hydration. Ranking service. Lazy delete filtering. Read-your-writes for the author.
</details>

### 🟡 4. Design WhatsApp. · [077]
<details><summary>Key points</summary>

WebSocket gateways. Session registry. Persist-then-ack in wide-column storage by conversation. A per-conversation sequence. Delivered/read receipts. Offline push. Cursor-based sync for multi-device. Small groups fan-out, large groups pull. E2EE via the Signal protocol.
</details>

### 🟡 5. Design a notification service for an e-commerce company. · [078]
<details><summary>Key points</summary>

An async API with idempotency keys. Priority queues (bulkheads). Preferences, quiet hours, frequency caps. Templates. Per-channel workers with retries, breakers, and fallback providers. Dedup per channel. A delivery log plus provider callbacks. Throttled campaigns.
</details>

### 🟡 6. Design YouTube (upload + watch). · [079]
<details><summary>Key points</summary>

Resumable multipart uploads to object storage. A parallel transcoding DAG → HLS/DASH renditions. Metadata DB. CDN delivery with ABR. View counting via stream aggregation. Search and recommendations. Egress cost focus. Tiered storage.
</details>

### 🟡 7. Design Dropbox. · [080]
<details><summary>Key points</summary>

Content-defined chunking + SHA-256 dedup. Upload only the missing chunks. A strongly consistent metadata DB sharded by namespace with a journal. Notify + pull by cursor. Conflicted copies. Refcount GC. Sharing ACLs.
</details>

### 🟡 8. Design an API rate limiter. · [075]
<details><summary>Key points</summary>

Gateway middleware. Token bucket in Redis via Lua. Rules per tier and endpoint. 429 + headers. Fail-open with local limits (fail-closed for security limits). Local leasing for scale. Hot-key handling.
</details>

### 🔴 9. Follow-up: "A celebrity with 100M followers posts. What happens in your feed design?" · [076]
<details><summary>Key points</summary>

No push fan-out. Their post goes to a cached celebrity-posts list. Followers merge it at read time. The post body is cached hot everywhere (L1 + CDN for media). Counters are sharded.
</details>

### 🔴 10. Follow-up: "How do you guarantee chat message ordering across devices?" · [077]
<details><summary>Key points</summary>

A server-assigned, monotonically increasing sequence per conversation, persisted with the message. Clients render by seq and detect gaps to request the missing ranges. Client timestamps aren't trusted for order.
</details>

### 🔴 11. Follow-up: "Your transcoding backlog is 6 hours during a spike. What do you do?" · [079]
<details><summary>Key points</summary>

Autoscale workers on queue depth (spot instances). Prioritize: publish low renditions first, and prioritize popular creators. Split into smaller chunks for more parallelism. Defer expensive codecs (AV1) for low-view videos. Hardware encoders.
</details>

### 🔴 12. Follow-up: "Two devices edit the same file offline. What happens in Dropbox?" · [080]
<details><summary>Key points</summary>

Both commit against base version N. The first commit wins (N+1). The second detects a base mismatch and creates a conflicted copy for the user to resolve. No silent overwrite. For real-time co-editing, use OT/CRDTs instead.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · 🏁 [Checkpoint 80%](checkpoint-80.md)
