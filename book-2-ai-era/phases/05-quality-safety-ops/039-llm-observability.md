# 039 · LLM Observability & Tracing

> ⏱ 12 min · 📈 78% · 🅰️ AI-era core · Phase 05: Quality, Safety & AI Ops
>
> `███████████████░░░░░` 78% of Book 2
>
> 🧬 **Atoms used:** observability [B1·067] · object storage [B1·042] · [015] · [021] · [030] · [032]

---

## 📖 Story

A complaint arrives: "**Last Tuesday, your agent ordered beef lasagna. I asked for the vegetarian one.**"

Maya searches the logs. She finds: `POST /agent/run 200 OK 3,214 ms`. That's it.

She can't see **what the customer typed**, **which recipes retrieval returned**, **which prompt version** ran, **which model** answered, **what the model decided**, **which tools** it called with **which arguments**, or **what each step cost**. The agent made eleven decisions in those 3.2 seconds, and every one of them left **no trace**.

Her Book 1 instincts kick in: distributed tracing. But her tracing shows **one span** for the whole model call, a grey box again, just like in lesson 002.

I told Maya that for AI systems, observability has to see **inside the reasoning**: every prompt, every retrieved chunk, every tool call, every token count, as a trace you can **replay**. And because those traces contain customers' words, it has to do all that **without becoming a privacy leak**. Let me show you what a good AI trace looks like.

## 🎯 One-sentence idea

**LLM observability extends distributed tracing with AI-specific spans (retrieval with chunk IDs and scores, model calls with model and prompt versions, token counts, cost, and latency phases, tool calls with arguments and results, and guardrail decisions), so any answer can be explained, replayed, and aggregated, with payloads sampled and redacted for privacy.**

## 🧸 Analogy

A **flight recorder** for every order the kitchen cooks:

- It records **which ingredients** were fetched from the pantry (retrieval), **which recipe card version** was used (prompt version), **which chef** cooked (model version), every **step and tool** they used, **how long** each took, and **what it cost**.
- When a diner says "I ordered vegetarian!", you **replay the recording** and see exactly where it went wrong.
- And because the recordings capture diners' conversations, they're **locked**, **trimmed of personal details**, and **kept only as long as needed**.

## 🖼️ Visual

*Diagram brief:* a trace tree for one agent run. The root span is the request, with child spans for input guardrails, query rewrite (a model call), retrieval (with chunk IDs and scores), the main model call (model version, prompt version, tokens in/out, TTFT, cost), two tool-call spans with arguments and results, and an output guardrail span. Each span shows a small cost and latency label.

```
agent.run  trace=7f3a  user=u_93 (pseudonymized)  3,214 ms  $0.031
├─ guard.input            12 ms   pass
├─ llm.rewrite            140 ms  small-v3 · prompt rw@v4 · in 310 / out 18 · $0.0001
├─ retrieve.hybrid        96 ms   top5=[r_8812(0.91), r_1203(0.88) "beef lasagna", …]
├─ llm.agent_step#1       610 ms  large-fp8@2026-09-30 · prompt agent@v17 · TTFT 290 · in 3,420 / out 64
│   └─ decision: add_to_cart(item="lasagna-beef-16", qty=1)   ← 🔎 the bug
├─ tool.add_to_cart       48 ms   args={item:"lasagna-beef-16",qty:1} → ok
├─ llm.agent_step#2       520 ms  …
└─ guard.output           9 ms    pass
```

## 🔬 How it works

- **Trace every AI step as a span:** model calls (model + version, **prompt template + version**, parameters, tokens in/out, **TTFT/TPOT**, cost, finish reason), retrieval (query, **chunk IDs + scores**, index version), tool calls (name, args, result status, latency), guardrails (decision, scores), and agent steps (step number, budget used). Follow **OpenTelemetry's generative-AI semantic conventions** where possible.
- **Metadata always, payloads sampled:** keep span **metadata for 100%** of requests (cheap, aggregatable). Store full **prompt and response payloads** for a sample, plus **all** errors, flagged, and complained-about traces. Payloads go to object storage, linked by trace ID.
- **Privacy by design:** redact or pseudonymize PII **before** storing payloads, restrict access (role-based, audited), set retention (e.g., 30 days for payloads, 13 months for metadata), and honour deletions (lesson 024).
- **Aggregate into AI dashboards:** cost and tokens by feature, tenant, model, and prompt version. Latency phases. Tool error rates. Retrieval score distributions. Guardrail trigger rates. Agent steps per task. All sliced by **version**, so any change is visible.
- **Close the loop:** link traces to **user feedback**, **online judge scores** (lesson 038), and **eval cases**: any bad trace can be turned into a golden-set regression case in one click (lesson 037). **Replay** a trace against a new prompt version to verify a fix.

## 🧩 Worked example

**Storage napkin for Pantry:**

```
200M AI requests/day × ~1.5 KB of span metadata    ≈ 300 GB/day (100% kept)
Payloads ≈ 8 KB per request (prompt + output + chunks)
  100% would be 1.6 TB/day 😬 → sample 5% + all flagged ≈ 90 GB/day
Retention: payloads 30 days ≈ 2.7 TB · metadata 13 months ≈ 115 TB (columnar, compressed ~10× → ~12 TB)
```

**The vegetarian lasagna, replayed:** the complaint carries an order ID → a trace link → the retrieval span shows the vegetarian recipe ranked **#3**, below the beef one. The customer had typed "**the lasagna, veggie**" and the rewrite step dropped "veggie". **Root cause found in 4 minutes.** The trace becomes eval case #613, the rewrite prompt is fixed, and the replay of the trace now picks the right dish.

## ⚖️ Trade-offs

| Decision | You gain | You pay |
|---|---|---|
| Full payload logging | Perfect debugging | Huge storage, privacy risk |
| Sampled payloads + 100% metadata | Affordable, aggregatable | Some traces lack payloads (keep errors) |
| PII redaction before storage | Privacy, compliance | Detector misses, less debug detail |
| Versioned prompts in every span | Changes are attributable | Discipline in the prompt registry |
| Trace → eval-case workflow | A golden set that grows from reality | Tooling and triage time |

## 🌍 Real world

- **OpenTelemetry** has semantic conventions for **generative-AI spans** (model, tokens, operation names), supported by many SDKs and observability vendors.
- LLM observability platforms record traces, prompts, costs, and evaluations together, and link traces to datasets for regression testing.
- Providers' API responses include **token usage**, which is the raw material for cost attribution.

## 📌 Cheat card

> - **Span every AI step:** retrieval, model calls, tools, guardrails, agent steps.
> - **Record versions:** model, prompt, index, tools.
> - **Tokens, TTFT/TPOT, cost per span.**
> - **Metadata 100%, payloads sampled + all errors and flags.** Redact PII. Retain briefly.
> - **Bad trace → eval case → replay the fix.**

## 🧪 Feynman check

Explain the kitchen's flight recorder: what it records, how it solves the "I ordered vegetarian!" mystery, and why the recordings must be locked and trimmed.

⚠️ **Common confusion:** "Our APM already traces the model call." A single span saying "model call, 3 s" hides **what was asked, what was retrieved, which version answered, and what it decided**. AI observability needs the **content and decisions**, which classic APM never captured, plus the privacy controls that content requires.

## ⚡ Quick recall

1. Name four attributes a model-call span should record.
<details><summary>Reveal Answer</summary>

Any four of: model and version, prompt template and version, parameters, input/output tokens, TTFT/TPOT, cost, and finish reason.
</details>

2. Why sample payloads but keep all metadata?
<details><summary>Reveal Answer</summary>

Metadata is small and needed for aggregates and dashboards. Full payloads are large and sensitive, so a sample (plus all errors and flagged traces) is enough for debugging.
</details>

3. How do traces improve evals?
<details><summary>Reveal Answer</summary>

Bad production traces can be turned into golden-set regression cases, and fixes can be verified by replaying traces against new versions.
</details>

## 🎤 Interview practice

**Q. "Design observability for a RAG + agent product handling 50M requests a day across 300 enterprise tenants."**
<details><summary>Model answer</summary>

- **Instrumentation:** OpenTelemetry spans for gateway, guardrails, rewrite, retrieval (chunk IDs, scores, index version), model calls (model/prompt versions, tokens, TTFT/TPOT, cost), tool calls, and agent steps, all tagged with tenant and feature.
- **Pipeline:** spans → a collector → a columnar store for metadata (100%). Payloads (redacted) → object storage for a 5% sample + all errors, flags, and complaints. Per-tenant encryption keys.
- **Privacy:** PII redaction before storage, tenant-scoped access controls, audited access, retention limits, and deletion propagation. Some tenants can opt out of payload storage entirely.
- **Dashboards and alerts:** cost per tenant/feature, latency phases, tool error rates, retrieval score drift, guardrail triggers, and quality SLIs per version.
- **Workflows:** trace links from support tickets, one-click "add to eval set", and replay against candidate versions.
- **Scale:** 50M × ~1.5 KB ≈ 75 GB/day of metadata. Payload sample ≈ 20 GB/day.
- **Likely follow-up:** "A tenant demands no prompt logging at all." → metadata-only tracing for that tenant (tokens, latencies, versions, decisions), with payload capture only on explicit, time-limited debug consent.
</details>

## 📖 Teaser

> 📖 *Now every decision leaves a trace, and one trace makes Maya's blood run cold: a customer asked whether rare chicken is safe if it's very fresh, and the chef said yes.*

---

⬅️ [038 · Online Evals, A/B Tests & LLM-as-Judge](038-online-evals.md) · 🗺️ [Phase map](README.md) · ➡️ [040 · Guardrails & Moderation](040-guardrails-and-moderation.md)

✅ **Safe stopping point.** Tick lesson 039 in [PROGRESS.md](../../PROGRESS.md).
