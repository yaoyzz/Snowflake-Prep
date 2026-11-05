# Secrets, Workload Identity, and Least Privilege

> **Level:** GIT-CICD · **Module 09/12** · **Goal:** Authenticate automated pipelines securely with minimal standing privilege.

## 1. Concept and mental model

- **WIF/OIDC:** trust short-lived GitHub workload identities bound to the expected repository/environment claims.
- **Key-pair auth:** fallback where federation is unavailable; rotate and store in approved secret manager.
- **GitHub environments:** approval and scope guard production tokens.
- **Least privilege:** CI reads schema and test fixtures; CD mutates approved objects using separate identities.

**How to reason about it:** Bind trust narrowly to repository, workflow, environment and audience; verify your provider and Snowflake support exact OIDC claims. Avoid printing tokens in logs.

## 2. Pharmaceutical data-engineering scenario

A GitHub Actions deployment needs to create a manufacturing mart but must not have access to clinical direct identifiers.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```yaml
# Recommended workflow permissions (YAML fragment):
permissions:
  contents: read
  id-token: write

# For Snowflake CLI: use WIF/OIDC-backed auth configured by security.
# Snowflake-side example requires administrator provisioning and
# actual issuer/subject from the organization's GitHub identity policy.
# Never commit private keys, tokens, passwords, config.toml credentials.

# Local checks:
git grep -n -i -E 'password|private.key|secret' -- ':!git-cicd/*' || true
# This grep is not a substitute for dedicated secret scanning.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Prove the identity can deploy its own mart and cannot select a restricted clinical table, create users or alter unrelated databases.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Broad `ACCOUNTADMIN` deployment role.
- Secrets exposed through debug mode.
- An untrusted fork PR receives production environment access.

**Engineering decision:** Prefer WIF and separate DEV/PROD identities; use environment rules, short-lived credentials and audit logs.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Secrets, Workload Identity, and Least Privilege**. The requirement is: Authenticate automated pipelines securely with minimal standing privilege. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Draw the trust flow GitHub OIDC issuer → Snowflake WIF service user → least-privilege role → approved schema.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply secrets, workload identity, and least privilege?**  
A: A GitHub Actions deployment needs to create a manufacturing mart but must not have access to clinical direct identifiers.

**Q: What is the key implementation trade-off?**  
A: Prefer WIF and separate DEV/PROD identities; use environment rules, short-lived credentials and audit logs.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Authenticate the workload, authorize its narrow action, audit the result.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/workload-identity-federation

---

**Navigation:** Previous: [Automated Tests and Deployment Quality Gates](08-automated-testing-quality-gates.md) · Next: [Terraform Infrastructure as Code](10-terraform-iac.md) · [All git-cicd topics](README.md)
