# 048 · Design Embedding-Based Recommendations

> ⏱ 14 min · 📈 96% · 🅱️ Production & case studies · Phase 06: AI Case Studies & Capstone
>
> `███████████████████░` 96% of Book 2
>
> 🧬 **Atoms used:** caching [B1·027] · top-K [B1·099] · batch vs stream [B1·091] · [019] · [020] · [026] · [038]

---

## 📖 Story

Card four: **"Design the 'Recommended for you' carousel. 30 million users, 2 million dishes, under 100 ms."**

Maya remembers the first version Pantry shipped: it asked a large language model to **"recommend 10 dishes"** for each user, with their order history in the prompt. It took **4 seconds**, cost more than the average order's margin, recommended dishes **from closed kitchens**, and gave everyone in a city nearly the **same list**.

"LLMs are wonderful for **explaining** a recommendation," Maya tells the panel. "But choosing 10 dishes from 2 million in 100 milliseconds is a job for **embeddings, a fast index, and a ranking model**: the funnel the big recommenders have run for years." She draws a funnel, wide at the top and narrow at the bottom.

Let me show you the funnel.

## 🎯 One-sentence idea

**Large-scale recommendation is a multi-stage funnel: candidate generation with a two-tower embedding model and ANN search (millions → hundreds), a feature-rich ranking model (hundreds → dozens), and business re-ranking for availability, diversity, and freshness (dozens → 10), all within ~100 ms, trained on logged interactions and judged by online A/B tests.**

## 🧸 Analogy

A **personal shopper in a gigantic market**:

- First, they **walk only the aisles** that match your taste (candidate generation: the flavour map, lesson 019).
- Then they **look closely** at a few hundred dishes, checking price, distance, ratings, and what you ate yesterday (ranking).
- Finally, they make sure the basket is **open now, nearby, and not all curry** (re-ranking).
- They don't **interview every stall** in the market for every customer, which is what the LLM-for-everything version did.

## 🖼️ Visual

*Diagram brief:* a funnel. At the top, 2M dishes. The two-tower stage (user tower computed online, item tower precomputed) with ANN retrieval narrows to ~500 candidates. The ranking stage (features from the feature store + a ranking model) narrows to ~50. Re-ranking with business rules (open now, delivery radius, diversity) yields the final 10. Each stage carries a latency label summing to under 100 ms.

```
2,000,000 dishes
   │  🧭 Two-tower retrieval: user vector (online, 5 ms) · ANN over item vectors (10 ms)
   ▼  + filters: delivery city, open now
~500 candidates
   │  📊 Ranking model: ~60 features per (user, dish) from the feature store (10 ms fetch, 25 ms score)
   ▼
~50 ranked
   │  🧮 Re-rank: diversity (≤ 2 per cuisine), freshness boost, sponsored slots, dedupe (3 ms)
   ▼
10 shown  ── total ≈ 60 ms p50, < 100 ms p99 ✅
```

## 🔬 How it works

- **Requirements and napkin:** 30M users. Peak **50k recommendation requests/s** (home-page loads at dinner). p99 < 100 ms. Dishes change daily (menus, closures), new dishes appear constantly. Optimize **orders**, not clicks.
- **Two-tower candidate generation:** a **user tower** maps the user's features and recent activity to a vector. An **item tower** maps each dish's features (text, image, cuisine, price) to a vector in the **same space**, trained so that dishes users ordered score high. Item vectors are precomputed and indexed (ANN, lesson 020), and the user vector is computed **at request time**. Partition the index by city, so filtering is free.
- **Ranking:** a heavier model (gradient-boosted trees or a deep network) scores ~500 candidates with **rich features** from the **feature store** (lesson 026): distance, ETA, price vs habits, ratings, time of day, recent views (streaming features).
- **Re-ranking and rules:** hard filters (open now, delivers here, no allergens from the profile), **diversity** (cuisines, kitchens), freshness boosts for new dishes, and sponsored slots, all in deterministic code.
- **Learning loop:** log impressions, clicks, and orders with the **served features** (to avoid skew), correct for **position bias** (top slots get clicked anyway), retrain daily, refresh item vectors as menus change, and handle **cold start** with content-based item vectors (text and image embeddings) until interaction data arrives.
- **Where the LLM fits:** offline **enrichment** (tagging dishes, writing descriptions, generating item embeddings from text) and optional **explanations** ("Because you loved the ramen last week") generated for the final 10 only, cached per (user, dish).

## 🧩 Worked example

**Capacity at 50k requests/s:**

```
User tower: small MLP, ~0.5 ms CPU → 50k/s ≈ 25 CPU cores (+ headroom)
ANN: per-city partitions, top 500, ~5–10 ms → ~200 QPS per core → ~250 cores
Features: 1 user + 500 candidates × 60 features, batched multi-get → ~10 ms (Redis cluster)
Ranking: 500 candidates per request = 25M scores/s → GPU/CPU inference fleet sized by benchmark
Cache: home carousel per user for 10 minutes → ~60% hit rate at dinner → halve all the above
```

**LLM-for-everything vs the funnel:**

| | LLM picks 10 dishes | Two-tower + rank + re-rank |
|---|---|---|
| p50 latency | 4 s | **60 ms** |
| Cost per 1,000 requests | ~$6 | ~$0.03 |
| Closed-kitchen dishes shown | 7% | **0%** (hard filter) |
| Orders from carousel (A/B) | baseline | **+9.4%** |

## ⚖️ Trade-offs

| Decision | Choice | Trade-off |
|---|---|---|
| Candidate generation | Two-tower + ANN | Fast, scalable vs limited user-item interaction modelling |
| Ranking model | Rich features, heavier model | Accuracy vs latency budget per candidate |
| Objective | Orders (not clicks) | Business value vs sparser signal |
| Caching | 10-minute per-user cache | Lower load vs slightly stale suggestions |
| LLM use | Offline enrichment + explanations | Quality text vs cost (kept off the hot path) |

## 🌍 Real world

- **YouTube's** deep-learning recommendation paper (2016) popularized **candidate generation + ranking**. Two-tower retrieval models are standard at large platforms.
- Recommenders correct for **position bias** and train on logged impressions, with online **A/B tests** as the final judge (lesson 038).
- Teams increasingly use LLMs for **item understanding** (tags, descriptions, embeddings) and **explanations**, while classic ranking stays in the latency-critical path.

## 📌 Cheat card

> - **Funnel: millions → ~500 (two-tower + ANN) → ~50 (ranking) → 10 (rules).**
> - **Item vectors precomputed, user vector online. Partition the index by city.**
> - **Features from the feature store. Log served features.**
> - **Hard filters and diversity in code.** Optimize orders, correct position bias.
> - **LLMs: offline enrichment + cached explanations**, not the hot path.

## 🧪 Feynman check

Explain the personal shopper in the gigantic market: why they walk only some aisles, why they look closely at a few hundred dishes, and why they don't interview every stall.

⚠️ **Common confusion:** "LLMs make recommendation systems obsolete." Choosing among millions of items in milliseconds needs **embeddings, indexes, and trained rankers**. LLMs add value around the edges: understanding items, cold start, and explanations.

## ⚡ Quick recall

1. What are the stages of a recommendation funnel?
<details><summary>Reveal Answer</summary>

Candidate generation (e.g., two-tower + ANN), ranking with rich features, and re-ranking with business rules (filters, diversity, freshness).
</details>

2. In a two-tower model, which vectors are precomputed?
<details><summary>Reveal Answer</summary>

Item vectors are precomputed and indexed. The user vector is computed at request time from current features.
</details>

3. How do you recommend a brand-new dish with no interactions?
<details><summary>Reveal Answer</summary>

Use content-based item embeddings (text, image, attributes) so it lands near similar dishes, plus freshness boosts, until interaction data accumulates.
</details>

## 🎤 Interview practice

**Q. "Design the recommendation system, then explain how you'd stop it from showing everyone the same popular dishes."**
<details><summary>Model answer</summary>

- **Design:** two-tower retrieval with per-city ANN partitions (top ~500), a ranking model over feature-store features (orders as the objective), deterministic re-ranking (availability, delivery radius, allergens, diversity), a per-user cache, and logging of served features and impressions. Daily retraining, streaming features for session context.
- **Popularity collapse fixes:**
  - **Debias training:** position-bias correction, and down-weighting of exposure-driven clicks.
  - **Exploration:** a small share of slots for exploration (e.g., bandits / Thompson sampling) over new and long-tail dishes, measured by long-term metrics.
  - **Diversity constraints** in re-ranking (cuisines, kitchens, price levels), and per-user novelty (don't repeat yesterday's carousel).
  - **Cold-start boosts** for new kitchens with content embeddings.
- **Measure:** an A/B test on orders **and** catalogue coverage, new-kitchen exposure, and 30-day retention.
- **Likely follow-up:** "How would you use an LLM here?" → offline tagging and descriptions that improve item embeddings, plus cached explanations for the final 10. Never per-request selection over millions of items.
</details>

## 📖 Teaser

> 📖 *The final card is the one the voice team has been dreading: "Customers call Pantry and talk to the chef. It must answer in under a second, and let them interrupt."*

---

⬅️ [047 · Design an AI Coding Agent](047-design-coding-agent.md) · 🗺️ [Phase map](README.md) · ➡️ [049 · Design a Real-Time Voice Assistant](049-design-voice-assistant.md)

✅ **Safe stopping point.** Tick lesson 048 in [PROGRESS.md](../../PROGRESS.md).
