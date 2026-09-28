# 📌 Phase 11 Cheatsheet: Data Processing & Advanced Designs

## 🏗️ Design one-liners

| # | System | Core insight | Key techniques |
|---|---|---|---|
| 091 | **Batch vs stream** | Bounded & cheap vs unbounded & fresh | MapReduce/Spark · Flink windows, watermarks, checkpoints · Lambda vs Kappa |
| 092 | **KV store (Dynamo)** | AP, always writable, self-healing | Ring + vnodes · N/R/W · vector clocks · hinted handoff · read repair · Merkle · gossip · LSM |
| 093 | **Autocomplete** | Precompute top-K per prefix | Trie with cached top-K · offline build + trending layer · CDN for short prefixes · debounce |
| 094 | **Web crawler** | A polite, deduplicated frontier | Priority + per-host queues · robots.txt · Bloom seen-URLs · SimHash · trap defenses |
| 095 | **Proximity / ride-sharing** | Index 2D space. Moving objects in memory | Geohash + 8 neighbours · quadtree/H3 · Redis GEO · ETA ranking · atomic driver reservation |
| 096 | **Payments** | Correct exactly once | Idempotency everywhere · state machine · double-entry ledger · outbox/saga · reconciliation |
| 097 | **Job scheduler** | Find due jobs, lease them, retry | next_run_at index + SKIP LOCKED / sorted sets / timing wheels · leases + fencing · idempotent jobs |
| 098 | **Ticket booking** | Contention on a few rows + a stampede | HELD with TTL · atomic conditional holds · waiting room · bot defense · saga checkout |
| 099 | **Top-K / trending** | Partition by item, merge local top-Ks | Count-min + heap · sliding buckets · velocity scoring · Redis serving · batch exact |
| 100 | **Capstone** | Design → teach → find gaps → revise | Design doc template · trade-off tables · mock interviews |

## 🔢 Numbers

```
Geohash: 5 chars ≈ 4.9 km · 6 ≈ 1.2 × 0.6 km · 7 ≈ 153 m
Bloom 1% FP ≈ 9.6 bits/item → 1B URLs ≈ 1.2 GB
Seat holds ~5–10 min · driver location every ~4 s
Stream watermark delay: seconds · Flink checkpoints: seconds–minutes
Count-min error: ≤ ε·N with width e/ε, depth ln(1/δ)
```

## 🪄 Tricks

- **Precompute for reads, stream for freshness, batch for truth.**
- **Anything with money or seats → strongly consistent + idempotent + reconciled.**
- **Anything moving fast (locations, counters) → in memory, overwrite, stream the history.**
- **Protect the core from stampedes → waiting room + cache + rate limit.**
- **Partition by the thing you aggregate** (item_id for top-K, host for crawling, event for tickets).

## ⚠️ Top mistakes

- Merging top-Ks from randomly partitioned streams.
- Retrying a payment with a new idempotency key after a timeout.
- Persisting every location ping to the OLTP DB.
- Crawling without politeness.
- Promising exactly-once job execution.
- Using the main search engine for per-keystroke suggestions.

---

🗺️ [Phase map](README.md) · 🎤 [Interview questions](INTERVIEW-QUESTIONS.md)
