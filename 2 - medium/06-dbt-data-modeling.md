# dbt Models, Tests, and Incremental Builds

> **Level:** MEDIUM · **Module 06/10** · **Goal:** Manage modular, versioned SQL transformations with dbt and test contracts.

## 1. Concept and mental model

- **ref():** expresses model dependencies and creates a DAG.
- **source():** declares upstream tables and supports source freshness checks.
- **Materializations:** view, table, incremental, ephemeral (semantics differ).
- **Tests:** unique/not_null/accepted_values/relationships plus custom SQL business assertions.

**How to reason about it:** Each dbt model is deployed to environment-specific namespaces. Use immutable source contracts and tests for business keys, not just “query runs successfully.”

## 2. Pharmaceutical data-engineering scenario

Clinical and manufacturing teams require repeatable Silver/Gold transformations reviewed in Git.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- models/marts/fct_batch_yield.sql
{{ config(materialized='table') }}
WITH batches AS (
  SELECT * FROM {{ source('pharma_raw', 'batch_events') }}
), latest AS (
  SELECT * FROM batches
  QUALIFY ROW_NUMBER() OVER (
    PARTITION BY batch_id ORDER BY source_updated_at DESC
  ) = 1
)
SELECT plant_id,product_id,
  SUM(units_produced) total_units,
  SUM(units_rejected) rejected_units
FROM latest WHERE status='COMPLETE'
GROUP BY 1,2;

-- models/marts/schema.yml (separate file in a real dbt project)
-- models:
--   - name: fct_batch_yield
--     columns:
--       - name: plant_id
--         data_tests: [not_null]
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Run `dbt parse`, `dbt build --select fct_batch_yield+`; validate generated lineage and compare outputs to source control totals.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Incremental strategies that miss late-arriving records.
- Overwriting production models without controlled migrations.
- Treating generic tests as adequate GxP validation evidence.

**Engineering decision:** Use `ref` DAGs for SQL modeling, orchestrators for cross-platform dependencies, and explicit data contracts for source semantics.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **dbt Models, Tests, and Incremental Builds**. The requirement is: Manage modular, versioned SQL transformations with dbt and test contracts. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Implement dbt sources, models, tests and a freshness check for the synthetic batch dataset.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply dbt models, tests, and incremental builds?**  
A: Clinical and manufacturing teams require repeatable Silver/Gold transformations reviewed in Git.

**Q: What is the key implementation trade-off?**  
A: Use `ref` DAGs for SQL modeling, orchestrators for cross-platform dependencies, and explicit data contracts for source semantics.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **dbt versions transformation logic; tests and release controls make it reliable.**

## 10. Official reference

- https://docs.getdbt.com/docs/build/models

---

**Navigation:** Previous: [Snowpark Python and Stored Procedures](05-snowpark-python.md) · Next: [Data Quality and Reconciliation](07-data-quality.md) · [All medium topics](README.md)
