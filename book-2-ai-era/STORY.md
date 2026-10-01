# 📖 The Story: Pantry Learns to Think

> The sequel to [Book 1's story](../STORY.md). I'm still your narrator, and Maya is still our hero.
> Every lesson opens with a **📖 Story** scene and ends with a **📖 Teaser**. Read them for motivation, or skip them for the facts. The lessons work either way.

---

## Pull up a chair, again

When we left Maya, she was standing at a whiteboard with a sticky note that said **"Ask the Chef"**: an AI cooking assistant for 10 million people a day. Her design review went well.

Then she shipped it.

And I watched the strangest month of her career begin. Her servers had never been this **expensive**: one bad prompt cost more than a week of database bills. Her responses had never been this **slow**: eight seconds of a blank screen while a GPU thought. And for the first time, her system could be **confidently wrong**: it told a customer that a peanut sauce was nut-free.

Everything Maya learned in Book 1 still mattered. Caches, queues, rate limits, and idempotency all came back, **wearing new costumes**. But there were new atoms too: tokens, KV caches, embeddings, retrieval, agents, evals, and guardrails.

This book is the story of Pantry becoming an **AI-native company**: one feature, one failure, one atom at a time. As before, **you'll never learn a concept before you need it.**

---

## 👥 The cast

| Character | Who they are | Where they appear |
|---|---|---|
| **Maya** | Now a senior engineer who leads design reviews. She knows the classic atoms cold, and she's about to discover which of them still hold. | Everywhere |
| **Me, your author** | The narrator. I've shipped AI features that embarrassed me in public, and I'll tell you exactly how. | The whole way |
| **You** | The reader. In the final chapter, I hand you the marker again. | The whole way |

---

## 🍲 What Pantry builds (and which lessons each feature teaches)

| Pantry AI feature | Teaches you |
|---|---|
| "Ask the Chef" chat assistant | Tokens, latency, serving, streaming, batching, caching |
| Recipe & allergen answers grounded in Pantry's data | Embeddings, vector search, RAG, freshness, permissions |
| The ordering agent that books, reorders, and refunds | Tool calling, agent loops, durable execution, approvals |
| Quality dashboards and the AI on-call | Evals, observability, guardrails, security, cost |
| Pantry for Business (kitchens' private documents) | Permission-aware retrieval, privacy, multi-tenancy |
| "Talk to Pantry" voice ordering | Real-time pipelines and end-to-end latency budgets |

---

## 📚 Chapter map

| Chapter | Phase | The story beat |
|---|---|---|
| 1 · Pantry Learns to Talk | [01 AI foundations](phases/01-ai-foundations/README.md) | "Ask the Chef" launches, and Maya learns that words cost money and time |
| 2 · The Kitchen Behind the Model | [02 Model serving](phases/02-model-serving/README.md) | GPU bills explode and answers crawl, so Maya learns to serve models like a short-order cook |
| 3 · Teaching the Model What Pantry Knows | [03 Retrieval & data](phases/03-retrieval-data/README.md) | The assistant invents a nut-free sauce, and Maya grounds it in real data |
| 4 · The Model Gets Hands | [04 Agents](phases/04-agents/README.md) | The assistant starts placing orders and issuing refunds, and Maya learns to keep it on a leash |
| 5 · Trust, Measured | [05 Quality, safety & ops](phases/05-quality-safety-ops/README.md) | Attacks, regressions, and a runaway bill teach Maya to measure everything |
| 6 · The Design Reviews | [06 AI case studies](phases/06-ai-case-studies/README.md) | Maya designs five AI systems end to end, and then hands the marker to you |
