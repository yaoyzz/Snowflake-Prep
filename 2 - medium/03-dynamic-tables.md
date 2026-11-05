# Dynamic Tables and Target Lag

> **Level:** MEDIUM · **Module 03/10** · **Goal:** Build declarative incremental transformations and understand refresh cost, modes, and freshness.

## 1. Concept and mental model

- **Dynamic table:** materializes SELECT results and refreshes automatically.
- **TARGET_LAG:** a freshness objective, not a fixed trigger or hard SLA.
- **Refresh mode:** INCREMENTAL/FULL/AUTO/ADAPTIVE (availability and semantics vary; verify the deployed platform).
- **DOWNSTREAM:** useful for intermediate dynamic tables refreshed only when downstream consumers need data.

**How to reason about it:** Choose a declarative SELECT if there is no stateful API side effect or procedural MERGE requirement. Query constructs, row churn, and source types affect incremental eligibility.

## 2. Pharmaceutical data-engineering scenario

Manufacturing analysts need a curated near-real-time quality KPI without maintaining custom task orchestration.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE OR REPLACE DYNAMIC TABLE PHARMA_DEMO.MART.DT_PLANT_YIELD
  TARGET_LAG='10 minutes'
  WAREHOUSE=WH_PHARMA_ETL
  REFRESH_MODE=INCREMENTAL
AS
SELECT plant_id,product_id,
       SUM(units_produced) AS total_units,
       SUM(units_rejected) AS rejected_units
FROM PHARMA_DEMO.RAW.BATCH_EVENTS
WHERE status='COMPLETE'
GROUP BY plant_id,product_id;
SHOW DYNAMIC TABLES LIKE 'DT_PLANT_YIELD'
  IN SCHEMA PHARMA_DEMO.MART;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Monitor refresh history, actual lag, refresh errors, and cost; verify sums after a source backfill. Refresh creation may require supported incremental constructs.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Assuming a 10-minute target means refresh exactly every 10 minutes.
- Unsupported SQL means an explicit incremental refresh may fail creation.
- High churn or deep dependency chains can degrade lag and cost.

**Engineering decision:** Dynamic tables for declarative datasets; streams/tasks for imperative operations; materialized views for suitable acceleration on a base table.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Dynamic Tables and Target Lag**. The requirement is: Build declarative incremental transformations and understand refresh cost, modes, and freshness. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Compare a stream/task pipeline and a dynamic table with the same controlled data; measure freshness and credits.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply dynamic tables and target lag?**  
A: Manufacturing analysts need a curated near-real-time quality KPI without maintaining custom task orchestration.

**Q: What is the key implementation trade-off?**  
A: Dynamic tables for declarative datasets; streams/tasks for imperative operations; materialized views for suitable acceleration on a base table.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Target lag is a best-effort freshness goal, not a schedule.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/dynamic-tables/overview

---

**Navigation:** Previous: [Streams, Tasks, and Incremental CDC](02-streams-tasks.md) · Next: [MERGE, Deduplication, SCD Type 1 and 2](04-merge-scd.md) · [All medium topics](README.md)
