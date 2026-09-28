# 🎤 Phase 08 Interview Question Bank: Reliability, Security & Ops

> 🟢 warm-up · 🟡 standard · 🔴 deep-dive. Answer out loud first.

---

### 🟢 1. Why add jitter to retries? · [063]
<details><summary>Model answer</summary>

To desynchronize clients, so their retries don't hit the recovering service in synchronized waves.
</details>

### 🟢 2. What does a circuit breaker do? · [064]
<details><summary>Model answer</summary>

It stops calling a failing dependency after a failure threshold (open state: fail fast), then tests recovery with trial calls (half-open) before resuming.
</details>

### 🟢 3. RPO vs RTO? · [066]
<details><summary>Model answer</summary>

RPO: the acceptable data loss window. RTO: the acceptable time to recover service.
</details>

### 🟢 4. Authentication vs authorization? · [069]
<details><summary>Model answer</summary>

AuthN verifies identity. AuthZ determines the allowed actions for that identity.
</details>

### 🟡 5. How do you prevent cascading failures in microservices? · [063, 064]
<details><summary>Model answer</summary>

Timeouts and deadlines, retries with backoff + jitter and budgets, circuit breakers, bulkheads, load shedding, fallbacks, and capacity headroom. Avoid long sync call chains.
</details>

### 🟡 6. What would you monitor for a new service? · [067]
<details><summary>Model answer</summary>

SLIs/SLOs for the key journeys, RED metrics per endpoint, USE for resources, dependency health (DB, cache, queues), traces, structured logs with trace IDs, burn-rate alerts, and business KPIs.
</details>

### 🟡 7. Blue-green vs canary? · [068]
<details><summary>Model answer</summary>

Blue-green: two full environments with an instant 100% switch and instant rollback, at 2× capacity. Canary: a gradual percentage rollout with metric comparison, a smaller blast radius, and slower. Both need backward-compatible DB changes.
</details>

### 🟡 8. How do you handle JWT revocation? · [069]
<details><summary>Model answer</summary>

Short-lived access tokens + rotating refresh tokens stored server-side (revocable). A denylist of token IDs for emergencies. Key rotation to invalidate everything if needed.
</details>

### 🟡 9. Snowflake vs UUID? · [072]
<details><summary>Model answer</summary>

Snowflake: 64-bit, time-sortable, needs worker ID assignment and clock care. UUIDv4: 128-bit random with no coordination, but bad index locality. UUIDv7: 128-bit, time-ordered, no coordination (a good default).
</details>

### 🔴 10. Design DR for a SaaS with RPO 5 min and RTO 30 min. · [066]
<details><summary>Model answer</summary>

Async cross-region DB replication (lag monitored well under 5 min) + continuous WAL archiving to another region. A warm standby region with infrastructure as code, pre-provisioned but scaled down. Replicated secrets and config. A global LB / DNS failover with health checks. Runbooks + automation to scale up and promote. Quarterly DR drills measuring the actual RPO/RTO. Immutable backups for logical corruption.
</details>

### 🔴 11. A retry storm keeps a service down after a brief dependency blip. Explain and fix. · [063, 061]
<details><summary>Model answer</summary>

A metastable failure: retries (multiplied across layers, synchronized) keep the load above capacity, so queues and timeouts perpetuate the overload. Fix: retry at one layer, exponential backoff + full jitter, retry budgets, circuit breakers, load shedding, and bounded queues. In the moment, shed at the edge or disable retries via a flag to let the system recover.
</details>

### 🔴 12. Design authentication and authorization for a multi-tenant B2B SaaS. · [069, 070]
<details><summary>Model answer</summary>

OIDC SSO per tenant (SAML/OIDC federation), MFA. Short-lived JWTs with tenant_id and roles claims, validated at the gateway. Every query is scoped by tenant_id (row-level security or per-tenant schemas/DBs for isolation). RBAC per tenant with custom roles, or ReBAC for sharing. Audit logs. Service-to-service mTLS. Secrets per tenant in a vault. Rate limits per tenant.
</details>

---

🗺️ [Phase map](README.md) · 📌 [Cheatsheet](CHEATSHEET.md) · ➡️ [Phase 09 Core case studies](../09-core-case-studies/README.md)
