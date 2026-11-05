# Data Types and Table Design

> **Level:** ENTRY · **Module 04/08** · **Goal:** Define stable data contracts using DATE/TIMESTAMP/NUMBER/VARCHAR/BOOLEAN and appropriate table semantics.

## 1. Concept and mental model

- **NUMBER(p,s):** exact fixed-point values, preferred for counts and decimal measurements.
- **TIMESTAMP_NTZ/TZ/LTZ:** choose based on timestamp meaning and required timezone handling.
- **Regular/transient/temporary tables:** differ in lifetime and recovery features.
- **Constraints:** Snowflake standard tables do not enforce most primary/foreign key constraints; validate uniqueness in pipelines.

**How to reason about it:** Prefer source-system identifiers and explicit units to vague fields such as `value`. For regulated analytics, capture source file, ingestion time, and data version; do not replace system-of-record audit logs.

## 2. Pharmaceutical data-engineering scenario

Production batch events must preserve batch identity, units, equipment timestamp, and a traceable load timestamp.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE DATABASE IF NOT EXISTS PHARMA_DEMO;
CREATE SCHEMA IF NOT EXISTS PHARMA_DEMO.RAW;
CREATE SCHEMA IF NOT EXISTS PHARMA_DEMO.CORE;
CREATE SCHEMA IF NOT EXISTS PHARMA_DEMO.MART;
CREATE SCHEMA IF NOT EXISTS PHARMA_DEMO.OPS;
CREATE TABLE IF NOT EXISTS PHARMA_DEMO.RAW.BATCH_EVENTS (
  batch_id VARCHAR, plant_id VARCHAR, product_id VARCHAR,
  event_ts TIMESTAMP_NTZ, status VARCHAR,
  units_produced NUMBER(18,0), units_rejected NUMBER(18,0),
  source_updated_at TIMESTAMP_NTZ,
  ingested_at TIMESTAMP_LTZ DEFAULT CURRENT_TIMESTAMP()
);
INSERT INTO PHARMA_DEMO.RAW.BATCH_EVENTS
(batch_id,plant_id,product_id,event_ts,status,units_produced,units_rejected,source_updated_at)
SELECT 'B-100','PL-01','DRUG-A','2026-10-10 08:00'::TIMESTAMP_NTZ,
       'COMPLETE',1000,12,'2026-10-10 09:00'::TIMESTAMP_NTZ;
SELECT * FROM PHARMA_DEMO.RAW.BATCH_EVENTS;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Assert nonnegative units, populated batch keys, and expected precision. Re-running INSERT intentionally creates a duplicate: use a load manifest or MERGE in production.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Assuming PK declarations always enforce uniqueness.
- Mixing UTC events with local timestamps without timezone metadata.
- Using FLOAT for exact quantities or financial values.

**Engineering decision:** Select retention/table type from the recovery requirement; document whether each timestamp is event, source modification, or ingestion time.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Data Types and Table Design**. The requirement is: Define stable data contracts using DATE/TIMESTAMP/NUMBER/VARCHAR/BOOLEAN and appropriate table semantics. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Add a synthetic QC table with measurement value, unit, result status, assay version, and batch foreign key; define five data contract checks.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply data types and table design?**  
A: Production batch events must preserve batch identity, units, equipment timestamp, and a traceable load timestamp.

**Q: What is the key implementation trade-off?**  
A: Select retention/table type from the recovery requirement; document whether each timestamp is event, source modification, or ingestion time.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Choose types for meaning and recovery, not just storage.**

## 10. Official reference

- https://docs.snowflake.com/en/sql-reference/data-types

---

**Navigation:** Previous: [SQL Essentials for Data Engineering](03-sql-essentials.md) · Next: [Stages, File Formats, and COPY INTO](05-loading-stages.md) · [All entry topics](README.md)
