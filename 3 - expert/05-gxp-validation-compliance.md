# GxP, Data Integrity, and Computer System Validation

> **Level:** EXPERT · **Module 05/12** · **Goal:** Recognize regulated-use boundaries and plan appropriate electronic record controls and validation evidence.

## 1. Concept and mental model

- **GxP:** umbrella for regulated good practices; exact obligations depend on the workflow/system and jurisdiction.
- **21 CFR Part 11:** electronic records/signatures controls where applicable; evaluate FDA guidance and predicate rules.
- **ALCOA+:** attributable, legible, contemporaneous, original, accurate plus complete, consistent, enduring and available.
- **Validation:** user requirements, risk assessment, traceability, approved test evidence, deviation and change control.

**How to reason about it:** Classify the intended use and impact on product quality/patient safety. Snowflake product functionality alone does not establish GxP validation, audit trail adequacy, electronic-signature compliance or the integrity of connected MES/LIMS records.

## 2. Pharmaceutical data-engineering scenario

A quality dashboard supports investigation of manufacturing deviations; it is not assumed to be an approved release decision system.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Example validation evidence query (not itself a compliant audit trail).
SELECT batch_id,COUNT(*) AS versions,
       MIN(source_updated_at) AS earliest_source_change,
       MAX(source_updated_at) AS latest_source_change
FROM PHARMA_DEMO.RAW.BATCH_EVENTS
GROUP BY batch_id;
-- Required accompanying artifacts (not executable SQL):
-- URS -> risk assessment -> design -> IQ/OQ/PQ or justified tests
-- -> traceability matrix -> approval -> controlled release
-- -> periodic review and retirement plan.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Demonstrate documented URS-to-test traceability, approved test results, source-to-target lineage, access tests, change approval, and retention strategy.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Calling a technology “FDA certified” without system-level validation.
- Treating a Snowflake query log as sufficient audit trail for all regulated events.
- Sending regulated content to unapproved external AI models.

**Engineering decision:** Use a documented, risk-based validation plan reviewed by quality/regulatory stakeholders; determine applicable rules with domain owners.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **GxP, Data Integrity, and Computer System Validation**. The requirement is: Recognize regulated-use boundaries and plan appropriate electronic record controls and validation evidence. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Write five URS and matching test cases for validated batch-yield reporting; add a deviation and corrective action workflow.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply gxp, data integrity, and computer system validation?**  
A: A quality dashboard supports investigation of manufacturing deviations; it is not assumed to be an approved release decision system.

**Q: What is the key implementation trade-off?**  
A: Use a documented, risk-based validation plan reviewed by quality/regulatory stakeholders; determine applicable rules with domain owners.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **GxP compliance is a validated, controlled system lifecycle—not a database feature.**

## 10. Official reference

- https://www.fda.gov/regulatory-information/search-fda-guidance-documents/part-11-electronic-records-electronic-signatures-scope-and-application

---

**Navigation:** Previous: [Advanced Governance, Privacy, and Lineage](04-advanced-governance.md) · Next: [Resilience, Recovery, and Disaster Readiness](06-resilience-recovery.md) · [All expert topics](README.md)
