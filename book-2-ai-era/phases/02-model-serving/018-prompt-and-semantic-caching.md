# 018 · Prompt Caching & Semantic Caching

> ⏱ 13 min · 📈 36% · 🅰️ AI-era core · Phase 02: Model Serving & Inference Infrastructure
>
> `███████░░░░░░░░░░░░░` 36% of Book 2
>
> 🧬 **Atoms used:** caching basics [B1·027] · cache invalidation [B1·031] · consistent hashing [B1·051] · [005] · [011] · [015]

---

## 📖 Story

Lesson 005 left a red circle on Maya's napkin. Every message starts with the **same 800-token system prompt**: the chef's persona, the allergy rules, the house style. And every message **prefills it from scratch**: **2,300 times a second**, the GPUs re-read the exact same words and compute the exact same keys and values.

That's **more than half** of all prefill compute, spent recomputing something that never changes.

Maya's first instinct is a Book 1 classic: **cache the answers**. She adds a cache keyed on the question text. Hit rate: **0.4%**. Nobody types the same sentence twice.

So she tries a fancier version: cache by **meaning**. "Is the satay nut-free?" and "does satay have nuts?" should share an answer. The hit rate jumps to **19%**. Then a customer asks, "Is the satay **sauce for kids** nut-free?", and the cache confidently serves the answer for **adults' satay**, which has a different recipe.

I told Maya that in the AI era there are **three** caches, not one, and they differ in how much they can ever get wrong. Let me show you all three, from the one that's always safe to the one that needs a seatbelt.

## 🎯 One-sentence idea

**AI systems use three caches: a prefix (KV) cache that reuses the computed keys and values of identical prompt beginnings, which is always correct and cuts TTFT and cost, an exact response cache for repeated identical requests, and a semantic cache that reuses answers for similar questions, which saves the most but must be tightly scoped because "similar" isn't "same".**

## 🧸 Analogy

A **kitchen's prep work**:

- **Prefix cache = mise en place.** The stock, chopped onions, and sauces that start **every** dish are prepared **once** and reused. The finished dish is still cooked fresh for each order. Always safe.
- **Exact cache = a dish under the heat lamp** for an identical repeat order. Safe if nothing changed.
- **Semantic cache = serving yesterday's dish** to someone whose order **sounds similar**. Fast and cheap, but "no nuts, for kids" and "no nuts" are **not** the same order.

## 🖼️ Visual

*Diagram brief:* a prompt drawn as a long bar: a static system prompt block, then tool definitions, then retrieved facts, then the user's question. The first two segments are highlighted as "cached prefix, reused". Below, the request path checks the exact cache, then the semantic cache (with scope and threshold), then routes to the GPU replica that already holds the prefix.

```
PROMPT LAYOUT (static first, dynamic last)
[ system prompt 800 ][ tool defs 400 ][ retrieved facts ][ history ][ question ]
 └──── identical for everyone → prefix KV cache ───┘ └──── different every time ────┘

Request path:
 exact cache (hash of full request) → semantic cache (scoped, threshold) →
 router with prefix affinity → GPU replica holding the prefix's KV blocks
```

## 🔬 How it works

- **Prefix (KV) caching:** if a new prompt **starts with exactly the same tokens** as an earlier one, reuse its KV blocks (lesson 011) and prefill only the new suffix. Output is **identical** to computing from scratch. Providers often **discount cached input tokens** heavily.
- **Design prompts for it:** put **static content first** (system prompt, tool definitions, few-shot examples, long documents being discussed) and **dynamic content last** (the user's question, timestamps). One changed token early in the prompt invalidates everything after it.
- **Route for cache hits:** with many replicas, a prefix is only cached where it was computed. Use **prefix-aware routing**: consistent hashing on the prefix hash (Book 1 lesson 051), with load-aware spillover.
- **Exact response cache:** key on a hash of model + prompt version + full input + parameters. It's useful for repeated system-generated requests (summaries, classifications). Invalidate when the underlying data or prompt changes.
- **Semantic cache:** embed the request, find a cached request **above a similarity threshold**, and return its answer. Use it only for **non-personalized, low-risk, high-repeat** intents (FAQs). Scope keys by **tenant, locale, menu version, and intent**, and set a strict threshold. Never use it for anything that depends on user state or safety-critical facts.

## 🧩 Worked example

**Prefix caching on "Ask the Chef"** (1,500-token prompts, the first 1,200 tokens shared: system prompt + tool definitions + house examples):

```
Prefill tokens per message: 1,500 → 300 (80% saved, on cache hits)
Hit rate with prefix-aware routing: 95%
Prefill compute: −76% → lesson 005's prefill fleet: ~120 → ~30 servers
TTFT p50: 400 ms → 150 ms
Provider-hosted traffic: cached input billed at a deep discount → input bill −60%+
```

**Semantic cache, made safe:**

| Rule | Why |
|---|---|
| Only intents `faq.hours`, `faq.delivery_area`, `faq.general_cooking` | Never allergens, orders, or prices |
| Key = (intent, locale, menu_version, region) + embedding | "Kids' satay" can't match "satay" across menus |
| Cosine similarity ≥ 0.95, plus an entity check (dish names must match exactly) | Similar wording, different dish → miss |
| TTL 24 h, flushed on menu_version change | No stale facts |

**Result:** semantic hit rate **11%** of FAQ traffic (down from the unsafe 19%), with **zero** wrong-dish answers in a 50,000-request audit.

## ⚖️ Trade-offs

| Cache | You gain | You pay |
|---|---|---|
| Prefix (KV) | Always correct, big TTFT and cost cuts | GPU memory, prefix-aware routing |
| Exact response | Zero cost on repeats | Low hit rate for human-typed input |
| Semantic | Highest savings on repetitive questions | Wrong answers when "similar" ≠ "same" |
| Prefix-aware routing | High hit rates | Load imbalance on hot prefixes |

## 🌍 Real world

- Major model APIs offer **prompt caching** with discounted cached-input pricing, and guidance to put static content first.
- **vLLM** (automatic prefix caching) and **SGLang** (RadixAttention) reuse KV blocks across requests, and routers add prefix-aware scheduling.
- Semantic caches are common for FAQ-style assistants, usually backed by a vector index with strict thresholds and scoping.

## 📌 Cheat card

> - **Prefix cache: identical start → reuse KV.** Always correct.
> - **Static first, dynamic last.** One early change breaks the prefix.
> - **Route by prefix hash** for hits.
> - **Exact cache** for repeated machine requests.
> - **Semantic cache: scoped, strict threshold, low-risk intents only.**

## 🧪 Feynman check

Explain the kitchen's prep work: why mise en place is always safe to reuse, and why serving a "similar" order from yesterday can go badly wrong.

⚠️ **Common confusion:** "Prompt caching caches the model's answer." It caches the **computed state of the prompt's beginning** (KV), not the output. Every answer is still freshly generated, which is why prefix caching never changes what the model says.

## ⚡ Quick recall

1. Why should static prompt content come first?
<details><summary>Reveal Answer</summary>

Prefix caching only matches an identical beginning: any change early in the prompt invalidates everything after it.
</details>

2. What does prefix-aware routing do?
<details><summary>Reveal Answer</summary>

It sends requests with the same prefix to the replica that already holds its KV blocks (e.g., via consistent hashing), increasing cache hits.
</details>

3. Where is a semantic cache safe, and where is it not?
<details><summary>Reveal Answer</summary>

Safe for non-personalized, low-risk, repetitive questions, tightly scoped. Not safe for anything depending on user state, orders, prices, or safety-critical facts.
</details>

## 🎤 Interview practice

**Q. "Your LLM bill is dominated by input tokens: a 3,000-token system prompt with tool definitions on every call. How do you cut cost and latency?"**
<details><summary>Model answer</summary>

- **Restructure the prompt:** static system prompt + tool definitions + examples first, dynamic content last. Freeze and version the static block so it changes rarely.
- **Prefix caching:**
  - Hosted APIs: enable prompt caching, so cached input is billed at a discount and TTFT drops.
  - Self-hosted: automatic prefix caching (paged KV), plus **prefix-aware routing** via consistent hashing on the prefix hash, with load-based spillover.
- **Shrink the static block:** trim tool descriptions, load tools **dynamically** per intent (only relevant tools), and move rarely used rules to retrieval.
- **Exact cache** for repeated machine-generated calls.
- **Semantic cache** only for FAQ intents, scoped and thresholded.
- **Measure:** cache hit rate, cached vs uncached input tokens, TTFT p50/p95, and $ per request, before and after.
- **Likely follow-up:** "What breaks prefix caching?" → per-request content early in the prompt (timestamps, user names), A/B tests that vary the system prompt, and routing that scatters identical prefixes across replicas.
</details>

## 📖 Teaser

> 📖 *The serving kitchen is lean and fast, and it still serves a chef who has never read a single one of Pantry's two million recipes: ask about a cook's signature dish, and it invents one.*

---

⬅️ [017 · Speculative Decoding & Latency Tricks](017-speculative-decoding.md) · 🗺️ [Phase map](README.md) · ➡️ [019 · Embeddings](../03-retrieval-data/019-embeddings.md)

✅ **Safe stopping point.** Tick lesson 018 in [PROGRESS.md](../../PROGRESS.md). 🎉 **Phase 02 complete!** Skim the [Phase 02 cheatsheet](CHEATSHEET.md) and try the [interview bank](INTERVIEW-QUESTIONS.md).
