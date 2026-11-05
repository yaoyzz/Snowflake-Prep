# Branches, Merging, Rebase, and Conflict Resolution

> **Level:** GIT-CICD · **Module 02/12** · **Goal:** Deliver changes in small branches and resolve conflicts without rewriting shared history.

## 1. Concept and mental model

- **Branch:** movable pointer to a commit, not a copy of all files.
- **Merge:** joins histories and preserves evidence of parallel lines.
- **Rebase:** reapplies commits onto a new base; changes commit hashes.
- **Cherry-pick:** applies selected commits to another branch; useful for controlled patches.

**How to reason about it:** Prefer short-lived feature branches. Resolve conflicts by understanding which transformation is authoritative, not merely selecting “ours” or “theirs.”

## 2. Pharmaceutical data-engineering scenario

A schema migration and a data-quality fix both modify a core manufacturing model.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
git fetch origin
git switch main
git pull --ff-only origin main
git switch -c feature/lab-qc
# ... edit and commit a change ...
git fetch origin
git rebase origin/main
# If conflict: edit affected files, inspect tests, then:
git add path/to/conflicted-file.sql
git rebase --continue
# To abandon rebase: git rebase --abort
# A shared branch should generally be merged rather than rebased.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Inspect `git log --graph --oneline --all`, rerun affected tests, and compare final diff with intended requirements.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Force-pushing a shared branch rewrites collaborators’ history.
- Accidental conflict resolution deletes a schema validation.
- Rebasing after branch review without informing reviewers.

**Engineering decision:** Rebase private work for clarity; merge shared history unless team policy says otherwise; use `--force-with-lease` only with deliberate ownership.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Branches, Merging, Rebase, and Conflict Resolution**. The requirement is: Deliver changes in small branches and resolve conflicts without rewriting shared history. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create simultaneous edits to the same SQL line, resolve conflict, test resulting SQL and explain both parents of a merge.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply branches, merging, rebase, and conflict resolution?**  
A: A schema migration and a data-quality fix both modify a core manufacturing model.

**Q: What is the key implementation trade-off?**  
A: Rebase private work for clarity; merge shared history unless team policy says otherwise; use `--force-with-lease` only with deliberate ownership.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Merge reconciles histories; rebase rewrites local history.**

## 10. Official reference

- https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging

---

**Navigation:** Previous: [Git Fundamentals and Repository Setup](01-git-fundamentals.md) · Next: [GitHub Pull Requests and Code Review](03-github-pr-code-review.md) · [All git-cicd topics](README.md)
