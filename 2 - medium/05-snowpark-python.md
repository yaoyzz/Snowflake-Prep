# Snowpark Python and Stored Procedures

> **Level:** MEDIUM · **Module 05/10** · **Goal:** Use Snowpark DataFrames for in-platform transformations and manage Python package/runtime choices.

## 1. Concept and mental model

- **Snowpark:** builds logical query plans and executes supported transformations in Snowflake.
- **DataFrame:** a lazy transformation graph until an action such as `collect()` or `save_as_table()`.
- **Python UDF:** scalar/vectorized computation within supported sandbox constraints.
- **Stored procedure:** encapsulates imperative tasks with managed permissions and versioned code.

**How to reason about it:** Prefer native SQL functions when possible; reserve Python for hard-to-express logic. Avoid `collect()` on full fact tables: it materializes data at the client/handler.

## 2. Pharmaceutical data-engineering scenario

A manufacturing quality team standardizes plant codes and calculates QC classification across millions of records.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```python
from snowflake.snowpark import Session
from snowflake.snowpark.functions import col, upper, trim, when, lit

# In a Snowflake notebook or managed procedure, obtain session appropriately.
def transform_batches(session: Session):
    df = session.table('PHARMA_DEMO.RAW.BATCH_EVENTS')
    clean = (df.with_column('PLANT_ID', upper(trim(col('PLANT_ID'))))
              .with_column('QUALITY_FLAG',
                  when(col('UNITS_REJECTED') > col('UNITS_PRODUCED'),
                       lit('INVALID')).otherwise(lit('CHECKED'))))
    return clean.select('BATCH_ID','PLANT_ID','QUALITY_FLAG')

# Example interactive usage after a real Session is available:
# result = transform_batches(session)
# result.show(10)
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Create a small fixture DataFrame, inspect generated results, and compare row counts and quality flags to an equivalent SQL query.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Collecting large DataFrames to local Python.
- Assuming every third-party package/network call is allowed in Snowflake execution contexts.
- UDF logic bypassing traceable SQL-based test coverage.

**Engineering decision:** Use native SQL for set transformations; Snowpark for Python-first integration and reusable logic; version handlers and dependencies.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Snowpark Python and Stored Procedures**. The requirement is: Use Snowpark DataFrames for in-platform transformations and manage Python package/runtime choices. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write a pytest-style unit test for `QUALITY_FLAG` and measure SQL versus Snowpark behavior on synthetic records.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply snowpark python and stored procedures?**  
A: A manufacturing quality team standardizes plant codes and calculates QC classification across millions of records.

**Q: What is the key implementation trade-off?**  
A: Use native SQL for set transformations; Snowpark for Python-first integration and reusable logic; version handlers and dependencies.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Snowpark expresses transformations in Python but executes supported plans inside Snowflake.**

## 10. Official reference

- https://docs.snowflake.com/en/developer-guide/snowpark/python/index

---

**Navigation:** Previous: [MERGE, Deduplication, SCD Type 1 and 2](04-merge-scd.md) · Next: [dbt Models, Tests, and Incremental Builds](06-dbt-data-modeling.md) · [All medium topics](README.md)
