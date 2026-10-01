# 🏁 Checkpoint 80%: The AI-Era Practical Mastery Gate

> ⏱ 30–45 min · Covers **lessons 001–040** · 📈 You're at **80%**
>
> `████████████████░░░░` 🏆🏆🏆 **YOU MADE IT TO 80%.** You can now design, serve, ground, operate, and safeguard AI systems end to end.

This gate is a **full AI system design mock interview** plus a **self-audit**. Treat it like the real thing: set a timer, talk out loud, and draw on paper.

> 📖 *I watched Maya present "Ask the Chef v3" to a room that had seen it fail in public. She opened with requirements and numbers, not models. Nobody interrupted for forty minutes. Your turn.*

---

## ⚡ Part 1: Rapid-fire recall (10 questions, 10 minutes)

1. Prefill vs decode: which is compute-bound, and which sets TTFT?
<details><summary>Reveal Answer</summary>

Prefill is compute-bound and sets TTFT. Decode is memory-bandwidth-bound and sets TPOT.
</details>

2. Weights of a 70B model in FP8? KV per token formula?
<details><summary>Reveal Answer</summary>

70 GB. KV/token = 2 × layers × KV heads × head size × bytes.
</details>

3. Three SLO families for AI features?
<details><summary>Reveal Answer</summary>

Service (availability, TTFT/TPOT), quality (golden set, sampled judge, user signals), cost ($ per request or conversation).
</details>

4. Why continuous batching + paged KV?
<details><summary>Reveal Answer</summary>

Slots refill every decode step (throughput, no head-of-line blocking), and KV memory is allocated on demand in blocks (no fragmentation), so far more concurrent sequences fit.
</details>

5. The three caches, from always-safe to risky?
<details><summary>Reveal Answer</summary>

Prefix (KV) cache, exact response cache, semantic cache.
</details>

6. RAG's two failure piles and their metrics?
<details><summary>Reveal Answer</summary>

Retrieval misses (recall@k) and unfaithful generation (faithfulness).
</details>

7. Where do tenant and ACL filters come from?
<details><summary>Reveal Answer</summary>

The verified identity, injected by code into a filter-aware search, plus a late check on final chunks.
</details>

8. How do durable workflows make agent side effects exactly-once?
<details><summary>Reveal Answer</summary>

Completed steps are replayed from recorded history (not re-run), and each side effect carries an idempotency key.
</details>

9. Why can an eval showing 84 vs 82 be meaningless?
<details><summary>Reveal Answer</summary>

Small sets and random outputs give wide confidence intervals: you need repeated runs, paired comparisons, and per-slice statistics.
</details>

10. Why aren't system-prompt rules a safety control?
<details><summary>Reveal Answer</summary>

They're requests the model may ignore or be tricked out of. Safety needs independent, measured guardrails and code-enforced permissions.
</details>

**Score: ___ / 10**

---

## 🎤 Part 2: Full mock interview (45 minutes, timed)

Pick **one** prompt you haven't studied as a case study. Use the [AI interview framework](../../cheatsheets/AI-INTERVIEW-FRAMEWORK.md).

| Prompt | Hints (only if you get stuck!) |
|---|---|
| **A. An AI tutor for a language-learning app** (chat + voice, 5M DAU) | Small model for drills, large for explanations · streaming · per-user memory · safety for minors · cost per lesson |
| **B. A support copilot for 2,000 agents** (drafts replies from tickets + KB) | Permission-aware RAG · CDC from the KB · citations · human-in-the-loop by design · eval on resolution |
| **C. A "chat with your bank statements" feature** | PII and residency · deterministic math (never the model) · structured outputs · guardrails on advice · audit traces |
| **D. An AI meal-planning agent that orders groceries** | Durable workflows · idempotent orders · approval tiers · budgets · substitution logic in code |

**Record yourself** and play it back. You'll hear every "um, the model just…": those are your gaps.

---

## 📋 Part 3: Self-audit rubric

Score each 0 (missed), 1 (partial), or 2 (strong):

| Skill | Score |
|---|---|
| Asked "should this be AI?" and split deterministic vs AI parts | /2 |
| Requirements with numbers: quality bar, error tolerance by risk, latency (TTFT/TPOT), cost budget | /2 |
| Token and GPU napkin math (tokens/day, $/day, concurrency or GPUs) with "so this means…" | /2 |
| Serving design: model choice/routing, batching/caching, streaming, a gateway | /2 |
| Grounding: retrieval design, freshness, permissions, citations | /2 |
| Actions: tool design, budgets, durability, idempotency, approvals (if agentic) | /2 |
| Quality: golden set, online evals, quality SLOs | /2 |
| Safety and security: guardrails, injection awareness, PII handling | /2 |
| Failure modes and degraded modes (provider down, GPU shortage) | /2 |
| Stated trade-offs out loud and managed time | /2 |

**Total: ___ / 20**

| Total | Meaning |
|---|---|
| 16–20 | 🏁 **AI-era practical mastery.** Lessons 041–050 are your senior/staff edge. |
| 11–15 | 🟡 Solid base. Redo a mock with a different prompt, focusing on your 0/1 rows. |
| ≤ 10 | 🔁 Revisit the phase cheatsheets for your weak rows, then retry in a few days. Totally normal. |

---

## 🗺️ Part 4: Where your weak spots map

| Weak row | Revisit |
|---|---|
| Requirements / should-this-be-AI | [008](../01-ai-foundations/008-ai-requirements.md), [006](../01-ai-foundations/006-non-determinism.md) |
| Napkin math | [005](../01-ai-foundations/005-gpu-napkin-math.md), [Phase 01 cheatsheet](../01-ai-foundations/CHEATSHEET.md) |
| Serving | [Phase 02 cheatsheet](../02-model-serving/CHEATSHEET.md) |
| Grounding | [Phase 03 cheatsheet](../03-retrieval-data/CHEATSHEET.md) |
| Agents | [Phase 04 cheatsheet](../04-agents/CHEATSHEET.md) |
| Quality and safety | [037](037-offline-evals.md), [038](038-online-evals.md), [040](040-guardrails-and-moderation.md) |

---

## 🎉 Celebrate!

You've covered the **AI-era core**: the part that shows up in nearly every AI system design interview and most real AI projects. Take a victory lap. 🏆

**What's next?**
- **Lessons 041–050** go deeper into production: prompt injection, cost engineering, reliability, privacy and rollouts, then five full case studies and the capstone.
- Or pause here and do **one AI mock interview per week**. Spaced practice beats cramming.

---

⬅️ [040 · Guardrails & Moderation](040-guardrails-and-moderation.md) · 🗺️ [Phase map](README.md) · ➡️ [🅱️ 041 · Prompt Injection & AI Security](041-prompt-injection-security.md)

✅ Tick **🏁 Checkpoint 80%** in [PROGRESS.md](../../PROGRESS.md). 🏆
