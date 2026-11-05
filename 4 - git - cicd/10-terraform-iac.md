# Terraform Infrastructure as Code

> **Level:** GIT-CICD · **Module 10/12** · **Goal:** Version Snowflake infrastructure safely and understand drift and state security.

## 1. Concept and mental model

- **Provider:** resource-specific API bindings and version compatibility; check supported Snowflake provider resource names.
- **Plan:** proposed change preview, not proof of data safety.
- **State:** sensitive record of managed resources; encrypt and limit access.
- **Drift:** changes outside IaC; detect before apply and choose authoritative owner.

**How to reason about it:** Illustration uses general Terraform commands; real Snowflake provider resources/arguments must match a pinned provider version and internal IAM standards.

## 2. Pharmaceutical data-engineering scenario

Onboarding a new synthetic manufacturing domain requires roles, a warehouse and schemas in DEV and PROD.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Standard Terraform operator workflow
terraform fmt -check
terraform init -lockfile=readonly
terraform validate
terraform plan -out=tfplan
# Approval required before apply:
terraform apply tfplan

# Design notes:
# - keep remote state encrypted and locked
# - pin provider versions in .terraform.lock.hcl
# - do not duplicate object ownership across SQL migrations and IaC
# - never store secret input values in source control
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Review plan for destructive operations, grant drift, role changes, state lock integrity, and environment target.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Running apply against the wrong Snowflake account.
- Unencrypted state storing sensitive configuration.
- Destroy/recreate unexpectedly invalidates dependent tasks/views.

**Engineering decision:** Use IaC for agreed infrastructure resources and versioned SQL migrations for schema/data evolution where that better preserves changes.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Terraform Infrastructure as Code**. The requirement is: Version Snowflake infrastructure safely and understand drift and state security. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write a Terraform change review checklist for adding a new ETL role and XSMALL warehouse with auto-suspend.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply terraform infrastructure as code?**  
A: Onboarding a new synthetic manufacturing domain requires roles, a warehouse and schemas in DEV and PROD.

**Q: What is the key implementation trade-off?**  
A: Use IaC for agreed infrastructure resources and versioned SQL migrations for schema/data evolution where that better preserves changes.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Plan describes intended resource change; tests and review prove safety.**

## 10. Official reference

- https://registry.terraform.io/providers/Snowflake-Labs/snowflake/latest/docs

---

**Navigation:** Previous: [Secrets, Workload Identity, and Least Privilege](09-secrets-iam-security.md) · Next: [Deployments, Promotion, and Rollback](11-deployment-rollback.md) · [All git-cicd topics](README.md)
