# 011 · The KV Cache & PagedAttention

> ⏱ 13 min · 📈 22% · 🅰️ AI-era core · Phase 02: Model Serving & Inference Infrastructure
>
> `████░░░░░░░░░░░░░░░░` 22% of Book 2
>
> 🧬 **Atoms used:** memory & caching [B1·027] · eviction policies [B1·030] · [002] · [005] · [010]

---

## 📖 Story

Maya's continuous-batching server admits **40 conversations**, then refuses the 41st: `KV cache full`.

She checks the numbers from her napkin (lesson 005). The server has **440 GB** for the KV cache. An average Pantry conversation needs **0.66 GB**. That's room for **660**, not 40.

She dumps the allocator's state and finds the culprit. When a conversation starts, the server can't know how long it will run, so it **reserves the maximum**: room for **8,000 tokens**, or **2.6 GB**, for every conversation. The average conversation uses **2,000**. Three-quarters of every reservation is **empty**, and nobody else can use it.

It's the most expensive empty space Maya has ever seen: **330 GB of GPU memory**, reserved and unused.

I told Maya operating systems solved this exact problem fifty years ago, and the AI world solved it again in 2023 by borrowing the answer. **Don't reserve the whole hotel floor. Hand out rooms as guests arrive.** Let me show you.

## 🎯 One-sentence idea

**The KV cache stores every active token's keys and values in GPU memory, and PagedAttention manages it like virtual memory: in small fixed-size blocks allocated on demand and mapped through a block table, which removes nearly all fragmentation and lets conversations share identical prefixes.**

## 🧸 Analogy

A **hotel for conversations**:

- **Old way:** every guest is given an **entire floor** on arrival, "in case their family comes". Most guests use one room. The hotel is "full" while most rooms are empty.
- **Paged way:** guests get **one room at a time**, anywhere in the building, and the front desk keeps a **room list per guest** (the block table). When a guest checks out, their rooms go straight back.
- Guests from the **same tour group** share the **same breakfast room** (shared prefixes), until one of them wants something different (copy-on-write).

## 🖼️ Visual

*Diagram brief:* on the left, GPU memory as a row of contiguous reservations, each mostly hatched as wasted space. On the right, the same memory as a grid of small 16-token blocks of mixed colours, with a block table per conversation pointing to scattered blocks, and two conversations pointing at the same shared system-prompt blocks.

```
CONTIGUOUS (reserve max 8k tokens each)      PAGED (16-token blocks on demand)
┌──────────────────────────────┐             conv A table: [7][2][9]
│A■■■■■░░░░░░░░░░░░░░░░░░░░░░░░│             conv B table: [7][4]        ← shares block 7
│B■■░░░░░░░░░░░░░░░░░░░░░░░░░░░│             conv C table: [7][1][5][8]  (system prompt)
│C■■■■■■■■░░░░░░░░░░░░░░░░░░░░░│             memory: [1C][2A][3 ][4B][5C][6 ][7*][8C][9A]
└──────────────────────────────┘                     waste ≤ 1 partly-filled block per conv
 ░ = reserved, unused (60–80%)
```

## 🔬 How it works

- **What's in the KV cache:** for every token in every active conversation, each layer's **key and value vectors**, so decode never recomputes the past (lesson 002). It grows by **~0.1–0.3 MB per token** (lesson 005) and is **the** limit on concurrent conversations.
- **Contiguous allocation wastes memory:** reserving `max_tokens` up front causes **internal fragmentation** (unused reserved space) and **external fragmentation** (gaps too small for a new reservation). Studies measured **60–80% waste**.
- **PagedAttention:** split the KV cache into **blocks of ~16 tokens**. Each conversation gets a **block table** mapping its logical token positions to physical blocks, allocated **only when needed**. Waste drops to **under one block per conversation** (< 4%).
- **Sharing:** conversations with an identical prefix (the same system prompt, or several samples of one prompt) **point at the same blocks**, with **copy-on-write** when they diverge. This is the foundation of **prefix caching** (lesson 018).
- **When memory runs out:** the scheduler **preempts** low-priority sequences: **swap** their blocks to CPU memory, or **drop and recompute** them later. Other levers: **FP8 KV cache** (half the memory), and offloading idle conversations' KV to CPU or SSD.

## 🧩 Worked example

**Maya's server, before and after** (440 GB for KV, 0.33 MB per token):

```
Contiguous, reserve 8,000 tokens each:
  8,000 × 0.33 MB = 2.6 GB per conversation
  440 GB ÷ 2.6 GB ≈ 169 conversations max
  (and fragmentation in practice → ~40 before allocation failures)

Paged, 16-token blocks, average conversation 2,000 tokens:
  2,000 × 0.33 MB = 0.66 GB, plus < 1 block of slack
  440 GB ÷ 0.66 GB ≈ 666 conversations

Paged + FP8 KV cache (1 byte instead of 2):
  0.33 GB per conversation → ~1,330 conversations
```

**So this means:** the same server holds **16–33× more conversations** than Maya's first allocator, and continuous batching (lesson 010) can actually keep its slots full. The fleet from lesson 005 stays at **~84 servers** by memory, instead of the 1,400 the naive allocator would have demanded.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Paged KV cache | Near-zero fragmentation, prefix sharing | Indirection in the attention kernel, a more complex allocator |
| Small blocks | Less waste | Bigger block tables, more overhead |
| FP8 KV cache | 2× capacity | A small quality risk (eval it) |
| Swap to CPU on preemption | Preserves work | PCIe transfer time |
| Recompute on preemption | No CPU memory needed | Re-pays the prefill |

## 🌍 Real world

- **vLLM** introduced **PagedAttention** (2023), reporting 2–4× throughput gains from memory efficiency alone. Most serving engines now page the KV cache.
- **Grouped-query attention** in modern models cuts KV size by sharing keys and values across query heads: the model architecture and the allocator attack the same problem.
- Long-context products **offload KV** to CPU memory or SSD tiers, and reload it when the user returns.

## 📌 Cheat card

> - **KV cache = every active token's keys + values.** It's THE concurrency limit.
> - **Reserving max length wastes 60–80%.**
> - **PagedAttention: ~16-token blocks + block tables**, allocated on demand. Waste < 4%.
> - **Shared prefixes → shared blocks** (copy-on-write).
> - **Out of memory → preempt: swap or recompute.** FP8 KV doubles capacity.

## 🧪 Feynman check

Explain the hotel for conversations: why giving every guest a whole floor makes a "full" hotel mostly empty, and how a room list per guest fixes it.

⚠️ **Common confusion:** "The KV cache is a cache, so a miss just means a bit of extra work." It's not optional: it's the **working memory of every active conversation**. Without room for it, a conversation can't continue at all, which is why it, not compute, usually caps concurrency.

## ⚡ Quick recall

1. What does the KV cache store?
<details><summary>Reveal Answer</summary>

The key and value vectors, for every layer, of every token in every active sequence, so past tokens aren't recomputed at each decode step.
</details>

2. Why does reserving the maximum length per request waste memory?
<details><summary>Reveal Answer</summary>

Most conversations are far shorter than the maximum, so most reserved space sits unused, and gaps between reservations fragment the rest.
</details>

3. How does PagedAttention enable prefix sharing?
<details><summary>Reveal Answer</summary>

Block tables let multiple sequences point to the same physical blocks for an identical prefix, with copy-on-write when one of them diverges.
</details>

## 🎤 Interview practice

**Q. "Your inference server runs out of KV memory at peak, and requests start failing. What are your options, short term and long term?"**
<details><summary>Model answer</summary>

- **Diagnose:** check the allocator (contiguous vs paged), the average vs maximum context lengths, the batch size, and whether long-context requests are crowding out short ones.
- **Short term:**
  - **Admission control:** queue new requests rather than fail them, prioritizing interactive traffic.
  - **Preemption:** swap or recompute low-priority sequences.
  - **Cap `max_tokens`** and trim prompt context (history summarization, fewer retrieved chunks).
- **Medium term:**
  - A **paged KV cache** if it isn't already.
  - **FP8 KV cache** (eval quality first).
  - **Prefix caching** so shared system prompts use one copy.
- **Long term:**
  - More memory per replica (bigger GPUs, or tensor parallelism across more GPUs).
  - Route **long-context requests to a dedicated pool**, so they don't evict short chats.
  - KV offload to CPU/SSD for idle conversations.
- **Likely follow-up:** "How do you size the KV budget?" → (GPU memory − weights − activation overhead) ÷ (KV per token × expected tokens per sequence), validated by a load test at the TPOT SLO.
</details>

## 📖 Teaser

> 📖 *The hotel is finally full of real guests, and finance asks the obvious next question: what if every guest simply took up less space?*

---

⬅️ [✅ Checkpoint 20%](checkpoint-20.md) · 🗺️ [Phase map](README.md) · ➡️ [012 · Quantization & Model-Size Trade-offs](012-quantization.md)

✅ **Safe stopping point.** Tick lesson 011 in [PROGRESS.md](../../PROGRESS.md).
