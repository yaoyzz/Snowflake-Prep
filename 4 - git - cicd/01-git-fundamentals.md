# Git Fundamentals and Repository Setup

> **Level:** GIT-CICD · **Module 01/12** · **Goal:** Use Git confidently from an existing working directory on Windows, Linux and macOS.

## 1. Concept and mental model

- **Working tree:** editable local files; **index/staging area:** selected next-commit changes.
- **Commit:** immutable snapshot referenced by a SHA; commit locally before pushing to a remote.
- **Remote:** e.g. `origin`; `git pull` means fetch + integration, not a substitute for cloning.
- **.git:** metadata directory makes a folder a Git repository.

**How to reason about it:** The error `fatal: not a git repository` means the current directory does not contain `.git` or is not below a Git repository root. `git pull https://...` will not initialize an ordinary folder. Clone first or `git init` if starting a genuinely new repository.

## 2. Pharmaceutical data-engineering scenario

A data engineer clones a Snowflake training repository, modifies a manufacturing quality SQL model, and pushes a reviewed branch.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Windows PowerShell OR bash (paths differ by OS)
git clone https://github.com/OWNER/Snowflake-Prep.git
cd Snowflake-Prep
git status
git switch -c feature/batch-yield-check
# Edit medium/07-data-quality.md
git add medium/07-data-quality.md
git commit -m "docs: add batch yield DQ checks"
git push -u origin feature/batch-yield-check
# Inspect context:
git log --oneline -5
git remote -v
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Confirm `git rev-parse --show-toplevel` points to repository root and `git status` is clean after commit/push.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Running `git pull` outside a Git repo.
- Committing credentials or source extracts.
- Using `git add .` without reviewing staged changes.

**Engineering decision:** Use `clone` for existing projects, `init` for a new project, `fetch` to inspect remote changes, and `pull` to integrate.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Git Fundamentals and Repository Setup**. The requirement is: Use Git confidently from an existing working directory on Windows, Linux and macOS. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Clone a disposable sample repo and practice status, diff, stage, commit, undo an unstaged change, and push to a fork.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply git fundamentals and repository setup?**  
A: A data engineer clones a Snowflake training repository, modifies a manufacturing quality SQL model, and pushes a reviewed branch.

**Q: What is the key implementation trade-off?**  
A: Use `clone` for existing projects, `init` for a new project, `fetch` to inspect remote changes, and `pull` to integrate.

**Q: How can you reproduce the change from a Git commit in a clean environment?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What prevents a bad change or leaked secret from reaching PROD?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Clone obtains a repository; pull updates one.**

## 10. Official reference

- https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository

---

**Navigation:** Previous: [Level README](README.md) · Next: [Branches, Merging, Rebase, and Conflict Resolution](02-git-branching-merging.md) · [All git-cicd topics](README.md)
