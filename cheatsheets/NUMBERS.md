# 📌 Numbers Every System Designer Should Know

> Don't memorize exact values. Memorize the **orders of magnitude**, meaning which things are about 1000× slower than others.
> Values are rough and hardware-dependent. They're fine for back-of-envelope math, not for benchmarks.

---

## ⏱️ Latency ladder

| Operation | Time | "Human scale" if 1 ns = 1 second |
|---|---|---|
| L1 cache reference | ~1 ns | 1 second |
| Branch mispredict | ~3 ns | 3 seconds |
| L2 cache reference | ~4 ns | 4 seconds |
| Mutex lock/unlock | ~17 ns | 17 seconds |
| Main memory (RAM) reference | ~100 ns | 1.5 minutes |
| Compress 1 KB (fast codec) | ~2 µs | 33 minutes |
| Send 1 KB over 10 Gbps network | ~1 µs | 17 minutes |
| Read 4 KB randomly from NVMe SSD | ~20–100 µs | 5 hours – 1 day |
| Read 1 MB sequentially from RAM | ~3–10 µs | ~1–3 hours |
| Read 1 MB sequentially from SSD | ~50–200 µs | ~1–2 days |
| Round trip inside same datacenter | ~0.5 ms | 6 days |
| Disk (HDD) seek | ~2–10 ms | 1–4 months |
| Read 1 MB sequentially from HDD | ~5 ms | 2 months |
| Round trip same region (different AZ) | ~1–2 ms | 2–3 weeks |
| Round trip US East ↔ US West | ~60–70 ms | 2 years |
| Round trip US ↔ Europe | ~80–100 ms | 3 years |
| Round trip US ↔ Australia | ~150–200 ms | 5–6 years |

### 🧠 The 5 takeaways

1. **Memory is fast, disk is slow, and network across the world is slowest.** Each step up the ladder is about 10–1000× slower.
2. **RAM ≈ 100 ns, SSD ≈ 100 µs, network hop ≈ 0.5 ms, cross-continent ≈ 100 ms.** Remember "100, 100, half, 100".
3. **Sequential reads are far faster than random reads**, especially on HDDs.
4. **Compress before you send** over slow links. CPU is cheap compared with the network.
5. **The speed of light is a hard limit.** You can't make cross-continent calls faster, so move the data closer instead (CDNs, regions).

---

## 🔢 Powers of two (data sizes)

| Power | Exact | Approx | Name |
|---|---|---|---|
| 2¹⁰ | 1,024 | ~1 thousand | 1 KB |
| 2²⁰ | 1,048,576 | ~1 million | 1 MB |
| 2³⁰ | ~1.07 billion | ~1 billion | 1 GB |
| 2⁴⁰ | | ~1 trillion | 1 TB |
| 2⁵⁰ | | ~1 quadrillion | 1 PB |

**Trick:** 2¹⁰ ≈ 10³. So every "×1024" is about "×1000", and you can switch between powers of 2 and powers of 10 freely.

### How big are common things?

| Thing | Size |
|---|---|
| ASCII character | 1 byte |
| Unicode char (UTF-8, typical) | 1–4 bytes |
| Integer (int32 / int64) | 4 / 8 bytes |
| UUID | 16 bytes (36 as text) |
| Timestamp | 8 bytes |
| A tweet-sized text post + metadata | ~300 bytes – 1 KB |
| A typical JSON API response | 1–10 KB |
| A web page (HTML) | ~100 KB |
| A compressed photo | ~200 KB – 2 MB |
| 1 minute of HD video (compressed) | ~50–100 MB |
| 1 hour of 1080p streaming | ~1.5–3 GB |

---

## 📅 Time conversions

| Period | Seconds | Easy approximation |
|---|---|---|
| 1 minute | 60 | |
| 1 hour | 3,600 | ~4 × 10³ |
| 1 day | 86,400 | **~10⁵** (round up!) |
| 1 month | ~2.6 million | ~2.5 × 10⁶ |
| 1 year | ~31.5 million | **~3 × 10⁷** |

### Requests per day → requests per second (QPS)

| Per day | Per second (avg) |
|---|---|
| 1 million | ~12 |
| 10 million | ~120 |
| 100 million | ~1,200 |
| 1 billion | ~12,000 |

**Trick:** divide by 100,000 (10⁵), which gives about 10 QPS per million/day. **Peak ≈ 2–3× average.**

---

## 🟢 Availability: nines → downtime

| Availability | Downtime / year | Downtime / month | Downtime / day |
|---|---|---|---|
| 90% (one nine) | 36.5 days | 72 hours | 2.4 hours |
| 99% (two nines) | 3.65 days | 7.2 hours | 14.4 min |
| 99.9% (three nines) | 8.76 hours | 43.8 min | 1.44 min |
| 99.95% | 4.38 hours | 21.9 min | 43 s |
| 99.99% (four nines) | 52.6 min | 4.38 min | 8.6 s |
| 99.999% (five nines) | 5.26 min | 26.3 s | 0.86 s |

**Trick:** each extra nine means **10× less downtime**. Three nines ≈ 9 hours/year. Four nines ≈ 1 hour/year. Five nines ≈ 5 minutes/year.

**Combining components:**
- **In series** (A needs B): multiply. 99.9% × 99.9% ≈ **99.8%**, so it gets *worse*.
- **In parallel** (either works): 1 − (1 − A)². Two 99% replicas give 1 − 0.01² = **99.99%**, so it gets *better*.

---

## 🏎️ Rough throughput of common components (one decent node)

| Component | Ballpark |
|---|---|
| Web/app server (simple API) | 1k – 10k+ requests/s |
| Nginx serving static files | 10k – 100k+ requests/s |
| PostgreSQL / MySQL (simple indexed reads) | 5k – 50k queries/s |
| PostgreSQL / MySQL (writes, with durability) | 1k – 10k+ writes/s |
| Redis / Memcached | 100k – 1M ops/s |
| Kafka (one broker) | 100k – 1M+ messages/s (~100s MB/s) |
| Cassandra (one node, writes) | 10k – 50k+ writes/s |
| Elasticsearch (search queries) | 1k – 10k queries/s |
| SSD sequential read | 0.5 – 7 GB/s |
| HDD sequential read | 100 – 200 MB/s |
| 1 Gbps network | ~125 MB/s |
| 10 Gbps network | ~1.25 GB/s |

**Trick:** "A single good machine does about 10k simple requests/s. Redis does about 100k+. Kafka does about 1M." Use these to work out *how many machines* you need.

---

## 💾 Storage & server rules of thumb

- A commodity server: **32–256 GB RAM, 8–64 cores, several TB of SSD.**
- One DB node comfortably holds **1–10 TB**. Beyond that, think about sharding.
- **Replication factor 3** is the common default, so multiply storage by 3.
- Plan for **headroom**: run at ≤ 50–70% capacity so spikes and failures don't topple you.

---

## 🌐 Network & web

| Thing | Number |
|---|---|
| TCP handshake | 1 round trip (RTT) |
| TLS 1.3 handshake | +1 RTT (0 RTT on resume) |
| TLS 1.2 handshake | +2 RTT |
| Typical DNS lookup (uncached) | 20–120 ms |
| Max TCP/UDP port number | 65,535 |
| Ethernet MTU | 1,500 bytes |
| Human "feels instant" | < 100 ms |
| Human "flow is interrupted" | > 1 s |
| Human gives up | > 10 s |

---

## 🆔 IDs & encoding

| Thing | Number |
|---|---|
| Base62 chars `[a-zA-Z0-9]` | 62 |
| 62⁶ | ~56.8 billion |
| 62⁷ | ~3.5 trillion |
| 2³² | ~4.3 billion (IPv4 addresses, int32 range) |
| 2⁶⁴ | ~1.8 × 10¹⁹ |
| Snowflake ID | 64 bits = 41 time + 10 machine + 12 sequence |
| Snowflake per machine | 4,096 IDs per millisecond |

---

➡️ Next sheet: [ESTIMATION-TRICKS.md](ESTIMATION-TRICKS.md)
