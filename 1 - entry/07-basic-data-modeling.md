# Basic Dimensional Data Modeling

> **Level:** ENTRY · **Module 07/08** · **Goal:** Design a batch-oriented star schema and understand source grain versus reporting grain.

## 1. Concept and mental model

- **Fact table:** events or measurements at a declared grain (for example one batch release).
- **Dimension:** descriptive attributes such as product, plant, and calendar.
- **Surrogate key:** decouples warehouse dimension identity from mutable source codes.
- **Bronze/Silver/Gold:** organizational pattern, not Snowflake-native storage tiers.

**How to reason about it:** A fact must be defined by a unique business event or snapshot grain. Store additive counts in facts and compute ratios at query time to avoid taking unweighted averages of percentages.

## 2. Pharmaceutical data-engineering scenario

Finance and manufacturing leaders need daily yield by plant and product across many source systems.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE OR REPLACE VIEW PHARMA_DEMO.MART.V_BATCH_YIELD AS
WITH current_batches AS (
  SELECT * FROM PHARMA_DEMO.RAW.BATCH_EVENTS
  QUALIFY ROW_NUMBER() OVER (
    PARTITION BY batch_id ORDER BY source_updated_at DESC,ingested_at DESC
  )=1
)
SELECT DATE_TRUNC('DAY',event_ts)::DATE AS production_date,
       plant_id,product_id,
       SUM(units_produced) AS units_produced,
       SUM(units_rejected) AS units_rejected,
       DIV0(SUM(units_produced)-SUM(units_rejected),
            SUM(units_produced)) AS yield_rate
FROM current_batches
WHERE status='COMPLETE'
GROUP BY 1,2,3;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Compare `SUM(units_rejected)` to source totals and ensure `0 <= yield_rate <= 1` when rejected counts cannot exceed production.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Fact grain not documented: same batch can be counted repeatedly.
- Averaging batch yield percentages rather than weighting by units.
- Mixing slowly changing dimensions without effective-date joins.

**Engineering decision:** Choose event fact for detailed analysis and an aggregate mart for consumption; create versioned dimensions when historical attribute changes matter.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Basic Dimensional Data Modeling**. The requirement is: Design a batch-oriented star schema and understand source grain versus reporting grain. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Sketch `FACT_BATCH_RELEASE`, `DIM_PRODUCT`, and `DIM_PLANT` with business keys, surrogate keys, and intended grains.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply basic dimensional data modeling?**  
A: Finance and manufacturing leaders need daily yield by plant and product across many source systems.

**Q: What is the key implementation trade-off?**  
A: Choose event fact for detailed analysis and an aggregate mart for consumption; create versioned dimensions when historical attribute changes matter.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Declare the grain first; ratios come from additive numerators and denominators.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/tables-clustering-micropartitions

---

**Navigation:** Previous: [JSON and Semi-Structured Data](06-semi-structured-data.md) · Next: [Query History, Time Travel, and Basic Operations](08-basic-operations.md) · [All entry topics](README.md)
