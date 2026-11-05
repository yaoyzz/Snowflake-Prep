# Platform Engineering and Infrastructure as Code

> **Level:** EXPERT · **Module 10/12** · **Goal:** Define enterprise templates for object lifecycle, environment promotion, identities and policy-as-code.

## 1. Concept and mental model

- **IaC:** version reviewable declarations for resources and drift management.
- **Database migrations:** procedural, ordered changes that may not be safely represented by replace-all DDL.
- **Service roles:** least-privilege identities for each deployment target.
- **Change boundaries:** infrastructure, code, schemas, data, and access policies have different rollback semantics.

**How to reason about it:** Provide a golden module/template with account permissions, environment conventions, naming, cost controls, logging and a deployment approval path.

## 2. Pharmaceutical data-engineering scenario

New manufacturing plants onboard a standard data product without copying ad hoc warehouse grants and tasks by hand.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Pseudocode pipeline mapping—not a complete deployable Terraform config.
# Git PR -> IaC plan -> human approval -> IaC apply
#        -> ordered SQL migration -> dbt build -> DQ verification
# Keep Terraform state in an encrypted, access-controlled backend.

# Example Snowflake CLI commands once connections are configured:
# snow connection test -c dev
# snow sql -f sql/001_bootstrap.sql -c dev
# snow sql -f sql/002_models.sql -c dev
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Check drift report, non-destructive preview, grant differences, service identity and post-deploy data tests before approving apply.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Terraform and SQL migration both own the same object.
- Destructive replacement loses metadata/history unexpectedly.
- Production changes bypass plan/approval evidence.

**Engineering decision:** Assign one authoritative owner per class of objects; separate mutable data and secrets from source-control declarations.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Platform Engineering and Infrastructure as Code**. The requirement is: Define enterprise templates for object lifecycle, environment promotion, identities and policy-as-code. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write an onboarding checklist for a new LIMS feed with owner, schema, IAM, retention, CI tests and cost budget.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply platform engineering and infrastructure as code?**  
A: New manufacturing plants onboard a standard data product without copying ad hoc warehouse grants and tasks by hand.

**Q: What is the key implementation trade-off?**  
A: Assign one authoritative owner per class of objects; separate mutable data and secrets from source-control declarations.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Platform templates encode standards; migrations encode change history.**

## 10. Official reference

- https://docs.snowflake.com/en/developer-guide/snowflake-cli/index

---

**Navigation:** Previous: [Cortex AI, Retrieval, and Snowflake ML](09-cortex-ai-ml.md) · Next: [FinOps, SLOs, and Incident Response](11-finops-observability.md) · [All expert topics](README.md)
