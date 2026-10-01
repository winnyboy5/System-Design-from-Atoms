# ✅ Checkpoint 30%: Serving at Scale

> ⏱ 20 min · Covers lessons **011–015** · 📈 You're at **30%**
>
> `██████░░░░░░░░░░░░░░` 🎉 Nearly a third! You can now design the serving layer of an AI product.

**Rules:** answer each question **out loud or on paper before** opening the answer. No peeking.

> 📖 *Maya's Friday outage became a 40-second blip. Let's see whether your gateway would have held too.*

---

## ⚡ Part 1: Recall (5 questions)

1. Why does reserving `max_tokens` of KV memory per request waste so much?
<details><summary>Reveal Answer</summary>

Most sequences are far shorter than the maximum, so most reserved space is empty (internal fragmentation), and gaps fragment the rest. Typical waste: 60–80%.
</details>

2. What gets quantized, and what degrades first?
<details><summary>Reveal Answer</summary>

Weights, activations, and/or the KV cache. Numbers, arithmetic, code, and long reasoning degrade first, so evaluate per slice.
</details>

3. Which parallelism belongs inside a server, and why?
<details><summary>Reveal Answer</summary>

Tensor parallelism, because it all-reduces after every layer and needs NVLink-class bandwidth.
</details>

4. How do you stop a dropped stream from being regenerated?
<details><summary>Reveal Answer</summary>

Decouple generation into a per-message buffer, resume with Last-Event-ID, and create messages with idempotency keys.
</details>

5. Name four jobs of a model gateway.
<details><summary>Reveal Answer</summary>

Any four of: auth and key custody, token-based quotas, routing to the right model, retries/timeouts/circuit breakers/fallbacks, caching, and metering cost per team and feature.
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

Set a 3-minute timer. Explain to an imaginary 12-year-old:

> "How does one company serve an AI model to millions of people at once without buying millions of computers?"

Aim to naturally use: **the hotel with rooms on demand (paged KV)**, **the lower-resolution photocopy (quantization)**, **the banquet with chefs sharing a dish (parallelism)**, and **the head waiter (gateway)**.

---

## 🛠️ Part 3: Mini-design

**A translation service** for 30,000 cook-written menus a day, average 2,000 tokens in and 2,000 tokens out. No latency SLO beyond "same day". Two providers are available, plus a self-hosted 8B model that scored 2 points lower on your translation golden set.

On paper:
1. Where does it run, and how is it scheduled?
2. What's the fallback chain?
3. One sentence: "So this means the design needs ___."

<details><summary>One good answer</summary>

- **Volume:** 30k × 4,000 = 120M tokens/day: small. Run it as a **queue-driven batch job** on the self-hosted 8B model in **off-peak hours**, with its own token quota so it never touches dinner-rush capacity.
- **Quality:** 2 points lower on average, so check slices (allergen terms, quantities). If a slice regresses, route that slice (or low-confidence items) to a provider's discounted batch API.
- **Fallback chain:** self-hosted → provider A batch → provider B batch, with idempotency per `(menu_id, version, locale)`.
- **So this means:** an idempotent, prioritized batch pipeline behind the gateway, with per-slice evals deciding the routing.
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "Your 70B model needs to serve 100k-token contexts. What changes in the serving design?"**
<details><summary>Model answer</summary>

KV per sequence becomes ~16–33 GB. Use a paged KV cache with FP8 KV, a dedicated long-context pool (so long requests don't evict short chats), higher TP or PP for memory, chunked prefill to protect TPOT, prefix caching for repeated documents, and KV offload for idle sessions.
</details>

**Q2. "How do you make provider fallback safe for quality?"**
<details><summary>Model answer</summary>

Each fallback model gets its own tested prompt variant, is part of the eval suite, and has validators for structured outputs. Breakers trip on TTFT and error rates. Fallbacks are exercised regularly (chaos tests) so they aren't stale when needed.
</details>

**Q3. "FP8 or INT4?"**
<details><summary>Model answer</summary>

FP8 is usually near-lossless on large models and gives ~2× memory and speed. INT4 gives ~4× weight memory but degrades numbers, code, and reasoning more. Decide per workload with sliced evals and $/1M tokens at the SLO.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 Move on to [016 · GPU Autoscaling & Cold Starts](016-gpu-autoscaling.md) |
| 4/5 | ✅ Move on, and reread the 📌 cheat card for the one you missed |
| ≤ 3/5 | 🔁 Revisit [011](011-kv-cache-and-pagedattention.md) and [015](015-model-gateway.md), then retry tomorrow. |

---

⬅️ [015 · The Model Gateway](015-model-gateway.md) · 🗺️ [Phase map](README.md) · ➡️ [016 · GPU Autoscaling & Cold Starts](016-gpu-autoscaling.md)

✅ Tick **Checkpoint 30%** in [PROGRESS.md](../../PROGRESS.md). 🎉
