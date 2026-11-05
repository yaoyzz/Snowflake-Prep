# MERGE, Deduplication, SCD Type 1 and 2

> **Level:** MEDIUM · **Module 04/10** · **Goal:** Implement deterministic upserts and choose historical-change strategies.

## 1. Concept and mental model

- **MERGE:** conditional matched update and unmatched insert, commonly used for SCD1.
- **SCD1:** overwrite old attributes without retaining history.
- **SCD2:** new row per attribute version with effective dates and current flag.
- **Determinism:** staging source must have at most one winning row per target key.

**How to reason about it:** Use SCD1 for corrections where old state is irrelevant. Use SCD2 when past qualification status must be reconstructed; its effective periods require careful timestamp and overlap checks.

## 2. Pharmaceutical data-engineering scenario

A qualified packaging supplier changes its preferred name and quality certification status; analysts need both current and historical states.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE OR REPLACE TABLE PHARMA_DEMO.CORE.SUPPLIER_CURRENT (
 supplier_id VARCHAR,name VARCHAR,qualification_status VARCHAR,
 updated_at TIMESTAMP_NTZ
);
MERGE INTO PHARMA_DEMO.CORE.SUPPLIER_CURRENT t
USING (
  SELECT * FROM VALUES
   ('SUP-7','Vendor Seven','QUALIFIED','2026-10-01'::TIMESTAMP_NTZ),
   ('SUP-7','Vendor Seven Ltd','SUSPENDED','2026-10-12'::TIMESTAMP_NTZ)
  AS v(supplier_id,name,qualification_status,updated_at)
  QUALIFY ROW_NUMBER() OVER(PARTITION BY supplier_id
                             ORDER BY updated_at DESC)=1
) s ON t.supplier_id=s.supplier_id
WHEN MATCHED AND s.updated_at>t.updated_at THEN
 UPDATE SET name=s.name,qualification_status=s.qualification_status,
            updated_at=s.updated_at
WHEN NOT MATCHED THEN INSERT
 (supplier_id,name,qualification_status,updated_at)
 VALUES(s.supplier_id,s.name,s.qualification_status,s.updated_at);
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Re-run the MERGE and verify unchanged row count and values. For SCD2, assert one active row and non-overlapping effective intervals per supplier.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Source duplicates cause nondeterminism or multiple matches.
- Ignoring late-arriving historical changes breaks SCD2 intervals.
- Using load timestamps instead of business-effective timestamps.

**Engineering decision:** Select business key plus source revision/order; do not synthesize random tie-breakers for high-stakes corrections.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **MERGE, Deduplication, SCD Type 1 and 2**. The requirement is: Implement deterministic upserts and choose historical-change strategies. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Extend the supplier model to SCD2; write test cases for same-time updates and retroactive status corrections.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply merge, deduplication, scd type 1 and 2?**  
A: A qualified packaging supplier changes its preferred name and quality certification status; analysts need both current and historical states.

**Q: What is the key implementation trade-off?**  
A: Select business key plus source revision/order; do not synthesize random tie-breakers for high-stakes corrections.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **MERGE is an upsert; SCD2 is a time-consistent history contract.**

## 10. Official reference

- https://docs.snowflake.com/en/sql-reference/sql/merge

---

**Navigation:** Previous: [Dynamic Tables and Target Lag](03-dynamic-tables.md) · Next: [Snowpark Python and Stored Procedures](05-snowpark-python.md) · [All medium topics](README.md)
