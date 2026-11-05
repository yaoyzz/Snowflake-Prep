# Advanced Governance, Privacy, and Lineage

> **Level:** EXPERT · **Module 04/12** · **Goal:** Design policy-driven access, sensitive data classification, lineage, and evidentiary audit trails.

## 1. Concept and mental model

- **Classification:** tag columns according to sensitivity/regulated use and validate classification coverage.
- **Dynamic masking/row access:** governed sharing controls must be tested for caller role behavior.
- **Lineage:** connect source extraction, transformations, access paths, and downstream reports.
- **Audit:** preserve independent evidence of who changed deployments and who read sensitive data.

**How to reason about it:** Do not assume column masking removes re-identification risk from small cohorts. Use country/role policy conditions, privacy thresholds, and an explicit data use approval process.

## 2. Pharmaceutical data-engineering scenario

Clinical operations shares country-level enrollment metrics while preserving access restrictions on source records and vendor feeds.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Simplified, policy-neutral consumer interface.
CREATE OR REPLACE SECURE VIEW PHARMA_DEMO.MART.V_COUNTRY_ENROLLMENT AS
SELECT study_id,country_code,SUM(enrolled_subjects) subjects
FROM PHARMA_DEMO.CORE.CLINICAL_AGG
GROUP BY study_id,country_code
HAVING SUM(enrolled_subjects) >= 10;
-- A secure view is not a replacement for masking policies,
-- row access policies, or regulatory privacy assessment.
SHOW GRANTS ON VIEW PHARMA_DEMO.MART.V_COUNTRY_ENROLLMENT;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Test inference risk for small groups, role-based deny tests, privilege drift, downstream data-copy paths, and annual access recertification.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Aggregate leaks from small cells or differencing queries.
- Untagged new columns bypass expected classification.
- View ownership or grants changed without review.

**Engineering decision:** Data privacy uses minimization, policy enforcement, approved consumption interfaces, activity monitoring, and human governance together.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Advanced Governance, Privacy, and Lineage**. The requirement is: Design policy-driven access, sensitive data classification, lineage, and evidentiary audit trails. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Produce policy tests for a low-privileged site analyst, global trial lead, service integration, and denied external consumer.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply advanced governance, privacy, and lineage?**  
A: Clinical operations shares country-level enrollment metrics while preserving access restrictions on source records and vendor feeds.

**Q: What is the key implementation trade-off?**  
A: Data privacy uses minimization, policy enforcement, approved consumption interfaces, activity monitoring, and human governance together.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Governance is end-to-end: classify, restrict, monitor, and prove.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/security-column-intro

---

**Navigation:** Previous: [Advanced SQL and Physical Query Optimization](03-advanced-sql-optimization.md) · Next: [GxP, Data Integrity, and Computer System Validation](05-gxp-validation-compliance.md) · [All expert topics](README.md)
