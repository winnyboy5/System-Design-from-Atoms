# 📌 Phase 05 Cheatsheet: Quality, Safety & AI Ops

> One screen. Reread it before each study session for spaced review.

## 🧠 Core ideas

| # | Idea in one line |
|---|---|
| 037 | **Golden set (real + incidents + adversarial, sliced) → graders (code > judge > human) → N runs → paired CIs → CI gate.** |
| 038 | **A/B on outcomes by user + guardrail metrics.** Sampled LLM judges, calibrated vs humans (κ), bias-aware. |
| 039 | **Span every AI step** with versions, tokens, TTFT/TPOT, cost. Metadata 100%, payloads sampled + redacted. |
| 040 | **Input + output guards, streamed, with actions.** Measure precision and recall. Over-refusal is a failure. |
| 041 | **Injection: assume fooled.** User-scoped tools, approvals, quarantine untrusted text, close exfiltration channels. |
| 042 | **Attribute, unit economics, levers (route, cache, trim, batch, self-host), quotas, budgets, anomaly alerts.** |
| 043 | **Core never waits on AI.** TTFT/stream timeouts, breakers, a diverse fallback ladder, shed low value first. |
| 044 | **Versioned prompts/models, eval → shadow → canary → auto-rollback.** Minimize PII, residency routing, zero retention. |

## 🔢 Numbers to know

```
Eval CI: 400 cases ≈ ±3 pts (95%) on a ~90% pass rate · 100 cases ≈ ±7 pts
A/B size per arm ≈ 16 p(1−p) / δ²  (20% baseline, 1 pt δ → ~25,600)
Judge-human agreement: aim for κ ≥ 0.6–0.7 before trusting a judge
Trace metadata ~1–2 KB/request · payloads ~5–10 KB (sample them)
Self-host break-even utilization ≈ self-host $/1M at 100% ÷ API $/1M (often ~25–40%)
Timeouts: TTFT ~1–3 s, stream gap ~5 s · 1 retry with jitter
Canary: 1% → 10% → 50% → 100%, auto-rollback in < 1 min
```

## 🧮 Formulas

| Formula | Use |
|---|---|
| SE = √(p(1−p)/n), 95% CI ≈ ±2 SE | Eval noise |
| n/arm ≈ 16 p(1−p) / δ² | A/B sizing |
| P(all rungs down) = Π P(rung down) (if independent) | Fallback availability |
| Unit cost = Σ token cost ÷ units of value | Unit economics |
| Precision = TP/(TP+FP), Recall = TP/(TP+FN) | Guardrail quality |

## 🧩 Mnemonics

- **"Thermometer before taster"**: code graders first.
- **"Count empty plates"**: outcomes beat judge scores.
- **"Flight recorder"**: AI traces.
- **"Inspectors at the door and the pass"**: guardrails.
- **"No keys, no stamps"**: containing injection.
- **"Sous-chef, simpler menu, door stays open"**: graceful degradation.
