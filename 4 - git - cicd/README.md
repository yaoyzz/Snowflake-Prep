# GIT-CICD — Version-control and safely release Snowflake systems

Operate Git, GitHub PR, automated tests, OIDC deployments, IaC and rollback. **12 learning modules + this README.** All examples assume an independently provisioned Snowflake sandbox and use fictitious data.

## Learning path

| # | Markdown file | Outcome |
|---:|---|---|
| 01 | [Git Fundamentals and Repository Setup](01-git-fundamentals.md) | Use Git confidently from an existing working directory on Windows, Linux and macOS. |
| 02 | [Branches, Merging, Rebase, and Conflict Resolution](02-git-branching-merging.md) | Deliver changes in small branches and resolve conflicts without rewriting shared history. |
| 03 | [GitHub Pull Requests and Code Review](03-github-pr-code-review.md) | Run an auditable PR process including review ownership, protected branches and checks. |
| 04 | [Data Engineering Repository Structure](04-repository-structure.md) | Organize SQL, Python, dbt, test fixtures, environments, documentation and pipeline assets for maintainability. |
| 05 | [Snowflake CLI and Git Integration](05-snowflake-cli-git-integration.md) | Operate Snowflake from versioned scripts and understand Snowflake-hosted Git repository clones. |
| 06 | [Database Migrations and Schema Versioning](06-database-migrations-versioning.md) | Plan ordered, repeatable DDL changes and safe expand–migrate–contract releases. |
| 07 | [GitHub Actions CI/CD for Snowflake](07-github-actions-cicd.md) | Define test and deployment workflows using GitHub environments and Snowflake workload identity. |
| 08 | [Automated Tests and Deployment Quality Gates](08-automated-testing-quality-gates.md) | Define unit, integration, contract, reconciliation, security and regression gates for Snowflake changes. |
| 09 | [Secrets, Workload Identity, and Least Privilege](09-secrets-iam-security.md) | Authenticate automated pipelines securely with minimal standing privilege. |
| 10 | [Terraform Infrastructure as Code](10-terraform-iac.md) | Version Snowflake infrastructure safely and understand drift and state security. |
| 11 | [Deployments, Promotion, and Rollback](11-deployment-rollback.md) | Promote changes through isolated environments and prepare recovery for code, schema, and data. |
| 12 | [AI-Assisted Git, CI/CD, and DevOps](12-ai-assisted-devops.md) | Use coding agents to create reproducible PRs, tests and troubleshooting reports without enabling unsafe autonomous releases. |

## Shared fictional laboratory

All companies, source feeds, people and sample records in this repository are **hypothetical pharma-inspired learning examples**, not claims about pharma's internal systems. Use synthetic or specifically approved datasets only. The consistent sandbox namespace is `PHARMA_DEMO`, with `RAW` (landing), `CORE` (validated), `MART` (consumption), and `OPS` (operational records). Some examples use additional domain schemas.

A minimal sandbox can be initialized from [`entry/04-data-types-tables.md`](../entry/04-data-types-tables.md). For role/warehouse prerequisites see [`entry/01-architecture.md`](../entry/01-architecture.md) and [`entry/02-objects-rbac.md`](../entry/02-objects-rbac.md). Statements may create billable resources or require privileges; execute only in an authorized disposable development environment. External services, cloud integrations and tools such as dbt/Airflow/Terraform must be set up separately.

**Business definition:** illustrative manufacturing yield = `(SUM(units_produced) - SUM(units_rejected)) / SUM(units_produced)`. Actual yield and release rules depend on approved source definitions, product specifications, batch lifecycle and QA decisions.


### Local prerequisites and quick Git repair

Install Git, a current editor, Python where needed, and the current Snowflake CLI. On Windows, PowerShell and Git Bash both work. To start from an existing hosted repository, use **`git clone`**, not `git pull` inside an arbitrary folder:

```bash
git clone https://github.com/OWNER/Snowflake-Prep.git
cd Snowflake-Prep
git status
git switch -c feature/quality-check
```

Replace `OWNER` with a valid GitHub organization/user and authenticate appropriately. To verify Snowflake CLI after approved connection configuration:

```bash
snow --version
snow connection test -c dev
```

For a deployable application, keep CI workflows in `.github/workflows/` and version-controlled SQL/dbt/Python outside this conceptual learning repo. **Do not commit credentials or regulated data.**

**Interview checkpoint:** Explain `clone` vs. `pull`, PR review gates, OIDC WIF, idempotent migrations, and why reverting Git code is not the same as restoring modified Snowflake data.


## Capstone — Protected PR → CI → controlled Snowflake deployment

1. Clone Git repository and create a feature branch modifying an additive schema migration.
2. Require a PR, reviewer, lint/compile, synthetic SQL/pytest/dbt tests and secret scan.
3. Authenticate using configured GitHub OIDC/WIF and environment-scoped Snowflake role.
4. Promote a pinned commit DEV → TEST → PROD via approval, with release manifest and recovery plan.

### Definition of done

- [ ] Invalid test causes nonzero CI status and blocks release.
- [ ] Fork PR cannot receive production identity.
- [ ] Migration is repeatable and downstream-compatible.
- [ ] Rollback/roll-forward includes data reconciliation.

## How to study efficiently

Read the mental model, run the focused example, validate it using a tiny synthetic fixture, introduce one deliberate failure, and explain the trade-off without looking at the text. Use the AI prompt inside each chapter to generate test variants, then independently verify the result. Record what failed and why.

## Quality and compliance boundary

This repository is a learning resource, **not a validated pharma system** and not a source of clinical or manufacturing release rules. Never use real patient data, proprietary company extracts, credentials or unapproved model endpoints in exercises. For GxP-relevant uses, involve system owners, security and Quality to assess applicability, computer-system validation, electronic-record controls, retention and audit evidence. Cloud product features alone do not confer compliance.

## Sources and freshness

Primary sources are the official Snowflake docs linked from each module, plus official Git/GitHub, dbt, Airflow, Terraform and FDA references where applicable. Snowflake features and CLI syntax can change; verify your account's region/edition and installed tool versions before applying production examples. Content prepared November 2026.

## Other learning levels

- [Entry](../entry/README.md)
- [Medium](../medium/README.md)
- [Expert](../expert/README.md)
- [Git / CI/CD](../git-cicd/README.md)
