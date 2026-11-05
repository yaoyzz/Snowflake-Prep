# SQL Essentials for Data Engineering

> **Level:** ENTRY · **Module 03/08** · **Goal:** Apply joins, window functions, CTEs, QUALIFY, and safe aggregation to operational datasets.

## 1. Concept and mental model

- **CTE:** a readable named query subexpression, not an automatic persistence boundary.
- **Window function:** computes rank/lag/aggregates across a partition without collapsing rows.
- **QUALIFY:** filters after window functions; useful for latest-per-key deduplication.
- **NULL:** `COUNT(col)` ignores nulls; joins with nullable keys need explicit semantics.

**How to reason about it:** Deduplicate *before* computing enrollment counts. Confirm that the timestamp and tie-breaker represent source truth; append-only ingestion timestamps are not always equivalent to business-effective time.

## 2. Pharmaceutical data-engineering scenario

An enrollment feed emits corrections per study site. Keep the latest status per synthetic subject token and calculate current active counts.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
WITH enrollment AS (
  SELECT * FROM VALUES
    ('S01','NY1','T001','SCREENED','2026-10-01'::DATE,1),
    ('S01','NY1','T001','ENROLLED','2026-10-03'::DATE,2),
    ('S01','CA1','T002','ENROLLED','2026-10-03'::DATE,1)
    AS t(study_id,site_id,subject_token,status,updated_at,version)
), latest AS (
  SELECT * FROM enrollment
  QUALIFY ROW_NUMBER() OVER (
    PARTITION BY study_id,subject_token
    ORDER BY updated_at DESC,version DESC
  ) = 1
)
SELECT study_id,site_id,COUNT_IF(status='ENROLLED') AS enrolled
FROM latest GROUP BY study_id,site_id ORDER BY study_id,site_id;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Expected: NY1=1, CA1=1. Check total enrolled = 2 and unique synthetic subject keys = 2.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Joining facts to non-unique dimensions creates fanout.
- Ranking only by date leads to nondeterministic ties.
- Using `NOT IN` with nullable values can produce surprises.

**Engineering decision:** Use `QUALIFY` for latest-per-key filtering, `GROUP BY` for true aggregation, and `COUNT_IF` for conditional event counts.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **SQL Essentials for Data Engineering**. The requirement is: Apply joins, window functions, CTEs, QUALIFY, and safe aggregation to operational datasets. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Rewrite the example using `LAG(status)` to identify actual status transitions, not repeated messages.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply sql essentials for data engineering?**  
A: An enrollment feed emits corrections per study site. Keep the latest status per synthetic subject token and calculate current active counts.

**Q: What is the key implementation trade-off?**  
A: Use `QUALIFY` for latest-per-key filtering, `GROUP BY` for true aggregation, and `COUNT_IF` for conditional event counts.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Deduplicate at the correct grain, then aggregate.**

## 10. Official reference

- https://docs.snowflake.com/en/sql-reference/constructs/qualify

---

**Navigation:** Previous: [Snowflake Objects and RBAC](02-objects-rbac.md) · Next: [Data Types and Table Design](04-data-types-tables.md) · [All entry topics](README.md)
