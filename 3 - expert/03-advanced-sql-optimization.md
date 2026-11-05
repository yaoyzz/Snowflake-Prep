# Advanced SQL and Physical Query Optimization

> **Level:** EXPERT · **Module 03/12** · **Goal:** Interpret cost-based plans and optimize large-scale, high-concurrency Snowflake analytical workloads.

## 1. Concept and mental model

- **Pruning:** scans depend on micro-partition metadata and predicate selectivity.
- **Join strategies:** distribution and build/probe data size affect memory and network.
- **Spill:** memory pressure raises local/remote spill and latency; profile before warehouse resize.
- **Physical features:** clustering, search optimization and materialized views have maintenance cost and limitations.

**How to reason about it:** Measure workload distribution and use a representative query suite. Materialize common aggregates only when storage/refresh costs are justified. Apply selective filters before exploding semi-structured arrays.

## 2. Pharmaceutical data-engineering scenario

A multiyear QC event mart holds billions of assay readings with skew toward a small set of plants.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Create representative predicate and aggregation query.
SELECT plant_id,DATE_TRUNC('DAY',event_ts)::DATE AS day,
       SUM(units_rejected) rejected
FROM PHARMA_DEMO.RAW.BATCH_EVENTS
WHERE event_ts >= '2026-10-01'::TIMESTAMP_NTZ
  AND event_ts < '2026-11-01'::TIMESTAMP_NTZ
  AND plant_id IN ('PL-01','PL-02')
GROUP BY 1,2;
-- Analyze Query Profile in Snowsight, then compare with:
EXPLAIN USING TEXT
SELECT plant_id,SUM(units_rejected)
FROM PHARMA_DEMO.RAW.BATCH_EVENTS
WHERE plant_id='PL-01' GROUP BY plant_id;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Track partition pruning, scanned bytes, cardinality, spilled bytes, duration percentiles, and credits per successful query.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Mistaking cache-hit speedups for plan improvements.
- Over-clustering write-heavy small tables.
- Persisting intermediate copies without recovery/retention design.

**Engineering decision:** Fix logic/cardinality first; physical optimization second; compute sizing and concurrency management third.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Advanced SQL and Physical Query Optimization**. The requirement is: Interpret cost-based plans and optimize large-scale, high-concurrency Snowflake analytical workloads. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a benchmark matrix comparing selective date filters, plant filters, join cardinality, and clustering candidates.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply advanced sql and physical query optimization?**  
A: A multiyear QC event mart holds billions of assay readings with skew toward a small set of plants.

**Q: What is the key implementation trade-off?**  
A: Fix logic/cardinality first; physical optimization second; compute sizing and concurrency management third.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **The best optimization reduces correct work—not just elapsed time on one run.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/ui-query-profile

---

**Navigation:** Previous: [Real-Time Streaming and Kafka](02-real-time-streaming.md) · Next: [Advanced Governance, Privacy, and Lineage](04-advanced-governance.md) · [All expert topics](README.md)
