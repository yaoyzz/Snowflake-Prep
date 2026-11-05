# FinOps, SLOs, and Incident Response

> **Level:** EXPERT · **Module 11/12** · **Goal:** Operate Snowflake using cost attribution, freshness SLOs, DQ telemetry and incident runbooks.

## 1. Concept and mental model

- **Cost attribution:** warehouse metering and serverless usage need separate accounting.
- **SLO:** measurable user-facing reliability objective; SLA is a business commitment.
- **Error budget:** tolerated unreliability over a window, relevant when prioritizing changes.
- **Incident:** detection, triage, mitigation, root cause, corrective actions, and retrospective.

**How to reason about it:** Use controlled query tags and account usage views with permissions; note usage views have publication lag. Define actual freshness thresholds based on upstream deliverables.

## 2. Pharmaceutical data-engineering scenario

A daily manufacturing KPI is late and the transform warehouse consumes twice the baseline credits.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
ALTER SESSION SET QUERY_TAG='domain=mfg;product=batch_yield;env=dev';
SELECT warehouse_name,START_TIME,END_TIME,
       CREDITS_USED_COMPUTE,CREDITS_USED_CLOUD_SERVICES
FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY
WHERE START_TIME >= DATEADD('day',-7,CURRENT_TIMESTAMP())
ORDER BY START_TIME DESC;
-- Requires access to SNOWFLAKE.ACCOUNT_USAGE; data can lag.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Track ingestion delay, source/target discrepancy, task failures, p95 query time, credits per successful batch and cost by domain.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Confusing total query execution time with billed compute.
- Resource monitor thresholds used as if they cover every serverless charge.
- Alerts without owner or response runbooks.

**Engineering decision:** Budgets for cost, SLOs for user impact, DQ gates for correctness, and query tags for attribution.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **FinOps, SLOs, and Incident Response**. The requirement is: Operate Snowflake using cost attribution, freshness SLOs, DQ telemetry and incident runbooks. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create severity rules for delayed QC data, invalid batch yield and runaway warehouse spending; assign first responder and rollback plan.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply finops, slos, and incident response?**  
A: A daily manufacturing KPI is late and the transform warehouse consumes twice the baseline credits.

**Q: What is the key implementation trade-off?**  
A: Budgets for cost, SLOs for user impact, DQ gates for correctness, and query tags for attribution.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Observability connects system health, data correctness, and business impact.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/cost-monitoring

---

**Navigation:** Previous: [Platform Engineering and Infrastructure as Code](10-platform-engineering.md) · Next: [AI-Agent-Assisted Data Platform Engineering](12-ai-agent-engineering.md) · [All expert topics](README.md)
