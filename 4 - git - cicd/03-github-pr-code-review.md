# GitHub Pull Requests and Code Review

> **Level:** GIT-CICD · **Module 03/12** · **Goal:** Run an auditable PR process including review ownership, protected branches and checks.

## 1. Concept and mental model

- **PR:** proposed merge of a branch into a protected target with discussion and CI results.
- **CODEOWNERS:** routes review requests to domain ownership teams.
- **Branch rulesets:** enforce required reviews/checks and prevent unreviewed direct pushes.
- **Review artifact:** commits, test evidence, linked requirement/change ticket and migration risk.

**How to reason about it:** Create acceptance criteria before coding. Reviewers verify business grain, data classification, deterministic tests, performance and approval evidence, not just syntax.

## 2. Pharmaceutical data-engineering scenario

A transformation now excludes provisional batches from the approved QA dashboard; QA and platform owners must review the behavioral change.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# .github/CODEOWNERS (example; replace teams with real GitHub owners)
# /sql/clinical/   @ORG/clinical-data
# /sql/manufacturing/ @ORG/mfg-data
# /infra/ @ORG/platform

# Example GitHub CLI usage if authenticated:
gh pr create --base main --head feature/batch-yield-check   --title "Validate manufacturing batch yield"   --body "Adds DQ threshold checks; includes regression tests."
gh pr checks
gh pr view --web
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Check required approvals, CI status, merge-base diff, changed regulated objects, and linked release evidence before merge.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Approval from someone lacking domain context.
- A green linter but no data reconciliation.
- Unprotected branch allows silent direct push.

**Engineering decision:** Require domain review for semantic changes and platform review for privilege/infra changes; use a protected default branch.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **GitHub Pull Requests and Code Review**. The requirement is: Run an auditable PR process including review ownership, protected branches and checks. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Draft a pull request template with risk, data contracts, tests, deployment, backfill and rollback sections.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply github pull requests and code review?**  
A: A transformation now excludes provisional batches from the approved QA dashboard; QA and platform owners must review the behavioral change.

**Q: What is the key implementation trade-off?**  
A: Require domain review for semantic changes and platform review for privilege/infra changes; use a protected default branch.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **PRs connect change intent, tested implementation, and accountable approval.**

## 10. Official reference

- https://docs.github.com/en/pull-requests

---

**Navigation:** Previous: [Branches, Merging, Rebase, and Conflict Resolution](02-git-branching-merging.md) · Next: [Data Engineering Repository Structure](04-repository-structure.md) · [All git-cicd topics](README.md)
