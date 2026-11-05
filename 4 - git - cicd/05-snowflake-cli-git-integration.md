# Snowflake CLI and Git Integration

> **Level:** GIT-CICD · **Module 05/12** · **Goal:** Operate Snowflake from versioned scripts and understand Snowflake-hosted Git repository clones.

## 1. Concept and mental model

- **Snowflake CLI:** `snow connection test`, `snow sql -f`, and project deployment commands.
- **Named connection:** local configuration independent of SQL content.
- **Snowflake Git integration:** Snowflake can fetch remote Git content into a repository object; distinct from local Git history.
- **Deploy:** CI must know which files, role, environment and approvals apply.

**How to reason about it:** Configure credentials outside tracked files. Snowflake-side Git repository objects are useful for reading versioned code in Snowflake but are not a replacement for GitHub PR/CI/CD governance.

## 2. Pharmaceutical data-engineering scenario

A batch-yield SQL model is tested locally, committed in GitHub, and promoted to a DEV Snowflake database.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Require a configured dev connection with least privilege.
snow --version
snow connection test -c dev
snow sql -q "SELECT CURRENT_ROLE(),CURRENT_WAREHOUSE()" -c dev
snow sql -f sql/migrations/001_create_schema.sql -c dev
# Snowflake-managed Git repository flow is configured separately:
# CREATE API INTEGRATION ... API_PROVIDER = git_https_api ...
# CREATE GIT REPOSITORY ... ORIGIN = 'https://...'
# ALTER GIT REPOSITORY ... FETCH;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Record exact CLI version, connection name, role, commit SHA, executed migration and Query IDs in the deployment log.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Interactive admin credentials used in automated pipelines.
- Snowflake Git repository FETCH assumed to trigger migrations.
- A local file edited without a commit is deployed in PROD.

**Engineering decision:** Prefer CI to deploy immutable Git commit artifacts using environment-scoped service identities.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Snowflake CLI and Git Integration**. The requirement is: Operate Snowflake from versioned scripts and understand Snowflake-hosted Git repository clones. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a DEV connection, confirm current role and test one harmless SELECT from a tracked .sql file.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply snowflake cli and git integration?**  
A: A batch-yield SQL model is tested locally, committed in GitHub, and promoted to a DEV Snowflake database.

**Q: What is the key implementation trade-off?**  
A: Prefer CI to deploy immutable Git commit artifacts using environment-scoped service identities.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Git versions code; Snowflake CLI executes a controlled version.**

## 10. Official reference

- https://docs.snowflake.com/en/developer-guide/snowflake-cli/sql/execute-sql

---

**Navigation:** Previous: [Data Engineering Repository Structure](04-repository-structure.md) · Next: [Database Migrations and Schema Versioning](06-database-migrations-versioning.md) · [All git-cicd topics](README.md)
