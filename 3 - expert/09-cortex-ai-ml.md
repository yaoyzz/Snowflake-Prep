# Cortex AI, Retrieval, and Snowflake ML

> **Level:** EXPERT · **Module 09/12** · **Goal:** Design governed AI features over analytical data without confusing model outputs with validated facts.

## 1. Concept and mental model

- **Cortex AI functions:** model-assisted text processing; invocation, availability, cost, and governance vary by account/region.
- **Cortex Search:** indexes and retrieves relevant text for retrieval-augmented applications.
- **Semantic models/Analyst:** map natural language to trusted structured definitions.
- **Human evaluation:** measure grounding, hallucinations, leakage, drift and quality against a reviewed test set.

**How to reason about it:** Start with curated documents and structured metrics; control who can retrieve each record. LLM summaries are not authoritative release decisions and should cite underlying documents.

## 2. Pharmaceutical data-engineering scenario

Quality investigators search synthetic deviation narratives and ask which manufacturing steps correlate with incident types.

The example is a **synthetic pharma-inspired** design, not an assertion about pharma internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- SQL-first retrieval baseline before a model is introduced.
CREATE TABLE IF NOT EXISTS PHARMA_DEMO.CORE.DEVIATION_NOTES (
 deviation_id VARCHAR,batch_id VARCHAR,summary VARCHAR,
 classified_cause VARCHAR,approved BOOLEAN
);
SELECT deviation_id,batch_id,summary
FROM PHARMA_DEMO.CORE.DEVIATION_NOTES
WHERE approved=TRUE
  AND summary ILIKE '%temperature%';
-- For Cortex Search, configure an approved service and its
-- role/access policy; APIs and availability must be verified.
-- Evaluate retrieved document IDs + precision@k before LLM output.
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Evaluate retrieval recall, false citations, document-level access rights, harmful omissions, user approval, and cost per analysis.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Sending PHI/GxP data to unapproved models.
- LLM-generated SQL can join wrong grains or read unauthorized tables.
- Treating an answer without source citations as reliable evidence.

**Engineering decision:** Use deterministic SQL for facts, retrieval for document context, and LLMs for assistance with explicit uncertainty and human review.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Cortex AI, Retrieval, and Snowflake ML**. The requirement is: Design governed AI features over analytical data without confusing model outputs with validated facts. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary pharma details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Design a 20-case gold-standard evaluation set for deviation search including negative and access-control scenarios.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply cortex ai, retrieval, and snowflake ml?**  
A: Quality investigators search synthetic deviation narratives and ask which manufacturing steps correlate with incident types.

**Q: What is the key implementation trade-off?**  
A: Use deterministic SQL for facts, retrieval for document context, and LLMs for assistance with explicit uncertainty and human review.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **An AI answer is an interpretation; evidence and access policy come first.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-search/cortex-search-overview

---

**Navigation:** Previous: [Airflow and Cross-Platform Orchestration](08-advanced-orchestration.md) · Next: [Platform Engineering and Infrastructure as Code](10-platform-engineering.md) · [All expert topics](README.md)
