# Snowflake Objects and RBAC

> **Level:** ENTRY · **Module 02/08** · **Goal:** Navigate the object hierarchy and grant least-privilege access to a domain schema.

## 1. Concept and mental model

- **Hierarchy:** organization → account → database → schema → table/view/stage/pipe/task.
- **Access:** roles hold privileges; users assume roles; the active role and inherited grants determine permissions.
- **USAGE:** access to parent database/schema is generally needed before accessing a child table.
- **Ownership:** ownership is different from a `SELECT` grant; deploy roles should not default to `ACCOUNTADMIN`.

**How to reason about it:** Create dedicated object-owner and consumer roles. Prefer future grants or managed access schemas where they fit your governance policy. Separate human administration from pipeline service identities.

## 2. Pharmaceutical data-engineering scenario

A clinical analytics role may read de-identified study metrics, while a manufacturing ETL role may write RAW production events.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
-- Role creation and grants require appropriate administrative privileges.
CREATE DATABASE IF NOT EXISTS PHARMA_DEMO;
CREATE SCHEMA IF NOT EXISTS PHARMA_DEMO.RAW;
CREATE SCHEMA IF NOT EXISTS PHARMA_DEMO.MART;
CREATE ROLE IF NOT EXISTS PHARMA_ANALYST;
GRANT USAGE ON DATABASE PHARMA_DEMO TO ROLE PHARMA_ANALYST;
GRANT USAGE ON SCHEMA PHARMA_DEMO.MART TO ROLE PHARMA_ANALYST;
GRANT SELECT ON ALL TABLES IN SCHEMA PHARMA_DEMO.MART
  TO ROLE PHARMA_ANALYST;
GRANT SELECT ON FUTURE TABLES IN SCHEMA PHARMA_DEMO.MART
  TO ROLE PHARMA_ANALYST;
SHOW GRANTS TO ROLE PHARMA_ANALYST;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Review `SHOW GRANTS TO ROLE PHARMA_ANALYST`; verify there is no unintended RAW or DDL permission.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Granting `ALL PRIVILEGES` indiscriminately.
- Forgetting usage on parent containers.
- Mistaking role names for a substitute for a documented access matrix.

**Engineering decision:** Use dedicated developer, deployment, workload, and reader roles; limit cross-domain sharing to documented data products.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Snowflake Objects and RBAC**. The requirement is: Navigate the object hierarchy and grant least-privilege access to a domain schema. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Build an access matrix: clinical readers, manufacturing writers, QA auditors, and platform deployment automation.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply snowflake objects and rbac?**  
A: A clinical analytics role may read de-identified study metrics, while a manufacturing ETL role may write RAW production events.

**Q: What is the key implementation trade-off?**  
A: Use dedicated developer, deployment, workload, and reader roles; limit cross-domain sharing to documented data products.

**Q: How would you demonstrate this in a Snowflake sandbox without accessing production data?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which source identifiers and metrics must be retained to prove correctness?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Roles grant privileges; access also requires a usable path through parent objects.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/security-access-control-overview

---

**Navigation:** Previous: [Snowflake Architecture](01-architecture.md) · Next: [SQL Essentials for Data Engineering](03-sql-essentials.md) · [All entry topics](README.md)
