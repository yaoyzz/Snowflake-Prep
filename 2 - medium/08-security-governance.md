# Security and Governance Fundamentals

> **Level:** MEDIUM · **Module 08/10** · **Goal:** Implement least privilege and understand row access, masking, classification, and auditing.

## 1. Concept and mental model

- **RBAC:** roles and hierarchy for functional access.
- **Masking policy:** conditions that change the value visible to a given role.
- **Row access policy:** restricts row visibility according to a policy expression.
- **Access history:** investigate who accessed sensitive columns and objects where account capabilities permit.

**How to reason about it:** Use synthetic tokens; never store identifying patient values in learning fixtures. Snowflake protections complement, not replace, consent, access approvals, retention, and data transfer assessments.

## 2. Pharmaceutical data-engineering scenario

Clinical site analysts need study-level aggregates but must not view direct identifiers or unauthorized country-level records.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
CREATE TABLE IF NOT EXISTS PHARMA_DEMO.CORE.CLINICAL_AGG (
 study_id VARCHAR,country_code VARCHAR,site_id VARCHAR,
 enrolled_subjects NUMBER
);
-- Demonstration of a view for aggregate-only access:
CREATE OR REPLACE VIEW PHARMA_DEMO.MART.V_STUDY_COUNTS AS
SELECT study_id,country_code,SUM(enrolled_subjects) subjects
FROM PHARMA_DEMO.CORE.CLINICAL_AGG
GROUP BY study_id,country_code;
-- Grant access to the MART view, not raw subject-level tables.
-- Masking/row-access policies require separate role and policy design.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Test with two distinct low-privilege roles, including explicit negative tests for inaccessible RAW objects and unauthorized rows.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Aggregates can still be disclosive for small cohorts.
- Security policies can affect performance/lineage behavior.
- Testing only as an admin fails to detect privilege escalation.

**Engineering decision:** Design defense in depth: authentication, RBAC, policy controls, aggregation threshold rules, monitoring, and reviewed sharing.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Security and Governance Fundamentals**. The requirement is: Implement least privilege and understand row access, masking, classification, and auditing. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Create a role-to-data matrix and a test plan for role-specific positive and negative queries.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply security and governance fundamentals?**  
A: Clinical site analysts need study-level aggregates but must not view direct identifiers or unauthorized country-level records.

**Q: What is the key implementation trade-off?**  
A: Design defense in depth: authentication, RBAC, policy controls, aggregation threshold rules, monitoring, and reviewed sharing.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Data minimization first; policy enforcement second; audit evidence always.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/security-column-intro

---

**Navigation:** Previous: [Data Quality and Reconciliation](07-data-quality.md) · Next: [Query Performance and Cost Optimization](09-performance-cost.md) · [All medium topics](README.md)
