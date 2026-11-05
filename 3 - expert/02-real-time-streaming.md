# Real-Time Streaming and Kafka

> **Level:** EXPERT · **Module 02/12** · **Goal:** Design low-latency event ingestion with high-performance Snowpipe Streaming and Kafka connector v4 considerations.

## 1. Concept and mental model

- **Snowpipe Streaming:** direct record ingestion via SDK/API or connector; high-performance architecture uses PIPE objects.
- **Elastic Channels:** simple scaling with at-least-once delivery, without ordering guarantees.
- **Named Channels:** more explicit channel/offset semantics when ordering and exactly-once are required and supported.
- **Connector v4:** migration from classic Kafka v3 is not drop-in; review offset and feature differences.

**How to reason about it:** Separate event time from ingest time, provide an immutable event ID, and deduplicate before KPI generation. For production, pin and validate connector releases and test replay, failover and device-clock skew.

## 2. Pharmaceutical data-engineering scenario

Manufacturing IoT sensors produce temperature/pressure signals; downstream QA needs near-real-time anomaly indicators.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Table DDL is executable; transport needs separately configured SDK/connector.
CREATE TABLE IF NOT EXISTS PHARMA_DEMO.RAW.SENSOR_EVENTS (
 event_id VARCHAR, device_id VARCHAR, batch_id VARCHAR,
 event_time TIMESTAMP_NTZ, ingested_at TIMESTAMP_LTZ,
 temperature_c FLOAT, payload VARIANT
);
-- Correct: deduplicate events *before* aggregation.
WITH dedup AS (
 SELECT * FROM PHARMA_DEMO.RAW.SENSOR_EVENTS
 QUALIFY ROW_NUMBER() OVER(PARTITION BY event_id
           ORDER BY ingested_at DESC)=1
)
SELECT batch_id,device_id,AVG(temperature_c) AS avg_temperature_c
FROM dedup GROUP BY batch_id,device_id;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Validate event counts, source Kafka offsets, duplicates, out-of-order arrival, max lag and replay after a forced connector restart.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Exactly-once transport promises do not guarantee exactly-once business outcomes.
- Connector upgrade breaks offset compatibility or changes schema behavior.
- Threshold checks on event ingestion time misrepresent delayed sensor events.

**Engineering decision:** Prefer high-performance streaming for low-latency records; use Kafka for durable fanout/replay when upstream platform requirements demand it.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Real-Time Streaming and Kafka**. The requirement is: Design low-latency event ingestion with high-performance Snowpipe Streaming and Kafka connector v4 considerations. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Build a design for 50k sensor events/sec with replay, dead-letter topic, duplicate keys, and a bounded-lateness policy.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply real-time streaming and kafka?**  
A: Manufacturing IoT sensors produce temperature/pressure signals; downstream QA needs near-real-time anomaly indicators.

**Q: What is the key implementation trade-off?**  
A: Prefer high-performance streaming for low-latency records; use Kafka for durable fanout/replay when upstream platform requirements demand it.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Reliable streaming requires event identity, ordering assumptions, replay strategy, and downstream deduplication.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/snowpipe-streaming/data-load-snowpipe-streaming-overview

---

**Navigation:** Previous: [Enterprise Pharmaceutical Data Platform Architecture](01-enterprise-architecture.md) · Next: [Advanced SQL and Physical Query Optimization](03-advanced-sql-optimization.md) · [All expert topics](README.md)
