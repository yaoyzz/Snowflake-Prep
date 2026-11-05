# Data Quality and Reconciliation

> **Level:** MEDIUM · **Module 07/10** · **Goal:** Specify data contracts, implement quantitative validation, and route anomalies to quarantine.

## 1. Concept and mental model

- **Completeness:** required keys, columns and expected time windows.
- **Uniqueness:** source business keys at the correct grain.
- **Validity:** ranges, units, enumerations and referential integrity.
- **Reconciliation:** source-to-target counts, sums, checksums and source revision correctness.

**How to reason about it:** DQ outcomes should be versioned and linked to a run ID; distinguish data-quality violations from infrastructure failures and from legitimate unknowns.

## 2. Pharmaceutical data-engineering scenario

An assay batch must not appear in the release-quality mart if its rejected units exceed production or if its batch identifier is missing.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
WITH quality AS (
 SELECT batch_id,plant_id,units_produced,units_rejected,
        CASE WHEN batch_id IS NULL THEN 'MISSING_BATCH_ID'
             WHEN units_produced IS NULL OR units_rejected IS NULL
               THEN 'MISSING_QUANTITY'
             WHEN units_produced < 0 OR units_rejected < 0
               OR units_rejected > units_produced THEN 'INVALID_COUNTS'
             ELSE 'PASS' END AS dq_status
 FROM PHARMA_DEMO.RAW.BATCH_EVENTS
)
SELECT dq_status,COUNT(*) AS records
FROM quality GROUP BY dq_status ORDER BY dq_status;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Assert expected PASS percentage; reconcile source count and total units. Fail release on critical defects rather than silently discarding records.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Silently filtering invalid records lowers operational totals.
- Applying one global threshold across all analytical domains.
- Confusing missing history with an ingestion bug.

**Engineering decision:** Keep raw truth and quarantine records; downstream release logic can gate only on approved, documented quality rules.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Data Quality and Reconciliation**. The requirement is: Specify data contracts, implement quantitative validation, and route anomalies to quarantine. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Add a duplicate-key rule, source lateness SLA, and failed-row sampling procedure for one simulated ETL run.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply data quality and reconciliation?**  
A: An assay batch must not appear in the release-quality mart if its rejected units exceed production or if its batch identifier is missing.

**Q: What is the key implementation trade-off?**  
A: Keep raw truth and quarantine records; downstream release logic can gate only on approved, documented quality rules.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Correctness requires contracts, tests, reconciliation, and a response plan.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/data-quality-intro

---

**Navigation:** Previous: [dbt Models, Tests, and Incremental Builds](06-dbt-data-modeling.md) · Next: [Security and Governance Fundamentals](08-security-governance.md) · [All medium topics](README.md)
