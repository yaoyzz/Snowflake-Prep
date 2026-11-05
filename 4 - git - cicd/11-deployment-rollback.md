# Deployments, Promotion, and Rollback

> **Level:** GIT-CICD · **Module 11/12** · **Goal:** Promote changes through isolated environments and prepare recovery for code, schema, and data.

## 1. Concept and mental model

- **DEV:** fast feedback with synthetic fixtures; **TEST/UAT:** production-like validation.
- **PROD:** protected approved deployment bound to a specific commit.
- **Rollback types:** code revert, schema rollback, data restoration, and compensating migration are different.
- **Canary:** validate impact on small safe subset before full transition when architecture permits.

**How to reason about it:** A git revert only changes code; it does not automatically reverse a deployed schema or corrupted target rows. Stop bad writes, preserve evidence, restore from trusted snapshots/replay, then deploy a tested fix.

## 2. Pharmaceutical data-engineering scenario

A plant-level dashboard change accidentally drops late-arriving batches; the team must restore prior business semantics.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Code rollback planning (reviewed PR; do not force-push PROD)
git log --oneline -5
git revert <bad_commit_sha>
git push origin rollback/batch-yield

# Snowflake verification after a controlled restore:
# SELECT batch_id, COUNT(*) FROM ... GROUP BY 1 HAVING COUNT(*)>1;
# SELECT SUM(units_produced),SUM(units_rejected) FROM ...;
# Compare to approved source manifest / baseline snapshot.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Acceptance: correct code revision deployed, historical data reconciled, no delayed consumers, role/access intact, and incident ticket complete.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Assuming a revert reverses prior DML.
- DROP/REPLACE deployed without Time Travel/recovery review.
- PROD approval bypassed for emergency fix without an emergency procedure.

**Engineering decision:** Use backward-compatible releases and prepared roll-forward/rollback plus source replay; select action based on actual data damage.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Deployments, Promotion, and Rollback**. The requirement is: Promote changes through isolated environments and prepare recovery for code, schema, and data. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Document an emergency rollback runbook after a changed aggregation query doubles the manufacturing yield denominator.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply deployments, promotion, and rollback?**  
A: A plant-level dashboard change accidentally drops late-arriving batches; the team must restore prior business semantics.

**Q: What is the key implementation trade-off?**  
A: Use backward-compatible releases and prepared roll-forward/rollback plus source replay; select action based on actual data damage.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Code rollback ≠ data rollback; plan both before release.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/data-time-travel

---

**Navigation:** Previous: [Terraform Infrastructure as Code](10-terraform-iac.md) · Next: [AI-Assisted Git, CI/CD, and DevOps](12-ai-assisted-devops.md) · [All git-cicd topics](README.md)
