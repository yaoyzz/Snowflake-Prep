# AI-Assisted Git, CI/CD, and DevOps

> **Level:** GIT-CICD · **Module 12/12** · **Goal:** Use coding agents to create reproducible PRs, tests and troubleshooting reports without enabling unsafe autonomous releases.

## 1. Concept and mental model

- **AI scaffolding:** generate migration drafts, workflows, fixtures and tests from explicit specs.
- **Review contract:** show diff, assumptions, affected objects, test output, security impact and failure plan.
- **Restricted execution:** read-only investigation and sandboxed test permissions by default.
- **Provenance:** generated code is linked to source requirement and human-approved PR.

**How to reason about it:** Prompt the agent to preserve existing behavior until consumers migrate. Do not let agents read raw patient data, make unreviewed access-control changes, or merge to protected branches.

## 2. Pharmaceutical data-engineering scenario

An AI agent proposes a schema change for a new LIMS measurement field and updates dbt models, tests and workflow configurations.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Safe repeatable local review sequence:
git switch -c feature/lims-unit
# Ask AI to create a migration and tests in the working tree.
git diff --check
git diff --stat
git diff -- sql/ tests/ .github/
# Run project lint + tests, then inspect the exact staged diff:
git add sql/ tests/ .github/
git diff --cached
# Commit only after human review:
# git commit -m "feat: version LIMS measurement unit"
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Evaluate AI proposal against fixture tests, static secret scan, CI permission checks, human reviewer signoff, and backfill correctness.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Agent changes workflow `permissions` to broad scopes.
- Hallucinated Snowflake SQL syntax enters deployment.
- AI-generated tests simply assert mocked results and never touch real transformation logic.

**Engineering decision:** Use AI to propose and accelerate; CI to falsify errors; owners to authorize PROD.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **AI-Assisted Git, CI/CD, and DevOps**. The requirement is: Use coding agents to create reproducible PRs, tests and troubleshooting reports without enabling unsafe autonomous releases. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a reusable agent prompt that requests a PR-ready migration plus tests, no secrets, no production access, and a risk summary.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply ai-assisted git, ci/cd, and devops?**  
A: An AI agent proposes a schema change for a new LIMS measurement field and updates dbt models, tests and workflow configurations.

**Q: What is the key implementation trade-off?**  
A: Use AI to propose and accelerate; CI to falsify errors; owners to authorize PROD.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Agents propose; pipelines verify; accountable humans approve.**

## 10. Official reference

- https://docs.github.com/en/actions

---

**Navigation:** Previous: [Deployments, Promotion, and Rollback](11-deployment-rollback.md) · Next: [Level README](README.md) · [All git-cicd topics](README.md)
