# Stages, File Formats, and COPY INTO

> **Level:** ENTRY · **Module 05/08** · **Goal:** Load CSV/JSON into Snowflake using stages and COPY INTO, including validation and error handling.

## 1. Concept and mental model

- **Stage:** a named or table/user storage location from which loading reads files.
- **File format:** delimited text, JSON, Parquet, etc.; specifies parsing rules.
- **COPY INTO:** file-based bulk loading with load history to avoid accidental reprocessing in normal usage.
- **VALIDATION_MODE:** dry-run error inspection for supported COPY load patterns; do not blindly use `ON_ERROR=CONTINUE` in regulated workloads.

**How to reason about it:** In a sandbox use an internal stage; in production prefer governed external storage integrations, notifications, and file manifests. `PUT` is supported from SnowSQL/appropriate client environments, not worksheet SQL.

## 2. Pharmaceutical data-engineering scenario

A laboratory system drops a nightly CSV with batch release measurements into governed cloud storage.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE OR REPLACE FILE FORMAT PHARMA_DEMO.RAW.FF_BATCH_CSV
  TYPE=CSV SKIP_HEADER=1 FIELD_OPTIONALLY_ENCLOSED_BY='"'
  NULL_IF=('','NULL');
CREATE STAGE IF NOT EXISTS PHARMA_DEMO.RAW.BATCH_STAGE
  FILE_FORMAT=PHARMA_DEMO.RAW.FF_BATCH_CSV;
-- Upload a matching CSV through Snowflake CLI:
-- snow stage copy ./sample_batch.csv @PHARMA_DEMO.RAW.BATCH_STAGE
COPY INTO PHARMA_DEMO.RAW.BATCH_EVENTS
  (batch_id,plant_id,product_id,event_ts,status,
   units_produced,units_rejected,source_updated_at)
FROM (SELECT $1,$2,$3,TRY_TO_TIMESTAMP_NTZ($4),$5,
             TRY_TO_NUMBER($6),TRY_TO_NUMBER($7),
             TRY_TO_TIMESTAMP_NTZ($8)
      FROM @PHARMA_DEMO.RAW.BATCH_STAGE)
FILE_FORMAT=(FORMAT_NAME=PHARMA_DEMO.RAW.FF_BATCH_CSV)
ON_ERROR=ABORT_STATEMENT;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Check COPY status, rows loaded, rejects, and `COPY_HISTORY`; compare file counts and batch-level control totals with the source manifest.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Changing file content under the same path can complicate replay.
- `TRY_TO_NUMBER` silently yields NULL for invalid input unless DQ catches it.
- Client-only file commands cannot be run as ordinary Snowsight worksheet SQL.

**Engineering decision:** Use an atomic reject-and-quarantine policy for critical files; include source filename, checksum, and ingestion run ID.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Stages, File Formats, and COPY INTO**. The requirement is: Load CSV/JSON into Snowflake using stages and COPY INTO, including validation and error handling. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a two-row CSV that includes a malformed quantity; observe parsing and implement a quarantine/rejection check.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply stages, file formats, and copy into?**  
A: A laboratory system drops a nightly CSV with batch release measurements into governed cloud storage.

**Q: What is the key implementation trade-off?**  
A: Use an atomic reject-and-quarantine policy for critical files; include source filename, checksum, and ingestion run ID.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Stage → parse → load → reconcile; never treat a successful COPY as data-quality proof.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/data-load-overview

---

**Navigation:** Previous: [Data Types and Table Design](04-data-types-tables.md) · Next: [JSON and Semi-Structured Data](06-semi-structured-data.md) · [All entry topics](README.md)
