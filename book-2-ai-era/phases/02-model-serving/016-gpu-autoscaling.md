# 016 · GPU Autoscaling & Cold Starts

> ⏱ 12 min · 📈 32% · 🅰️ AI-era core · Phase 02: Model Serving & Inference Infrastructure
>
> `██████░░░░░░░░░░░░░░` 32% of Book 2
>
> 🧬 **Atoms used:** autoscaling & containers [B1·025] · object storage [B1·042] · backpressure & load shedding [B1·061] · [004] · [005] · [009]

---

## 📖 Story

Friday, 18:30. Traffic starts its climb toward the dinner peak. Maya's autoscaler, copied from the web tier, watches **CPU utilization**. GPU servers barely use their CPUs, so it sees **12%** and does **nothing**.

At 18:52, queues explode, and a hand-written rule finally fires: "Add 20 servers."

The first new server reports ready at **19:03**: **eleven minutes** later. Maya reads the timeline:

- **4 minutes** to get the machine,
- **2 minutes** to pull a **30 GB** container image,
- **4 minutes** to download **140 GB** of weights from object storage at 600 MB/s,
- **1 minute** to load them into GPU memory and warm up.

By 19:03, the rush has peaked. TTFT p99 is **14 seconds**, and **9%** of customers have given up.

I told Maya that a web server is a **food truck** you can call in ten seconds. A GPU server is a **restaurant** you have to staff, stock, and preheat. You can't scale it when you're hungry. You have to scale it **before**. Let me show you how.

## 🎯 One-sentence idea

**GPU autoscaling must scale on the signals that actually saturate (queue depth, pending tokens, KV-cache utilization), predict demand ahead of time because cold starts take minutes, and attack every slice of the cold start: images, weight loading, and warm pools.**

## 🧸 Analogy

**Opening extra restaurant kitchens for a festival**:

- You don't wait until diners are queueing around the block. You check **last year's festival** and open kitchens at 17:30 (predictive scaling).
- You watch the **queue of orders**, not how busy the cashier looks (the right signal).
- **Pre-stocked kitchens** open faster: ingredients already on the shelves (weights on local disk), the oven left warm (warm pools).
- And when you truly can't open more, you serve a **shorter menu** to keep the line moving (degradation).

## 🖼️ Visual

*Diagram brief:* a timeline bar of an 11-minute cold start split into four coloured segments (provision, image, weights, load), and below it the optimized 90-second version with each segment shrunk, labelled with the technique that shrank it. Underneath, a demand curve with the predictive scale-up starting before the peak.

```
BEFORE (11 min):  [provision 4m][image 2m][weights 4m          ][load 1m]
AFTER  (90 s):    [warm pool 0s][cached image 0s][weights 45s][load 45s]
                    ↑ pre-provisioned  ↑ baked on node  ↑ parallel stream / local NVMe

Demand  ▁▁▂▃▅▇█▇▅▃   ← dinner peak at 19:00
Scale   ▁▁▃▅▇██▇▅▃   ← predictive scale starts 17:30, reactive scale on queue depth
```

## 🔬 How it works

- **Scale on saturation signals:** **queue depth** and **waiting time**, **pending prefill tokens**, **KV-cache utilization**, and **TTFT/TPOT vs SLO**. CPU and classic "GPU util %" are misleading (lesson 009).
- **Cold start = provision + image + weights + load.** Attack each slice:
  - Keep a **warm pool** of idle servers (or reserved capacity) ready to join.
  - Pre-cache **container images** on nodes and keep them slim (weights don't belong in the image).
  - Load weights **in parallel** from object storage, from a **local NVMe cache**, or **peer-to-peer** from servers that already have them.
  - Use formats that **memory-map** straight into the GPU (e.g., safetensors) and skip warm-up work where possible.
- **Predict, then react:** traffic follows daily and weekly patterns, so pre-scale from a **forecast** (yesterday plus trend), and let reactive scaling on queue depth handle surprises.
- **Capacity models:** **reserved/committed** GPUs for the baseline, **on-demand** for predicted peaks, and **spot** only for interruptible batch work. GPUs can be scarce, so plan capacity **weeks ahead** and across regions.
- **When capacity runs out:** shed batch traffic, route to smaller or external models (lesson 015), cap `max_tokens`, and show a graceful "busy" state (Book 1 lesson 061).

## 🧩 Worked example

**Weight loading, the biggest slice:**

```
140 GB from object storage, single stream at 600 MB/s      → 233 s ≈ 4 min
140 GB, 32 parallel range requests (~5 GB/s aggregate)     → 28 s
140 GB from local NVMe (~7 GB/s)                           → 20 s
70 GB (FP8 weights, lesson 012) from local NVMe            → 10 s
```

**Maya's new scaling policy:**

| Layer | Rule |
|---|---|
| Baseline | 60 reserved servers (overnight floor + 20%) |
| Predictive | Forecast in-flight sequences for the next 60 min → pre-scale at T−30 min |
| Reactive | Queue wait p95 > 300 ms for 60 s → +10% servers. KV utilization > 85% → +10% |
| Warm pool | 8 servers booted with weights loaded, not serving |
| Scale-in | Utilization < 50% for 20 min, draining active streams first |

**Friday, replayed:** the forecast adds 30 servers starting at 17:30. A surprise spike at 18:50 pulls the **8 warm servers** into service in **< 10 s**, and reactive scaling adds 12 more in **~90 s** each. **TTFT p99 stays at 0.9 s.** The warm pool costs **8 × 8 GPUs × $2.50 ≈ $160/hour**, far less than losing 9% of the dinner rush.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Warm pools | Seconds to add capacity | Paying for idle GPUs |
| Predictive scaling | Capacity ready before the peak | Forecast errors (over or under) |
| Reserved capacity | Guaranteed GPUs, discounts | A commitment, idle off-peak |
| Spot for batch | 60–80% cheaper | Interruptions, so work must be checkpointed |
| Scale to zero | No idle cost for rare models | Minute-long cold starts for the first user |

## 🌍 Real world

- **Kubernetes** GPU autoscaling commonly uses **KEDA** or custom metrics (queue length, pending requests) instead of CPU.
- Serverless GPU platforms compete on **cold-start time** with snapshotting, cached weights, and fast loaders.
- Large AI providers run **capacity planning** weeks ahead and shift load across regions, because GPUs can't be conjured on demand.

## 📌 Cheat card

> - **Scale on queue depth, pending tokens, KV utilization**, never CPU.
> - **Cold start = provision + image + weights + load.** Cut each slice.
> - **Weights: parallel streams, local NVMe, peer-to-peer, mmap formats.**
> - **Predictive + reactive + warm pool.**
> - **Reserved baseline, on-demand peaks, spot for batch.** Degrade gracefully.

## 🧪 Feynman check

Explain the festival kitchens: why you can't open one when the line is already around the block, and why a pre-stocked kitchen with a warm oven opens so much faster.

⚠️ **Common confusion:** "Autoscaling solves traffic spikes." It solves **slow** changes. A GPU spike that arrives faster than your cold start is only absorbed by **headroom, warm pools, prediction, and degradation**. Measure your cold start, because it's your real reaction time.

## ⚡ Quick recall

1. Which metrics should drive GPU autoscaling?
<details><summary>Reveal Answer</summary>

Queue depth or wait time, pending prefill tokens, KV-cache utilization, and TTFT/TPOT relative to the SLO.
</details>

2. What are the four slices of a GPU cold start?
<details><summary>Reveal Answer</summary>

Provisioning the machine, pulling the container image, loading the weights onto the node, and loading them into GPU memory and warming up.
</details>

3. When is spot capacity appropriate?
<details><summary>Reveal Answer</summary>

For interruptible, checkpointed batch work without latency SLOs, never for live interactive serving.
</details>

## 🎤 Interview practice

**Q. "Traffic to your LLM service triples every evening and can spike 50% in minutes during promotions. GPU cold start is 6 minutes. Design the scaling strategy."**
<details><summary>Model answer</summary>

- **Signals:** scale on queue wait time, pending tokens, and KV utilization against TTFT/TPOT SLOs.
- **Baseline:** reserved capacity covering the overnight floor.
- **Daily pattern:** **predictive scaling** from historical curves, adding capacity 30–60 minutes ahead of the evening ramp.
- **Spikes faster than cold start:**
  - A **warm pool** sized to the largest 6-minute spike seen (e.g., +50% for one cold-start window).
  - Promotions are **scheduled events**, so pre-scale from the marketing calendar.
  - Reactive scaling on queue depth refills the warm pool.
- **Cut the cold start:** slim images pre-cached on nodes, weights on local NVMe or parallel-streamed, memory-mapped loading, and FP8 weights to halve the bytes.
- **Last line of defence:** priority queues (interactive first), shedding batch work, routing overflow to an external provider or a smaller model, `max_tokens` caps, and a graceful busy message.
- **Likely follow-up:** "How do you size the warm pool cost-effectively?" → the expected loss from unserved demand during a cold-start window vs the hourly cost of idle GPUs. Warm pools can also run low-priority batch work that's preempted instantly.
</details>

## 📖 Teaser

> 📖 *Capacity arrives on time now, and the voice team still says 25 ms a token is too slow for a conversation, while Maya's GPUs sit with their compute mostly idle during every decode step.*

---

⬅️ [✅ Checkpoint 30%](checkpoint-30.md) · 🗺️ [Phase map](README.md) · ➡️ [017 · Speculative Decoding & Latency Tricks](017-speculative-decoding.md)

✅ **Safe stopping point.** Tick lesson 016 in [PROGRESS.md](../../PROGRESS.md).
