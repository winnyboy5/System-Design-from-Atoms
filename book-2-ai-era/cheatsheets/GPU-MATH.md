# 🧮 GPU & Token Math

> Every formula you need to size an AI system on a napkin. Worked through in [005](../phases/01-ai-foundations/005-gpu-napkin-math.md).

## 1. Traffic in tokens

```
messages/s      = DAU × messages per user ÷ 86,400   (peak ≈ 2–3× average)
input tokens/s  = messages/s × input tokens per message
output tokens/s = messages/s × output tokens per message
$/day           = Σ tokens/day × price per token (input, cached input, output separately)
```

## 2. Latency

```
TTFT       ≈ queue wait + prefill (+ network)
E2E        = TTFT + output tokens × TPOT
prefill s  ≈ 2 × params × prompt tokens ÷ achieved FLOPS
TPOT       ≈ (weight bytes + batch KV bytes) ÷ memory bandwidth (÷ TP degree, roughly)
```

## 3. Memory

```
weights          = params × bytes per param            (FP16 2, FP8 1, INT4 0.5)
KV per token     = 2 × layers × KV heads × head dim × bytes
KV per sequence  = KV per token × (prompt + output tokens)
slots per server = (total GPU memory − weights − overhead) ÷ KV per sequence
```

## 4. Fleet size

```
concurrent sequences = peak messages/s × sequence lifetime (Little's Law)
servers (memory)     = concurrent sequences ÷ slots per server
servers (prefill)    = 2 × params(active) × new input tokens/s ÷ (server FLOPS × ~0.5)
servers              = max(memory, prefill) × (1 + headroom ~30%), across zones
```

## 5. Cost

```
$ per 1M tokens      = $/GPU-hour × GPUs ÷ (tokens per hour ÷ 10⁶)
effective $ per 1M   = $ per 1M at full load ÷ utilization
break-even util      ≈ self-host $/1M at 100% ÷ API $/1M
training FLOPs       ≈ 6 × params × training tokens
```

## 6. Tricks

| Trick | Effect | Lesson |
|---|---|---|
| Prefix caching | Prefill ↓ by the cached share | [018](../phases/02-model-serving/018-prompt-and-semantic-caching.md) |
| FP8 weights + KV | ~2× slots, faster decode | [012](../phases/02-model-serving/012-quantization.md) |
| Speculative decoding | TPOT ↓ ~2–3× at small batches | [017](../phases/02-model-serving/017-speculative-decoding.md) |
| Routing to small models | $ ↓ by Σ(share × price) | [015](../phases/02-model-serving/015-model-gateway.md) |
| Shorter outputs | Total time and $ ↓ linearly | [002](../phases/01-ai-foundations/002-life-of-an-llm-request.md) |
| Spec-decode tokens/step | (1 − α^(k+1)) ÷ (1 − α) | 017 |
