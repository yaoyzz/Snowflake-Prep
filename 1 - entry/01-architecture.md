# Snowflake Architecture

> **Level:** ENTRY · **Module 01/08** · **Goal:** Explain the separation of storage, virtual warehouses, and Snowflake cloud services and choose compute for distinct workloads.

## 1. Concept and mental model

- **Database storage:** Snowflake manages columnar micro-partitions and metadata; users do not maintain indexes on regular tables.
- **Virtual warehouses:** independent compute clusters execute SQL; warehouse size, auto-suspend, and multi-cluster policies drive performance and cost.
- **Cloud services:** authentication, optimization, metadata, transaction coordination, and request management.
- **Isolation:** analysts and ingestion jobs can use separate warehouses without duplicating the underlying data.

**How to reason about it:** The warehouse does not own a copy of the tables. Increasing its size affects compute capacity, not the physical table size. A suspended warehouse resumes when a query needs compute; serverless features have their own billing model.

## 2. Pharmaceutical data-engineering scenario

R&D analysts query clinical study aggregates while a manufacturing team loads batch events. Isolate their compute to prevent contention; share governed data selectively.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Run with a role permitted to create warehouses.
CREATE WAREHOUSE IF NOT EXISTS WH_PHARMA_ETL
  WAREHOUSE_SIZE = 'XSMALL'
  AUTO_SUSPEND = 60 AUTO_RESUME = TRUE;
CREATE WAREHOUSE IF NOT EXISTS WH_PHARMA_BI
  WAREHOUSE_SIZE = 'XSMALL'
  AUTO_SUSPEND = 60 AUTO_RESUME = TRUE;
USE WAREHOUSE WH_PHARMA_ETL;
SELECT CURRENT_WAREHOUSE(), CURRENT_ROLE(), CURRENT_DATABASE();
SHOW WAREHOUSES LIKE 'WH_PHARMA_%';
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Confirm the session selected `WH_PHARMA_ETL`; inspect auto-suspend settings with `SHOW WAREHOUSES`.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Leaving warehouses running increases cost.
- Confusing larger warehouses with faster ingest from serverless Snowpipe.
- Using one shared warehouse for noisy ETL and interactive BI requests.

**Engineering decision:** Scale up for per-query resources; scale out with multi-cluster warehouses for concurrency (where supported and justified). Benchmark before increasing size.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Snowflake Architecture**. The requirement is: Explain the separation of storage, virtual warehouses, and Snowflake cloud services and choose compute for distinct workloads. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Design separate warehouses for batch loading, interactive analytics, and a scheduled machine learning feature build. Record isolation and monthly cost assumptions.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply snowflake architecture?**  
A: R&D analysts query clinical study aggregates while a manufacturing team loads batch events. Isolate their compute to prevent contention; share governed data selectively.

**Q: What is the key implementation trade-off?**  
A: Scale up for per-query resources; scale out with multi-cluster warehouses for concurrency (where supported and justified). Benchmark before increasing size.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Storage is shared; compute is isolated; cloud services coordinate.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/intro-key-concepts

---

**Navigation:** Previous: [Level README](README.md) · Next: [Snowflake Objects and RBAC](02-objects-rbac.md) · [All entry topics](README.md)
