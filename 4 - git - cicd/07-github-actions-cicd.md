# GitHub Actions CI/CD for Snowflake

> **Level:** GIT-CICD · **Module 07/12** · **Goal:** Define test and deployment workflows using GitHub environments and Snowflake workload identity.

## 1. Concept and mental model

- **Triggers:** `pull_request` for CI and approved `push`/manual release for CD.
- **OIDC:** short-lived GitHub-issued identity can authenticate to Snowflake when Snowflake WIF is configured.
- **Environment protection:** approval and branch restrictions for regulated production deployments.
- **Immutable artifacts:** pin action versions, dependencies and exact Git SHA.

**How to reason about it:** Example is a production-shaped *template*, not a complete deployment: setup must include Snowflake WIF service user, mapped claims/roles, environment permissions, verified project files and tests.

## 2. Pharmaceutical data-engineering scenario

A reviewed manufacturing SQL change runs tests, then deploys to DEV and, after evidence approval, PROD.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```yaml
# .github/workflows/snowflake.yml
name: Snowflake validation
on:
  pull_request:
  workflow_dispatch:
permissions:
  contents: read
  id-token: write
jobs:
  validate:
    runs-on: ubuntu-latest
    environment: dev
    steps:
      - uses: actions/checkout@v4
        with:
          persist-credentials: false
      - uses: snowflakedb/snowflake-actions@v3
        with:
          use-oidc: true
          cli-version: '3.16'
      - name: Validate Snowflake connection
        env:
          SNOWFLAKE_ACCOUNT: ${{ secrets.SNOWFLAKE_ACCOUNT }}
        run: snow connection test -x
      - name: Validate checked-in SQL
        run: snow sql -f sql/tests/smoke.sql -x
# Add a separate protected release job for PROD; do not write to PROD
# on untrusted PRs. Pin exact verified SHAs in real organizations.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Run on a feature PR; prove least-privilege identity, protected environment, redacted logs, and failing tests block deployment.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- PR secrets accidentally exposed through privileged events.
- Unpinned action upgrades change runtime unexpectedly.
- Same service identity writes both DEV and PROD without controls.

**Engineering decision:** CI validates all PRs; CD promotes reviewed immutable commits through environment-protected jobs.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **GitHub Actions CI/CD for Snowflake**. The requirement is: Define test and deployment workflows using GitHub environments and Snowflake workload identity. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write a two-stage workflow with test artifacts, QA approval, protected PROD environment and post-deploy smoke tests.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply github actions ci/cd for snowflake?**  
A: A reviewed manufacturing SQL change runs tests, then deploys to DEV and, after evidence approval, PROD.

**Q: What is the key implementation trade-off?**  
A: CI validates all PRs; CD promotes reviewed immutable commits through environment-protected jobs.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **CI proves a change; CD promotes an approved version.**

## 10. Official reference

- https://docs.snowflake.com/en/developer-guide/snowflake-cli/cicd/github-action

---

**Navigation:** Previous: [Database Migrations and Schema Versioning](06-database-migrations-versioning.md) · Next: [Automated Tests and Deployment Quality Gates](08-automated-testing-quality-gates.md) · [All git-cicd topics](README.md)
