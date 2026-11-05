# Apache Iceberg and Lakehouse Interoperability

> **Level:** EXPERT · **Module 07/12** · **Goal:** Evaluate Iceberg table architecture, catalog ownership, and cross-engine data access.

## 1. Concept and mental model

- **Iceberg:** open table format representing snapshots, manifests and table metadata on object storage.
- **Catalog:** coordinates table metadata; distinguish Snowflake-managed from externally managed setups.
- **Interoperability:** Spark/Databricks/Flink compatibility depends on catalog and writer coordination.
- **Compaction/rewrite:** performance depends on file layout, metadata health and safe concurrent writes.

**How to reason about it:** Select a catalog-of-record and assign write ownership. Explicitly document who maintains compaction, snapshots, schema evolution, permissions and retention; avoid uncontrolled multi-engine writers.

## 2. Pharmaceutical data-engineering scenario

R&D scientists use Spark for feature engineering while Snowflake analysts query a shared, governed research dataset.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Discovery and query pattern; actual Iceberg table DDL depends on
-- catalog, external volume, permissions, and supported deployment.
SELECT CURRENT_VERSION();
SELECT COUNT(*) AS batches
FROM PHARMA_DEMO.RAW.BATCH_EVENTS;
-- Pseudocode design:
-- data -> governed object storage -> Iceberg snapshots -> catalog
--    -> authorized Snowflake/Spark read engines
-- Compare snapshot ID and row counts across engines.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Reconcile snapshot identifiers, row count, schema, read permissions and stale metadata behavior between two engines.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Multiple writers without clear concurrency rules.
- Mixing Iceberg and Snowflake-native recovery assumptions.
- Assuming open file format implies unrestricted governed access.

**Engineering decision:** Prefer native tables for Snowflake-centric workloads; consider Iceberg for supported multiengine access and portable table storage.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Apache Iceberg and Lakehouse Interoperability**. The requirement is: Evaluate Iceberg table architecture, catalog ownership, and cross-engine data access. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write an architecture decision record comparing Snowflake-native, Snowflake-managed Iceberg and external-catalog Iceberg.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply apache iceberg and lakehouse interoperability?**  
A: R&D scientists use Spark for feature engineering while Snowflake analysts query a shared, governed research dataset.

**Q: What is the key implementation trade-off?**  
A: Prefer native tables for Snowflake-centric workloads; consider Iceberg for supported multiengine access and portable table storage.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Iceberg makes table format portable; a catalog and governance make it operable.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/tables-iceberg

---

**Navigation:** Previous: [Resilience, Recovery, and Disaster Readiness](06-resilience-recovery.md) · Next: [Airflow and Cross-Platform Orchestration](08-advanced-orchestration.md) · [All expert topics](README.md)
