# Automated Tests and Deployment Quality Gates

> **Level:** GIT-CICD · **Module 08/12** · **Goal:** Define unit, integration, contract, reconciliation, security and regression gates for Snowflake changes.

## 1. Concept and mental model

- **Static tests:** format/lint SQL, compile Python and dbt.
- **Unit tests:** evaluate deterministic transformations on synthetic inputs.
- **Integration tests:** execute against ephemeral DEV schemas with a controlled Snowflake role.
- **Production gates:** reject unacceptable source/target discrepancies or unauthorized policy changes.

**How to reason about it:** Test logical units locally where possible, then validate warehouse semantics with Snowflake-backed fixtures. Production release evidence should include test versions and input snapshots.

## 2. Pharmaceutical data-engineering scenario

An AI assistant modifies yield calculation; tests must detect invalid division and double-counted reprocessed batches.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Snowflake SQL assertion: return rows only when violating the rule.
WITH yields AS (
 SELECT batch_id,units_produced,units_rejected
 FROM PHARMA_DEMO.RAW.BATCH_EVENTS
)
SELECT batch_id,'INVALID_YIELD_COUNTS' AS failure
FROM yields
WHERE units_produced IS NULL OR units_rejected IS NULL
   OR units_produced<0 OR units_rejected<0
   OR units_rejected>units_produced;
-- CI should explicitly FAIL when this query returns any rows;
-- running SELECT alone without checking results is insufficient.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Seed a passing fixture and three failing fixtures; assert CI exits nonzero on each failure rather than printing violations and proceeding.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Tests run against live regulated data instead of synthetic fixtures.
- Query succeeded but an assertion returned error rows.
- Schema permission tests use admin account and always pass.

**Engineering decision:** Use a risk-based test pyramid: static → unit → integration → security/DQ → approved release tests.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Automated Tests and Deployment Quality Gates**. The requirement is: Define unit, integration, contract, reconciliation, security and regression gates for Snowflake changes. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Build a small Python test harness that executes the validation query and raises an exception if any rows are returned.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply automated tests and deployment quality gates?**  
A: An AI assistant modifies yield calculation; tests must detect invalid division and double-counted reprocessed batches.

**Q: What is the key implementation trade-off?**  
A: Use a risk-based test pyramid: static → unit → integration → security/DQ → approved release tests.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **A gate must change deployment outcome, not just display results.**

## 10. Official reference

- https://docs.github.com/en/actions

---

**Navigation:** Previous: [GitHub Actions CI/CD for Snowflake](07-github-actions-cicd.md) · Next: [Secrets, Workload Identity, and Least Privilege](09-secrets-iam-security.md) · [All git-cicd topics](README.md)
