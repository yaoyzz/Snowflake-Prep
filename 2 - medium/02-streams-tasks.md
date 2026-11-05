# Streams, Tasks, and Incremental CDC

> **Level:** MEDIUM · **Module 02/10** · **Goal:** Construct an incremental transformation using change tracking and scheduled Snowflake tasks.

## 1. Concept and mental model

- **Stream:** records changes to a tracked object since its consumption offset; not a permanent audit-log substitute.
- **Task:** runs SQL or a procedure on a schedule or after predecessor tasks.
- **Transaction:** consuming stream rows in committed DML advances the offset; querying a stream alone does not.
- **Concurrency:** tasks and application-level retried writes still require idempotent target keys.

**How to reason about it:** Stage data in a raw append-only table and MERGE changes into a current-state table. This is a simplified insert-only stream example; updates/deletes require appropriate metadata handling.

## 2. Pharmaceutical data-engineering scenario

Manufacturing telemetry arrives in RAW; the CORE table should reflect the most recently received version of each batch.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE OR REPLACE TABLE PHARMA_DEMO.RAW.BATCH_CDC (
  batch_id VARCHAR, status VARCHAR, changed_at TIMESTAMP_NTZ
);
CREATE OR REPLACE STREAM PHARMA_DEMO.RAW.BATCH_CDC_STREAM
  ON TABLE PHARMA_DEMO.RAW.BATCH_CDC APPEND_ONLY=TRUE;
CREATE OR REPLACE TABLE PHARMA_DEMO.CORE.BATCH_CURRENT (
  batch_id VARCHAR,status VARCHAR,changed_at TIMESTAMP_NTZ
);
CREATE OR REPLACE TASK PHARMA_DEMO.CORE.APPLY_BATCH_CDC
  WAREHOUSE=WH_PHARMA_ETL SCHEDULE='5 MINUTES'
  WHEN SYSTEM$STREAM_HAS_DATA('PHARMA_DEMO.RAW.BATCH_CDC_STREAM')
AS
MERGE INTO PHARMA_DEMO.CORE.BATCH_CURRENT t
USING (
 SELECT batch_id,status,changed_at
 FROM PHARMA_DEMO.RAW.BATCH_CDC_STREAM
 QUALIFY ROW_NUMBER() OVER(PARTITION BY batch_id
                            ORDER BY changed_at DESC)=1
) s ON t.batch_id=s.batch_id
WHEN MATCHED AND s.changed_at>t.changed_at
  THEN UPDATE SET status=s.status,changed_at=s.changed_at
WHEN NOT MATCHED THEN INSERT (batch_id,status,changed_at)
  VALUES(s.batch_id,s.status,s.changed_at);
ALTER TASK PHARMA_DEMO.CORE.APPLY_BATCH_CDC RESUME;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Insert two versions of the same synthetic batch; execute task in DEV or wait for schedule; assert one target row with the latest timestamp.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Streams can become stale beyond change-tracking retention.
- Out-of-order records can overwrite the latest row without a version guard.
- A source stream with deletes needs metadata-aware logic; this example handles inserts only.

**Engineering decision:** Use Streams+Tasks for procedural CDC/MERGE; use Dynamic Tables when a supported SELECT fully expresses the result.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Streams, Tasks, and Incremental CDC**. The requirement is: Construct an incremental transformation using change tracking and scheduled Snowflake tasks. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Introduce update/delete events and design versioned tombstone handling; document replay from source snapshots.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply streams, tasks, and incremental cdc?**  
A: Manufacturing telemetry arrives in RAW; the CORE table should reflect the most recently received version of each batch.

**Q: What is the key implementation trade-off?**  
A: Use Streams+Tasks for procedural CDC/MERGE; use Dynamic Tables when a supported SELECT fully expresses the result.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **A stream is a change offset; a task is an execution trigger; MERGE establishes target state.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/streams-intro

---

**Navigation:** Previous: [Snowpipe File Ingestion](01-snowpipe.md) · Next: [Dynamic Tables and Target Lag](03-dynamic-tables.md) · [All medium topics](README.md)
