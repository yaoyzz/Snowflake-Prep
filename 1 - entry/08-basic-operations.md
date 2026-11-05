# Query History, Time Travel, and Basic Operations

> **Level:** ENTRY · **Module 08/08** · **Goal:** Investigate SQL failures, inspect query profiles, and understand recovery and warehouse usage.

## 1. Concept and mental model

- **Query history:** identity, start/end, elapsed time, bytes scanned, errors, and query IDs.
- **Query Profile:** execution plan, operators, scan/partition pruning, spill, and bottlenecks.
- **Time Travel:** recover or query previous object state within configured retention limits.
- **Cost:** credits depend on warehouse size/runtime plus other services/storage.

**How to reason about it:** First identify the executed query and impacted objects. Verify retention before attempting `AT`/`BEFORE` or `UNDROP`. Recovery is not a replacement for immutable source backups and approval-based restoration.

## 2. Pharmaceutical data-engineering scenario

The morning batch-yield dashboard is missing records after an engineer overwrites a reporting table.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
SELECT CURRENT_TIMESTAMP() AS investigated_at;
SELECT query_id,query_text,execution_status,
       total_elapsed_time,bytes_scanned,error_message
FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY(
  DATEADD('hour',-2,CURRENT_TIMESTAMP()),CURRENT_TIMESTAMP(),100))
ORDER BY start_time DESC;
-- On an existing table with appropriate retention:
-- SELECT * FROM PHARMA_DEMO.MART.SOME_TABLE
--   AT (TIMESTAMP => '2026-10-10 09:00:00'::TIMESTAMP_TZ);
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Capture query ID, role, error text, affected table, source watermark, and evidence that corrected output matches the expected record count.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Recovery outside retention window is impossible through Time Travel.
- History visibility depends on role and retention.
- Profiling from a sample may not match the production workload.

**Engineering decision:** Triaging order: correctness → availability/freshness → performance/cost. Log a query tag for traceability.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Query History, Time Travel, and Basic Operations**. The requirement is: Investigate SQL failures, inspect query profiles, and understand recovery and warehouse usage. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Deliberately write a failing query in DEV, collect its query ID, classify the failure, and document a safe fix.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply query history, time travel, and basic operations?**  
A: The morning batch-yield dashboard is missing records after an engineer overwrites a reporting table.

**Q: What is the key implementation trade-off?**  
A: Triaging order: correctness → availability/freshness → performance/cost. Log a query tag for traceability.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Find the failing query, prove the root cause, then repair with evidence.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/ui-query-profile

---

**Navigation:** Previous: [Basic Dimensional Data Modeling](07-basic-data-modeling.md) · Next: [Level README](README.md) · [All entry topics](README.md)
