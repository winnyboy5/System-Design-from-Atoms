# 009 · GPUs & Accelerators for Designers

> ⏱ 12 min · 📈 18% · 🅰️ AI-era core · Phase 02: Model Serving & Inference Infrastructure
>
> `███░░░░░░░░░░░░░░░░░` 18% of Book 2
>
> 🧬 **Atoms used:** latency vs throughput vs bandwidth [B1·004] · vertical vs horizontal scaling [B1·017] · [002] · [005]

---

## 📖 Story

Finance approved self-hosting, with one condition: **"Use the cheap GPUs."**

Maya finds them: a rack of small inference cards at **$0.70 an hour**, versus **$2.50** for the big ones. She deploys Pantry's 8-billion-parameter "quick answers" model onto them and watches the bill.

The bill goes **up**.

Each cheap card generates about **270 tokens a second**. The expensive card next to it, running the same model, generates **9,000**. Per million tokens, the "cheap" GPU costs **ten times more**.

Meanwhile, on the expensive cards, the monitoring panel says **"GPU utilization: 98%"**, and yet half the requests are waiting. On another node, it says **31%**, and that node is doing more useful work.

I told Maya that a GPU isn't one number. It's **four**: how much it remembers, how fast it reads, how fast it calculates, and how fast it talks to its neighbours. Pick the wrong one to optimize and you pay for it by the token. Let me show you the four numbers.

## 🎯 One-sentence idea

**For system design, an accelerator is four numbers (memory capacity, memory bandwidth, compute FLOPS, and interconnect speed), and the right hardware is the one with the lowest cost per useful token for your workload's bottleneck, not the lowest price per hour or the highest "utilization" reading.**

## 🧸 Analogy

**Kitchens for hire**:

- **Pantry size** = memory capacity: how many cookbooks and order tickets fit.
- **Corridor width** to the pantry = memory bandwidth: how fast ingredients reach the stove. (Decode lives here.)
- **Number of burners** = compute: how many things cook at once. (Prefill lives here.)
- **The hatch between kitchens** = interconnect: how fast two kitchens can share a dish being cooked together.

A cheap kitchen with a narrow corridor serves fewer plates per hour. **Price per plate**, not rent, is what matters.

## 🖼️ Visual

*Diagram brief:* one server drawn as eight GPU boxes, each with its HBM memory stacked beside it. Thick NVLink bars connect the GPUs inside the server. Thin network links leave the server to other servers. Each link is labelled with its speed, so the 10–20× drop from inside to outside the box is obvious.

```
┌──────────────── one 8-GPU server ───────────────────────────────┐
│  [GPU+80 GB HBM] ═ [GPU+80 GB] ═ [GPU] ═ [GPU] …   (8 GPUs)    │
│      ║ HBM: ~3.35 TB/s per GPU (the corridor)                   │
│      ═ NVLink: ~900 GB/s GPU↔GPU (the hatch, inside the box)    │
└───────────────────────────│─────────────────────────────────────┘
                            │ network: ~50 GB/s (400 Gb/s) per link
                            ▼  ← 18× slower than NVLink
                     other servers
```

## 🔬 How it works

- **Memory capacity** sets **what fits**: model weights + KV cache (lesson 005). It decides how many GPUs one replica needs and how many conversations it holds.
- **Memory bandwidth** sets **decode speed**: TPOT ≈ bytes read per step ÷ bandwidth. Bandwidth per dollar is usually the best predictor of **cost per output token**.
- **Compute (FLOPS)** sets **prefill speed** and big-batch throughput. Training and long prompts are compute-hungry. Single-stream decode barely touches it.
- **Interconnect** sets how well GPUs **cooperate**: NVLink inside a server (~900 GB/s) vs the network between servers (~50 GB/s per link). Split one model across GPUs **inside** a server first (lesson 013).
- **"GPU utilization %" lies.** It only says *some* kernel ran in each sample period. Measure **tokens/s per GPU**, **cost per 1M tokens**, **memory-bandwidth utilization (MBU)** for decode, and **model FLOPs utilization (MFU)** for prefill and training.
- **Match hardware to the job:** large models → high-memory, high-bandwidth GPUs. Embeddings, rerankers, and small models → cheaper inference cards or even CPUs. Batch jobs → spot or discounted capacity. Other accelerators (TPUs, custom inference chips) follow the same four numbers.

## 🧩 Worked example

**Pantry's 8B model (16 GB in FP16), two options** (illustrative prices):

```
Big GPU: 80 GB, 3.35 TB/s, $2.50/h
  Batch 64 conversations (~1k tokens each, 0.13 GB KV each)
  Bytes per step = 16 GB + 64 × 0.13 GB ≈ 24 GB → 24 / 3,350 ≈ 7 ms
  Throughput = 64 tokens / 7 ms ≈ 9,100 tokens/s ≈ 33M tokens/h
  Cost ≈ $2.50 / 33 ≈ $0.08 per 1M tokens ✅

Cheap GPU: 24 GB, 0.3 TB/s, $0.70/h
  Room for the weights + ~6 GB of KV → batch 16 at most
  Bytes per step = 16 + 16 × 0.13 ≈ 18 GB → 18 / 300 ≈ 60 ms
  Throughput = 16 / 0.06 ≈ 270 tokens/s ≈ 1M tokens/h
  Cost ≈ $0.70 / 1 ≈ $0.73 per 1M tokens ❌ (9× worse)
```

**Where the cheap card wins:** Pantry's **embedding model** (~0.3B parameters) is compute-light and memory-light. On the cheap card it embeds 2,000 queries/s for $0.70/h, while the big GPU would sit at 4% busy.

**So this means:** Maya moves chat to big GPUs and embeddings and reranking to cheap ones. **Cost per 1M chat tokens: $0.73 → $0.08**, and she replaces the utilization panel with **tokens/s/GPU and $/1M tokens**.

## ⚖️ Trade-offs

| Choice | You gain | You pay |
|---|---|---|
| Top-end GPUs | Most memory and bandwidth, best $/token for big models | High hourly price, scarce supply |
| Inference-class cards | Cheap per hour, great for small models | Poor $/token for big models |
| CPUs | Everywhere, cheap, elastic | Far too slow for large models |
| Reserved capacity | Guaranteed supply, discounts | Paying for idle hours |
| Spot capacity | 60–80% cheaper | Preempted at any time, batch only |

## 🌍 Real world

- GPU generations improve **memory size and bandwidth** as much as FLOPS (80 → 141 → 192+ GB of HBM), because inference is memory-bound.
- Cloud providers offer **TPUs** and custom **inference chips**: the same four-number analysis applies.
- Serving teams publish **$ per 1M tokens** and **tokens/s/GPU** as their headline efficiency metrics.

## 📌 Cheat card

> - **Four numbers: capacity, bandwidth, FLOPS, interconnect.**
> - **Decode → bandwidth. Prefill → FLOPS. Fit → capacity.** Split → NVLink first.
> - **Compare $ per 1M tokens**, never $ per hour.
> - **"GPU util %" lies.** Use tokens/s/GPU, MBU, MFU.
> - **Big models on big GPUs. Small models and embeddings on cheap cards.**

## 🧪 Feynman check

Explain the kitchens for hire: why a cheap kitchen with a narrow corridor can cost more per plate, and why the hatch between kitchens matters when two of them cook one dish.

⚠️ **Common confusion:** "Our GPUs are at 98% utilization, so we need more GPUs." That metric only says a kernel was running, not that the GPU did useful work efficiently. A GPU at "98%" with batch size 1 can be wasting **90%** of its bandwidth. Check tokens/s and batch size first.

## ⚡ Quick recall

1. Which GPU number most directly sets decode speed?
<details><summary>Reveal Answer</summary>

Memory bandwidth, because each decode step reads all weights and the batch's KV cache.
</details>

2. Why compare $ per 1M tokens instead of $ per hour?
<details><summary>Reveal Answer</summary>

A cheaper-per-hour GPU can produce far fewer tokens per hour, making it more expensive per token, which is what you actually pay for.
</details>

3. What's wrong with the classic "GPU utilization %" metric?
<details><summary>Reveal Answer</summary>

It only reports whether a kernel was running during a sample period, not how much of the GPU's bandwidth or compute was used, so a nearly idle GPU can read close to 100%.
</details>

## 🎤 Interview practice

**Q. "You have a fixed budget and three workloads: a 70B chat model, a 0.3B embedding model, and a nightly batch summarization job. How do you choose hardware for each?"**
<details><summary>Model answer</summary>

- **70B chat:** latency-critical and memory-bound. Needs **high-memory, high-bandwidth GPUs** in 8-GPU servers with NVLink (weights 70–140 GB, plus KV). Reserved capacity for the baseline, plus on-demand for peaks.
- **Embedding model:** small and compute-light. **Inference-class GPUs or CPUs** with large batches, where it hits high throughput cheaply. Scale on queue depth.
- **Nightly batch:** no latency SLO. **Spot/preemptible** GPUs (or a provider's batch API) with checkpointed, idempotent work items, run off-peak when reserved chat GPUs are idle (time-sharing the same fleet).
- **Decide with a benchmark:** for each workload, measure tokens/s (or items/s) per accelerator at the SLO, then compute **$ per 1M tokens or per item**. Pick the lowest, subject to supply.
- **Likely follow-up:** "GPU supply is constrained, so what then?" → multi-region and multi-provider capacity, quantization to fit more per GPU, routing easy queries to smaller models, and shifting batch work to off-peak hours.
</details>

## 📖 Teaser

> 📖 *Chat now runs on the right GPUs, yet Maya's tokens-per-second panel shows the expensive cards sitting half-idle, waiting for one customer's very long recipe to finish.*

---

⬅️ [008 · Requirements for AI Features](../01-ai-foundations/008-ai-requirements.md) · 🗺️ [Phase map](README.md) · ➡️ [010 · Static vs Continuous Batching](010-continuous-batching.md)

✅ **Safe stopping point.** Tick lesson 009 in [PROGRESS.md](../../PROGRESS.md).
