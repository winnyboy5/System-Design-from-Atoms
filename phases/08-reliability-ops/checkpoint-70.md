# ✅ Checkpoint 70%: 🎉 Level-Up! Production-Ready Thinking

> ⏱ 15 min · Covers lessons **066–070** · 📈 You're at **70%**
>
> `██████████████░░░░░░` 🎉 **70%!** You now know what separates a demo from a production system: DR, observability, safe deploys, and security.

**Rules:** answer out loud or on paper **before** opening answers.

---

## ⚡ Part 1: Recall (5 questions)

1. Define RPO and RTO.
<details><summary>Answer</summary>

RPO: the max acceptable data loss (as time). RTO: the max acceptable downtime.
</details>

2. What do metrics, traces, and logs each tell you?
<details><summary>Answer</summary>

Metrics: *that* something's wrong (trends and alerts). Traces: *where* (which service or span). Logs: *why* (detailed events).
</details>

3. Why do canary releases reduce risk?
<details><summary>Answer</summary>

Only a small percentage of traffic sees the new version first, and automated comparisons trigger a rollback before most users are affected.
</details>

4. Why should JWT access tokens be short-lived?
<details><summary>Answer</summary>

They can't easily be revoked before they expire, so a short lifetime limits the damage if one is stolen. Refresh tokens handle long sessions.
</details>

5. Name three OWASP-style defenses.
<details><summary>Answer</summary>

Parameterized queries (SQL injection), output encoding/CSP (XSS), CSRF tokens/SameSite cookies, SSRF allow-lists, object-level authorization (any three).
</details>

**Score: ___ / 5**

---

## 🧪 Part 2: Explain it aloud (Feynman)

3-minute timer. Explain to a 12-year-old:

> "How does a big company release a new version of its app to millions of people without breaking it for everyone, and how do they notice fast if something goes wrong?"

Must include: **canary, feature flag, rollback, metrics/alerts, traces**.

---

## 🛠️ Part 3: Mini-design

**A fintech app** (balances, transfers). Define:
1. RPO/RTO targets and the DR strategy.
2. The three most important SLO alerts.
3. The deployment strategy for the transfer service.
4. Five security controls.

<details><summary>One good answer</summary>

1. Transfers/ledger: RPO ≈ 0, RTO ≤ 15 min → a consensus DB across AZs (or sync standby) + a warm standby region, PITR + immutable backups.
2. Transfer success rate (99.95%), transfer p99 latency (< 1 s), and ledger reconciliation mismatches (= 0).
3. Feature flags + canary (1% → 100%) with automatic rollback on success-rate regression. Expand/contract DB migrations.
4. MFA/passkeys, short-lived JWT + refresh tokens, object-level authZ on accounts, KMS encryption + field-level encryption for PII, audit logs + anomaly detection (also WAF and rate limits on login/transfer).
</details>

---

## 🎤 Part 4: Interview questions

**Q1. "What would you put on the on-call dashboard for this system?"**
<details><summary>Model answer</summary>

SLO status and burn rate per key journey, golden signals per service (RED), dependency health (DB pool usage, replication lag, cache hit ratio, queue lag), recent deploys and flag changes (for correlation), and business KPIs (orders or transfers per minute).
</details>

**Q2. "Session cookies vs JWT: which would you pick?"**
<details><summary>Model answer</summary>

For a single web app, use server-side sessions (simple and revocable). For many services, mobile, or third-party clients, use short-lived JWT access tokens + refresh tokens, validated at the gateway. Many systems combine them (a BFF holds a session and exchanges it for JWTs internally).
</details>

**Q3. "Multi-AZ vs multi-region: when is multi-AZ enough?"**
<details><summary>Model answer</summary>

When the business accepts downtime during rare region-wide outages (and has backups for DR). Multi-region is justified by strict RTO/RPO, global latency needs, or regulatory requirements, at significant cost and complexity.
</details>

---

## 📊 Score yourself

| Recall score | What to do |
|---|---|
| 5/5 | 🚀 On to [071 · Service Discovery & Mesh](071-service-discovery-and-mesh.md) |
| 4/5 | ✅ Move on. Reread the cheat card you missed. |
| ≤ 3/5 | 🔁 Revisit [066](066-multi-region-and-disaster-recovery.md), [067](067-observability.md), [069](069-authn-authz-oauth-jwt.md) |

🏆 **Level-up reward:** 70%. Ten more lessons to the 🏁 **Practical Mastery gate**!

---

⬅️ [070 · Security Essentials](070-security-essentials.md) · 🗺️ [Phase map](README.md) · ➡️ [071 · Service Discovery & Mesh](071-service-discovery-and-mesh.md)

✅ Tick **Checkpoint 70%** in [PROGRESS.md](../../PROGRESS.md). 🎉
