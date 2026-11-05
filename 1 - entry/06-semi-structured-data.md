# JSON and Semi-Structured Data

> **Level:** ENTRY · **Module 06/08** · **Goal:** Parse VARIANT payloads, flatten arrays, cast types, and validate schema evolution.

## 1. Concept and mental model

- **VARIANT:** holds semi-structured values with type information.
- **Path extraction:** `payload:key` and nested paths read JSON fields.
- **LATERAL FLATTEN:** explodes arrays while retaining parent context.
- **Schema-on-read:** flexible ingestion does not remove validation requirements.

**How to reason about it:** Store the original payload separately from curated typed columns. When schema changes, quarantine unknown mandatory fields and retain the raw source for replay.

## 2. Pharmaceutical data-engineering scenario

Each batch quality-control document contains repeated analyte measurements and a source revision number.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
WITH docs AS (
  SELECT PARSE_JSON('{"batch_id":"B-100","revision":2,
    "results":[{"analyte":"PURITY","value":99.2,"unit":"pct"},
               {"analyte":"PH","value":7.1,"unit":"pH"}]}') payload
)
SELECT payload:batch_id::STRING AS batch_id,
       payload:revision::INT AS source_revision,
       r.value:analyte::STRING AS analyte,
       r.value:value::NUMBER(10,2) AS result_value,
       r.value:unit::STRING AS result_unit
FROM docs, LATERAL FLATTEN(input=>payload:results) r;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Expected two measurements, one parent batch ID, revision 2; reject unexpected empty `results` or missing mandatory unit.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- A missing path produces NULL; this is not necessarily a valid measurement.
- Flattening multiple independent arrays multiplies row counts.
- Casting everything to STRING loses numerical validation.

**Engineering decision:** Keep immutable raw payloads with identifiers and ingest metadata; type and validate in Silver/Core.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **JSON and Semi-Structured Data**. The requirement is: Parse VARIANT payloads, flatten arrays, cast types, and validate schema evolution. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Add JSON with a missing unit and build a query that classifies valid vs. rejected analytes.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply json and semi-structured data?**  
A: Each batch quality-control document contains repeated analyte measurements and a source revision number.

**Q: What is the key implementation trade-off?**  
A: Keep immutable raw payloads with identifiers and ingest metadata; type and validate in Silver/Core.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **VARIANT accepts flexible input; curated schemas enforce contracts.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/semistructured-intro

---

**Navigation:** Previous: [Stages, File Formats, and COPY INTO](05-loading-stages.md) · Next: [Basic Dimensional Data Modeling](07-basic-data-modeling.md) · [All entry topics](README.md)
