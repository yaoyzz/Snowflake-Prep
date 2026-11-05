# Airflow and Cross-Platform Orchestration

> **Level:** EXPERT · **Module 08/12** · **Goal:** Coordinate reliable end-to-end pipelines across SAP, LIMS, MES, S3, and Snowflake.

## 1. Concept and mental model

- **Airflow DAG:** dependency graph describing tasks and schedules, not an event-storage system.
- **Idempotent task:** safe on retry with the same logical run ID.
- **Backfill:** re-execution of historical periods with bounded partitions and auditability.
- **Ownership:** handle credentials, rate limits, timeouts, retries, and poisoned messages explicitly.

**How to reason about it:** Separate orchestration from transformation. Airflow checks source readiness, launches Snowflake statements or dbt jobs, validates results, and publishes a readiness signal only after quality gates pass.

## 2. Pharmaceutical data-engineering scenario

The daily release-quality mart depends on an ERP master data snapshot, laboratory assay records and manufacturing batch completion.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```python
# Illustrative Airflow TaskFlow DAG; configure a Snowflake connection
# and real tasks before production deployment.
from datetime import datetime
from airflow.decorators import dag, task

@dag(schedule='0 4 * * *', start_date=datetime(2026,1,1),
     catchup=False, tags=['pharma','quality'])
def batch_quality():
    @task
    def check_manifest():
        # Replace with deterministic S3/LIMS manifest checks.
        return {'file_count': 12, 'run_date':'2026-10-10'}

    @task
    def validate(report):
        if report['file_count'] <= 0:
            raise ValueError('Missing files')
    validate(check_manifest())

batch_quality()
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Test failure of each upstream dependency, duplicate DAG runs, task retry, paused schedule, late source files and manual backfill.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Scheduler retries append duplicate target rows.
- All source systems complete at different timestamps.
- No lineage between Airflow run, query tag and approved release.

**Engineering decision:** Use Snowflake Tasks for in-platform orchestration; Airflow for cross-system dependencies and retries needing shared coordination.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Airflow and Cross-Platform Orchestration**. The requirement is: Coordinate reliable end-to-end pipelines across SAP, LIMS, MES, S3, and Snowflake. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Expand the DAG with an approved Snowflake SQL execution task, DQ gate, and notification runbook.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply airflow and cross-platform orchestration?**  
A: The daily release-quality mart depends on an ERP master data snapshot, laboratory assay records and manufacturing batch completion.

**Q: What is the key implementation trade-off?**  
A: Use Snowflake Tasks for in-platform orchestration; Airflow for cross-system dependencies and retries needing shared coordination.

**Q: What are the failure blast radius, permissions, recoverability, and compliance implications?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: What evidence would convince an architecture/security/QA reviewer this is safe?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Orchestrators coordinate work; idempotent transformations ensure safe retries.**

## 10. Official reference

- https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/dags.html

---

**Navigation:** Previous: [Apache Iceberg and Lakehouse Interoperability](07-iceberg-lakehouse.md) · Next: [Cortex AI, Retrieval, and Snowflake ML](09-cortex-ai-ml.md) · [All expert topics](README.md)
