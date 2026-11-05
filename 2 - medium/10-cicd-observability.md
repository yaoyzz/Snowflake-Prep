# CI/CD and Operational Observability

> **Level:** MEDIUM · **Module 10/10** · **Goal:** Instrument pipeline runs and connect Git-controlled changes to deployed Snowflake behavior.

## 1. Concept and mental model

- **Version control:** source SQL, Python, dbt and deployment configuration, not data or secrets.
- **CI:** lint, parse, unit/integration tests and security gates.
- **CD:** promoted, approved deployments with environment-specific identities.
- **Observability:** logs, alerts, data freshness, task history, resource usage and lineage.

**How to reason about it:** Annotate queries with a CI run ID and Git SHA. Maintain a deployment manifest, separate dev/test/prod Snowflake roles, and approval for validated manufacturing processes.

## 2. Pharmaceutical data-engineering scenario

An updated batch classification transformation must pass tests and be traceable to the production query and code commit.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Run in a deployment session (replace example tag values).
ALTER SESSION SET QUERY_TAG =
  'app=pharma-quality;env=dev;git_sha=abc1234;run_id=local-001';
SELECT CURRENT_SESSION(),CURRENT_ROLE(),CURRENT_WAREHOUSE();
SELECT query_id,query_tag,execution_status,total_elapsed_time
FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY(
  DATEADD('hour',-1,CURRENT_TIMESTAMP()),CURRENT_TIMESTAMP(),100))
WHERE query_tag ILIKE '%pharma-quality%'
ORDER BY start_time DESC;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** For one deployment, capture commit SHA, test artifact, deployment identity, environment, query history and roll-forward/rollback plan.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- No negative tests for schema changes.
- Alerts detect infrastructure uptime but not incorrect business metrics.
- Unreviewed AI-generated SQL enters PROD.

**Engineering decision:** Automate low-risk DEV checks; require risk-based approvals and validation evidence for regulated PROD changes.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **CI/CD and Operational Observability**. The requirement is: Instrument pipeline runs and connect Git-controlled changes to deployed Snowflake behavior. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a PR checklist and an alert matrix (pipeline failed, stale data, rejects > threshold, unusually high credits).

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply ci/cd and operational observability?**  
A: An updated batch classification transformation must pass tests and be traceable to the production query and code commit.

**Q: What is the key implementation trade-off?**  
A: Automate low-risk DEV checks; require risk-based approvals and validation evidence for regulated PROD changes.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Code commit → test evidence → release identity → monitored query.**

## 10. Official reference

- https://docs.snowflake.com/en/developer-guide/snowflake-cli/cicd/github-action

---

**Navigation:** Previous: [Query Performance and Cost Optimization](09-performance-cost.md) · Next: [Level README](README.md) · [All medium topics](README.md)
