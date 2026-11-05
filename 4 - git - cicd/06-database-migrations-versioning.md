# Database Migrations and Schema Versioning

> **Level:** GIT-CICD · **Module 06/12** · **Goal:** Plan ordered, repeatable DDL changes and safe expand–migrate–contract releases.

## 1. Concept and mental model

- **Migration:** one deliberate state transition tracked by version/ID and checksum.
- **Expand–contract:** add backward-compatible structure; backfill; transition readers; remove old structure later.
- **Schema drift:** unexpected state divergence between Git declarations and live resources.
- **Rollback:** DDL rollback and data restoration are different problems; forward fixes may be safer.

**How to reason about it:** Add the field first, backfill from a trusted source, verify no ambiguous measurements, migrate readers, and only later enforce/remove old behavior after review.

## 2. Pharmaceutical data-engineering scenario

LIMS v2 introduces `measurement_unit` while downstream QC reports still consume v1 data.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- 003_add_assay_unit.sql (illustrative migration)
CREATE TABLE IF NOT EXISTS PHARMA_DEMO.CORE.LAB_RESULTS (
  sample_id VARCHAR,batch_id VARCHAR,
  result_value NUMBER(18,6)
);
ALTER TABLE PHARMA_DEMO.CORE.LAB_RESULTS
  ADD COLUMN IF NOT EXISTS measurement_unit VARCHAR;
-- Backfill from the authoritative source, not a guessed default:
-- UPDATE ... FROM governed mapping ...;
SELECT COUNT_IF(measurement_unit IS NULL) AS unfilled
FROM PHARMA_DEMO.CORE.LAB_RESULTS;
-- Block contraction until ambiguity is resolved.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Verify new column exists, historical unit coverage is complete, consumers work in both versions, and migration is recorded once.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Adding NOT NULL before backfill.
- Default unit incorrectly reinterprets historical results.
- Re-running an unsafe data migration duplicates rows.

**Engineering decision:** Choose versioned migrations for schema evolution; use Terraform for appropriate declarative infrastructure without competing object ownership.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Database Migrations and Schema Versioning**. The requirement is: Plan ordered, repeatable DDL changes and safe expand–migrate–contract releases. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Design a three-release expand/migrate/contract timeline for renaming a clinical site field.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply database migrations and schema versioning?**  
A: LIMS v2 introduces `measurement_unit` while downstream QC reports still consume v1 data.

**Q: What is the key implementation trade-off?**  
A: Choose versioned migrations for schema evolution; use Terraform for appropriate declarative infrastructure without competing object ownership.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Schema releases should be compatible, tested, and reversible where feasible.**

## 10. Official reference

- https://docs.snowflake.com/en/sql-reference/sql/alter-table-column

---

**Navigation:** Previous: [Snowflake CLI and Git Integration](05-snowflake-cli-git-integration.md) · Next: [GitHub Actions CI/CD for Snowflake](07-github-actions-cicd.md) · [All git-cicd topics](README.md)
