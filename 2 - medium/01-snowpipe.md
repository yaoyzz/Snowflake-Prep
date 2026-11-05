# Snowpipe File Ingestion

> **Level:** MEDIUM · **Module 01/10** · **Goal:** Configure asynchronous file-based loading and explain auto-ingest notifications, monitoring, and replay.

## 1. Concept and mental model

- **Snowpipe:** serverless, event-driven ingestion for staged files; distinct from direct-row Snowpipe Streaming.
- **Pipe:** stored `COPY INTO` definition used to ingest matching files.
- **AUTO_INGEST:** cloud-event notification integration must be provisioned separately.
- **Idempotency:** file history helps avoid duplicate loads, but content-level deduplication is still needed.

**How to reason about it:** Landing events go to RAW with file name and ingestion timestamp. For an S3-based system, coordinate an external stage/storage integration and the cloud notification channel with infrastructure teams.

## 2. Pharmaceutical data-engineering scenario

A clinical CRO exports site enrollment files hourly into a governed S3 prefix.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Prerequisite: provisioned S3 storage integration + stage + notification.
-- Example stage: @PHARMA_DEMO.RAW.CLINICAL_S3_STAGE
CREATE OR REPLACE TABLE PHARMA_DEMO.RAW.ENROLLMENT_LANDING (
  payload VARIANT,source_filename VARCHAR,load_ts TIMESTAMP_LTZ
);
CREATE OR REPLACE PIPE PHARMA_DEMO.RAW.ENROLLMENT_PIPE
  AUTO_INGEST=FALSE
AS
COPY INTO PHARMA_DEMO.RAW.ENROLLMENT_LANDING
  (payload,source_filename,load_ts)
FROM (SELECT $1,METADATA$FILENAME,CURRENT_TIMESTAMP()
      FROM @PHARMA_DEMO.RAW.CLINICAL_S3_STAGE)
FILE_FORMAT=(TYPE=JSON);
-- Turn AUTO_INGEST=TRUE only after configuring supported cloud events.
SELECT SYSTEM$PIPE_STATUS('PHARMA_DEMO.RAW.ENROLLMENT_PIPE');
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Monitor pipe status, ingestion latency, failed files, and count/control-total reconciliation against the source export manifest.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Pipe is created but notifications are not wired.
- Files arrive before stage permissions are valid.
- A successful ingestion message is mistaken for semantic validity.

**Engineering decision:** Use Snowpipe for files arriving continuously, `COPY INTO` for controlled bulk loads, and Snowpipe Streaming for direct row ingestion.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Snowpipe File Ingestion**. The requirement is: Configure asynchronous file-based loading and explain auto-ingest notifications, monitoring, and replay. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Design notification, replay, and failure quarantine for 10,000 small files/day; calculate expected serverless ingest cost.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply snowpipe file ingestion?**  
A: A clinical CRO exports site enrollment files hourly into a governed S3 prefix.

**Q: What is the key implementation trade-off?**  
A: Use Snowpipe for files arriving continuously, `COPY INTO` for controlled bulk loads, and Snowpipe Streaming for direct row ingestion.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Snowpipe watches staged files; Snowpipe Streaming ingests rows.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/data-load-snowpipe-intro

---

**Navigation:** Previous: [Level README](README.md) · Next: [Streams, Tasks, and Incremental CDC](02-streams-tasks.md) · [All medium topics](README.md)
