# ✅ Checkpoint 40%: Fast Kitchens, Smart Maps

> ⏱ 20 min · Covers lessons **016–020** · 📈 You're at **40%**
>
> `████████░░░░░░░░░░░░` 🎉 Two-fifths done! You can scale serving and search by meaning.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *Maya's serving kitchen is lean and her flavour map is fast. Before the chef starts reading Pantry's recipes, let's check your map-reading.*

---

## ⚡ Part 1: Recall (5 questions)

1. Why can't you autoscale your way out of a sudden GPU spike?
<details><summary>Reveal Answer</summary>

GPU cold starts take minutes (provision, image, weights, load), longer than many spikes, so you need headroom, prediction, warm pools, and degradation.
</details>

2. Why is speculative decoding lossless?
<details><summary>Reveal Answer</summary>

The large model verifies the drafted tokens with a rejection-sampling rule, so the output distribution is identical to the large model's alone.
</details>

3. Which prompt layout maximizes prefix-cache hits?
<details><summary>Reveal Answer</summary>

Static content first (system prompt, tool definitions, examples), dynamic content last (the question, timestamps).
</details>

4. What do embeddings capture well, and what do they miss?
<details><summary>Reveal Answer</summary>

They capture topical similarity and paraphrase. They miss negation, exact identifiers, and factual truth.
</details>

5. What's the risk of post-filtering ANN results?
<details><summary>Reveal Answer</summary>

Selective filters can remove all of the top-k, returning nothing even though matching items exist further away.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 3-minute timer. Explain to an imaginary 12-year-old:

> "How can a computer find 'cosy food for a rainy night' among fifty million dishes in a few milliseconds?"

Aim to naturally use: **the flavour map (embeddings)**, **closeness (cosine)**, **the librarian following "see also" signs (HNSW)**, and **telling her the filters first**.

---

## 🛠️ Part 3: Mini-design

**"Similar dishes" carousel.** 5M dishes, 768-dim embeddings, 4,000 QPS at peak, p99 < 30 ms, always filtered by the customer's delivery city (~400 cities, very uneven sizes).

On paper:
1. Memory for float16 vectors and an HNSW index.
2. Index layout given the city filter.
3. One sentence: "So this means the design needs ___."

<details><summary>One good answer</summary>

- **Memory:** 5M × 768 × 2 B ≈ **7.7 GB** + HNSW links (~0.6 GB) ≈ **8.3 GB**: fits one node's RAM. Replicate ×3–4 for 4k QPS and availability.
- **Layout:** **partition by city** (one small index per city, or filter-aware search with city as a partition key). Tiny cities can be brute-forced (a few thousand vectors = microseconds).
- **Caching:** "similar to dish X in city Y" is highly cacheable, so precompute nightly for popular dishes and serve from a KV store.
- **So this means:** per-city partitions with replicas, plus a precomputed cache for hot dishes. Measure recall@10 against brute force per partition.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Your semantic cache served a wrong answer. What went wrong, and how do you redesign it?"**
<details><summary>Model answer</summary>

"Similar" questions with different meanings (different dish, user, menu version) matched. Scope keys by intent, tenant, locale, and data version, raise the threshold, add entity checks, restrict to low-risk non-personalized intents, and flush on data changes.
</details>

**Q2. "HNSW or IVF-PQ for 2 billion vectors?"**
<details><summary>Model answer</summary>

All-RAM HNSW at 2B vectors costs terabytes of memory. IVF-PQ (or disk-based graph indexes) shrinks it 16–64× with re-scoring for recall. Choose by the memory budget, the recall target, and the update rate, and measure recall@k.
</details>

**Q3. "Why does the GPU autoscaler need a different signal than the web tier?"**
<details><summary>Model answer</summary>

GPU servers barely use CPU, and "GPU util %" doesn't reflect saturation. Queue wait, pending tokens, and KV-cache utilization show when sequences can't be admitted.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [021 · RAG Architecture](021-rag-architecture.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [018](../02-model-serving/018-prompt-and-semantic-caching.md) and [020](020-vector-databases-ann.md), then retry tomorrow. |

---

⬅️ [020 · Vector Databases & ANN Search](020-vector-databases-ann.md) · 🗺️ [Phase map](README.md) · ➡️ [021 · RAG Architecture](021-rag-architecture.md)

✅ Tick **Checkpoint 40%** in [PROGRESS.md](../../PROGRESS.md). 🎉
