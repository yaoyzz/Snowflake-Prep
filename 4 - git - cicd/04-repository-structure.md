# Data Engineering Repository Structure

> **Level:** GIT-CICD · **Module 04/12** · **Goal:** Organize SQL, Python, dbt, test fixtures, environments, documentation and pipeline assets for maintainability.

## 1. Concept and mental model

- **One source of truth:** do not independently edit deployed SQL copies in multiple folders.
- **Environment separation:** parameterize connection, role and database, not core business definitions.
- **Ownership:** domain folder and PR rules identify accountable reviewers.
- **Artifacts:** generated binaries, logs, secrets, and raw datasets do not belong in ordinary Git history.

**How to reason about it:** A separate deployable repo (versus this learning repo) could use the following layout. Avoid committing real clinical/patient records; use sanitized fixtures.

## 2. Pharmaceutical data-engineering scenario

The manufacturing team needs repeatable onboarding for a second plant without copy-paste SQL drift.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```text
# Example deployable-project structure
pharma-data-platform/
  .github/workflows/ci.yml
  .github/workflows/deploy.yml
  sql/migrations/001_create_schema.sql
  sql/marts/fct_batch_yield.sql
  dbt/models/staging/stg_batch_events.sql
  dbt/models/marts/fct_batch_yield.sql
  python/quality_checks.py
  tests/fixtures/synthetic_batches.csv
  tests/test_quality_checks.py
  infra/terraform/
  docs/runbooks/
  README.md
  .gitignore
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Run a repository inventory and ensure every production object has one source file, owner, tests and deploy path.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- DDL defined in both Terraform and an independent migration.
- Multiple hard-coded PROD account identifiers.
- Credentials, regulated extracts, or `.env` committed.

**Engineering decision:** Separate deployable source from generated evidence while retaining references to approved run artifacts in a secure artifact store.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Data Engineering Repository Structure**. The requirement is: Organize SQL, Python, dbt, test fixtures, environments, documentation and pipeline assets for maintainability. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a CODEOWNERS file, naming conventions and example environment variable matrix for this structure.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply data engineering repository structure?**  
A: The manufacturing team needs repeatable onboarding for a second plant without copy-paste SQL drift.

**Q: What is the key implementation trade-off?**  
A: Separate deployable source from generated evidence while retaining references to approved run artifacts in a secure artifact store.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **A good repo makes ownership, dependencies, and deployment unambiguous.**

## 10. Official reference

- https://docs.github.com/en/repositories

---

**Navigation:** Previous: [GitHub Pull Requests and Code Review](03-github-pr-code-review.md) · Next: [Snowflake CLI and Git Integration](05-snowflake-cli-git-integration.md) · [All git-cicd topics](README.md)
