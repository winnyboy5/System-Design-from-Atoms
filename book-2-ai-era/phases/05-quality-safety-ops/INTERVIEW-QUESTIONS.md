# 🎤 Phase 05 Interview Question Bank: Quality, Safety & AI Ops

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive
> Answer out loud, **then** open the model answer. Time yourself: 🟢 1 min, 🟡 3 min, 🔴 5 min.

---

### 🟢 1. What is a golden set? · [037]
<details><summary>Model answer</summary>

A versioned set of representative, adversarial, and incident-derived cases with expected outcomes or rubrics, used to evaluate every change.
</details>

### 🟢 2. What is LLM-as-judge, and its main risk? · [038]
<details><summary>Model answer</summary>

Using a model to score outputs against a rubric. Risks are biases (verbosity, position, self-preference) and drift, so calibrate against human labels.
</details>

### 🟢 3. What is indirect prompt injection? · [041]
<details><summary>Model answer</summary>

Malicious instructions hidden in content the system reads (documents, emails, web pages, tool results) that the model follows as if they were instructions.
</details>

### 🟢 4. Why should AI features be bulkheaded from core flows? · [043]
<details><summary>Model answer</summary>

So a slow or failed model can't block essential functions like checkout. AI enhances asynchronously, and the core works without it.
</details>

### 🟡 5. How do you tell if an eval improvement is real? · [037]
<details><summary>Model answer</summary>

Repeat runs per case, compare versions paired on the same cases, compute confidence intervals, and check per-slice results (not just the average).
</details>

### 🟡 6. What goes into an AI trace? · [039]
<details><summary>Model answer</summary>

Spans for guardrails, retrieval (chunk IDs, scores), model calls (model/prompt versions, tokens, TTFT/TPOT, cost), tool calls (args, results), and agent steps, plus redacted, sampled payloads.
</details>

### 🟡 7. Your AI bill jumped 70%. First steps? · [042]
<details><summary>Model answer</summary>

Attribute spend by feature, tenant, model, and prompt version via gateway metering. Find the top drivers (loops, batch reruns, bloated prompts, unrouted traffic), fix them, then add quotas, budgets, and anomaly alerts.
</details>

### 🟡 8. How do you roll out a prompt change safely? · [044]
<details><summary>Model answer</summary>

A versioned artifact → eval gate → shadow → canary 1/10/50/100% behind a flag, with automatic rollback on quality, safety, latency, or cost SLIs, and versions recorded per request.
</details>

### 🔴 9. Design the guardrail and security architecture for an agent that reads emails and can send replies. · [040, 041, 036]
<details><summary>Model answer</summary>

Quarantined no-tools reader for inbound mail → structured summary. A privileged planner with user-scoped tools. Sends and external recipients need confirmation with the exact message. No remote images or unknown links rendered. An egress allow-list. Input/output guardrails with measured precision and recall. A red-team corpus in CI. Traces with redaction.
</details>

### 🔴 10. Define and operate SLOs for an AI assistant that relies on two model providers. · [007, 038, 043]
<details><summary>Model answer</summary>

- **Service SLO:** "useful answer or graceful handoff within 3 s TTFT", 99.95%.
- **Quality SLOs:** golden-set score and sampled judge groundedness per rung, with error budgets.
- **Cost SLO:** $ per resolved conversation.
- **Mechanics:** a fallback ladder across providers, regions, and a self-hosted small model. Breakers on latency and errors. Chaos drills. Dashboards of traffic per rung.
- **Change control:** a canary + auto-rollback pipeline. Quality budgets freeze changes when burned.
</details>
