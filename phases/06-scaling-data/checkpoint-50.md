# ✅ Checkpoint 50%: 🎉🎉 HALFWAY! Big Level-Up!

> ⏱ 20 min · Covers lessons **046–050** (and a halfway review) · 📈 You're at **50%**
>
> `██████████░░░░░░░░░░` 🎉🎉 **Halfway through the entire guide!** You can now explain how real systems replicate and split their data. That's senior-engineer territory.

**Rules:** answer out loud or on paper **before** opening answers.

> 📖 *Pantry's data now lives on many machines, in many copies. We're halfway through the story, so this is a big checkpoint.*

---

## ⚡ Part 1: Recall (5 questions)

1. Do read replicas help with write-heavy load? Why or why not?
<details><summary>Answer</summary>

No. Every replica must apply every write. Replicas scale reads and availability, and sharding scales writes.
</details>

2. How do you guarantee users see their own writes when reading from replicas?
<details><summary>Answer</summary>

Read your own data from the leader (or pin to the leader briefly after writes), or track the write's LSN and read only from caught-up replicas.
</details>

3. What's the danger of last-write-wins conflict resolution?
<details><summary>Answer</summary>

It silently discards concurrent writes, and clock skew can make it discard the truly newer one.
</details>

4. Why map many logical shards onto fewer physical nodes?
<details><summary>Answer</summary>

Rebalancing becomes moving whole logical shards and updating a map, instead of rehashing all the keys.
</details>

5. What makes a good shard key?
<details><summary>Answer</summary>

High cardinality, even distribution of data and traffic, query locality (common queries hit one shard), and stability.
</details>

**Score: ___ / 5**

---

## 🔁 Halfway review (1 minute each, no peeking at lessons!)

Say one sentence on each. If one feels blurry, add it to your "reread" list:

- Latency vs throughput (004) · Availability nines (006) · TCP vs UDP (010)
- Stateless services (018) · Load balancing (019) · CDN (023) · Rate limiting (024)
- Cache-aside (028) · Cache invalidation (031) · ACID (035) · Indexes (037)
- Replication (046) · Sharding (049)

---

## 🧪 Part 2: Explain it aloud (Feynman)

5-minute timer. Explain to a 12-year-old:

> "When a website has more data than fits on one computer, and more visitors than one computer can handle, what does it do?"

Must include: **replicas (copies), shards (splits), shard key, a hotspot, and what happens when a machine dies**.

---

## 🛠️ Part 3: Mini-design

**A global multiplayer game** stores player profiles (100M players), match history (1B matches/year), and a friends list.

Decide the sharding and replication strategy for each data set, and name the likely hotspots.

<details><summary>One good answer</summary>

- **Profiles:** shard by `hash(player_id)` (logical shards → nodes), and a leader + 2 replicas per shard (semi-sync). Read-your-writes for the player's own profile.
- **Match history:** a wide-column store with partition key `(player_id, month)` and clustering by time. Each match is written under every participant (denormalized) for locality. Or store the match once by `match_id`, plus per-player index rows.
- **Friends:** shard by player_id, store edges in both directions (A→B stored with A, B→A with B).
- **Hotspots:** streamers or celebrities (profile views → cache), tournament matches (salting or a dedicated cache), season launches (write bursts → queues).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Explain the difference between replication and sharding, and why you usually need both."**
<details><summary>Model answer</summary>

Replication copies the same data to multiple nodes, for availability and read scaling. Sharding splits different data across nodes, for storage and write scaling. Production systems shard for capacity and replicate each shard for fault tolerance.
</details>

**Q2. "What happens during a leader failover, and what can go wrong?"**
<details><summary>Model answer</summary>

Detect failure (timeout) → elect or promote the most up-to-date replica → reconfigure clients and replicas. Risks: lost async writes, split brain (the old leader still accepting writes → needs fencing), false failovers from bad timeouts, and client errors during the switch.
</details>

**Q3. "How do you query 'all orders over $1,000 last week' in a database sharded by customer_id?"**
<details><summary>Model answer</summary>

Scatter-gather to all shards, then merge (acceptable for rare admin queries, but watch the tail latency and load), or better, answer it from a warehouse or OLAP store fed by CDC, or maintain a secondary index or table keyed by date for this report.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [051 · Consistent Hashing](051-consistent-hashing.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [046](046-leader-follower-replication.md), [049](049-sharding.md), [050](050-shard-keys-and-hotspots.md) |

🏆 **HALFWAY REWARD:** seriously, celebrate. Tell someone you're halfway through mastering system design. Take a full day off if you like. Spaced breaks improve retention.

---

⬅️ [050 · Shard Keys & Hotspots](050-shard-keys-and-hotspots.md) · 🗺️ [Phase map](README.md) · ➡️ [051 · Consistent Hashing](051-consistent-hashing.md)

✅ Tick **Checkpoint 50%** in [PROGRESS.md](../../PROGRESS.md). 🎉🎉
