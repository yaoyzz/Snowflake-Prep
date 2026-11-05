# AI-Agent-Assisted Data Platform Engineering

> **Level:** EXPERT · **Module 12/12** · **Goal:** Use coding agents to accelerate investigation while preserving reproducibility, least privilege and review.

## 1. Concept and mental model

- **Agent scope:** explicit repository, allowed commands, branch, bounded dataset, and acceptance criteria.
- **Grounding:** query profiles, schema definitions, lineage and failed tests—not raw guesses.
- **Tool boundary:** local code, read-only Snowflake access and controlled deployment roles.
- **Review:** deterministic tests, diff review, data provenance, performance comparison and approvals.

**How to reason about it:** Provide synthetic examples and a read-only schema snapshot. Require the agent to explain root cause, identify affected partitions, propose a minimal patch and generate a reconciliation test. Human owner approves production release.

## 2. Pharmaceutical data-engineering scenario

An agent investigates a sudden rise in rejected manufacturing quantities after a vendor changed one input unit.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```bash
# Example prompt to an approved coding agent:
# "Investigate why total units_rejected doubled after run R-104.
# Use only read-only SQL and the checked-out repository.
# Compare RAW vs CORE by batch_id, source version, time and unit.
# Propose a minimal patch, add tests for duplicates and unit mismatch,
# and report a reproducible SQL query. Do not deploy or export data."

# Example reviewer guardrail (shell):
git diff --check
git status --short
# Execute real project tests in a sandbox, then require a PR review.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Compare proposed patch vs baseline on golden fixtures, data-quality counts, security scan, query cost and backfill blast radius.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Prompt injection from source comments or data.
- An agent with broad production write access.
- A model invents a cause from partial logs and bypasses reproducing the anomaly.

**Engineering decision:** Use agents for diagnosis and implementation proposals; reserve production mutations and regulated approval for authorized humans/processes.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **AI-Agent-Assisted Data Platform Engineering**. The requirement is: Use coding agents to accelerate investigation while preserving reproducibility, least privilege and review. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write an agent policy covering data allowed in prompts, SQL privileges, artifact retention, tests and two-person review.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply ai-agent-assisted data platform engineering?**  
A: An agent investigates a sudden rise in rejected manufacturing quantities after a vendor changed one input unit.

**Q: What is the key implementation trade-off?**  
A: Use agents for diagnosis and implementation proposals; reserve production mutations and regulated approval for authorized humans/processes.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **AI accelerates hypothesis and code; deterministic evidence establishes correctness.**

## 10. Official reference

- https://docs.snowflake.com/en/developer-guide/snowflake-cli/index

---

**Navigation:** Previous: [FinOps, SLOs, and Incident Response](11-finops-observability.md) · Next: [Level README](README.md) · [All expert topics](README.md)
