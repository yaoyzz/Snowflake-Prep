# Resilience, Recovery, and Disaster Readiness

> **Level:** EXPERT · **Module 06/12** · **Goal:** Set RPO/RTO, distinguish historical recovery from regional failover, and test recovery plans.

## 1. Concept and mental model

- **RPO:** maximum tolerable data loss; **RTO:** maximum tolerable time to restore service.
- **Time Travel:** query/restore prior versions only within data retention scope.
- **Fail-safe:** Snowflake-managed recovery mechanism with restrictions; not a self-service backup system.
- **Replication/failover:** feature availability and supported object coverage depend on edition/region/account configuration.

**How to reason about it:** Establish source replay and cross-region recovery strategy separately. Do not claim Time Travel covers indefinite recovery or that a replicated database automatically reproduces all integrations/secrets/tasks.

## 2. Pharmaceutical data-engineering scenario

A manufacturing dashboard is corrupted by a faulty merge before a scheduled management review.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Recovery rehearsal in a disposable DEV table:
CREATE OR REPLACE TABLE PHARMA_DEMO.OPS.RECOVERY_LAB AS
SELECT 'B-100'::VARCHAR AS batch_id,1000::NUMBER AS units;
SELECT CURRENT_TIMESTAMP() AS checkpoint_time;
UPDATE PHARMA_DEMO.OPS.RECOVERY_LAB SET units=0;
-- After recording the timestamp above, use that value to inspect:
-- SELECT * FROM PHARMA_DEMO.OPS.RECOVERY_LAB
--   AT (TIMESTAMP => '<recorded_timestamp>'::TIMESTAMP_TZ);
-- Test actual recovery using a clone/restore strategy approved for DEV.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Document success criteria: recovery at the intended point, correct data, permissions, application reconnection, evidence, and actual measured RPO/RTO.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Assuming warehouse availability means source feed recovery.
- Backups/replication exist but are never rehearsed.
- Dependent integrations are absent after failover.

**Engineering decision:** For operational data use replay and immutable sources; for disasters use edition-appropriate replication/failover and documented cross-system dependencies.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Resilience, Recovery, and Disaster Readiness**. The requirement is: Set RPO/RTO, distinguish historical recovery from regional failover, and test recovery plans. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Conduct a tabletop outage and a controlled recovery drill for a simulated accidental DELETE and an unavailable primary region.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply resilience, recovery, and disaster readiness?**  
A: A manufacturing dashboard is corrupted by a faulty merge before a scheduled management review.

**Q: What is the key implementation trade-off?**  
A: For operational data use replay and immutable sources; for disasters use edition-appropriate replication/failover and documented cross-system dependencies.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Recovery is a tested capability measured by RPO and RTO.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/data-time-travel

---

**Navigation:** Previous: [GxP, Data Integrity, and Computer System Validation](05-gxp-validation-compliance.md) · Next: [Apache Iceberg and Lakehouse Interoperability](07-iceberg-lakehouse.md) · [All expert topics](README.md)
