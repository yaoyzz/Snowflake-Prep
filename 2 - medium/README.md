# MEDIUM — Build, test and monitor incremental pipelines

Implement production-shaped CDC, dbt, governance, reliability and cost measurement. **10 learning modules + this README.** All examples assume an independently provisioned Snowflake sandbox and use fictitious data.

## Learning path

| # | Markdown file | Outcome |
|---:|---|---|
| 01 | [Snowpipe File Ingestion](01-snowpipe.md) | Configure asynchronous file-based loading and explain auto-ingest notifications, monitoring, and replay. |
| 02 | [Streams, Tasks, and Incremental CDC](02-streams-tasks.md) | Construct an incremental transformation using change tracking and scheduled Snowflake tasks. |
| 03 | [Dynamic Tables and Target Lag](03-dynamic-tables.md) | Build declarative incremental transformations and understand refresh cost, modes, and freshness. |
| 04 | [MERGE, Deduplication, SCD Type 1 and 2](04-merge-scd.md) | Implement deterministic upserts and choose historical-change strategies. |
| 05 | [Snowpark Python and Stored Procedures](05-snowpark-python.md) | Use Snowpark DataFrames for in-platform transformations and manage Python package/runtime choices. |
| 06 | [dbt Models, Tests, and Incremental Builds](06-dbt-data-modeling.md) | Manage modular, versioned SQL transformations with dbt and test contracts. |
| 07 | [Data Quality and Reconciliation](07-data-quality.md) | Specify data contracts, implement quantitative validation, and route anomalies to quarantine. |
| 08 | [Security and Governance Fundamentals](08-security-governance.md) | Implement least privilege and understand row access, masking, classification, and auditing. |
| 09 | [Query Performance and Cost Optimization](09-performance-cost.md) | Diagnose scans, pruning, spills and warehouse utilization before changing compute size. |
| 10 | [CI/CD and Operational Observability](10-cicd-observability.md) | Instrument pipeline runs and connect Git-controlled changes to deployed Snowflake behavior. |

## Shared fictional laboratory

All companies, source feeds, people and sample records in this repository are **hypothetical pharma-inspired learning examples**, not claims about pharma's internal systems. Use synthetic or specifically approved datasets only. The consistent sandbox namespace is `PHARMA_DEMO`, with `RAW` (landing), `CORE` (validated), `MART` (consumption), and `OPS` (operational records). Some examples use additional domain schemas.

A minimal sandbox can be initialized from [`entry/04-data-types-tables.md`](../entry/04-data-types-tables.md). For role/warehouse prerequisites see [`entry/01-architecture.md`](../entry/01-architecture.md) and [`entry/02-objects-rbac.md`](../entry/02-objects-rbac.md). Statements may create billable resources or require privileges; execute only in an authorized disposable development environment. External services, cloud integrations and tools such as dbt/Airflow/Terraform must be set up separately.

**Business definition:** illustrative manufacturing yield = `(SUM(units_produced) - SUM(units_rejected)) / SUM(units_produced)`. Actual yield and release rules depend on approved source definitions, product specifications, batch lifecycle and QA decisions.


### Recommended build order

Establish the shared RAW tables, then compare Snowpipe file loading to direct row ingestion. Build an insert-only stream/task CDC pipeline and a declarative dynamic table, add SCD handling and a dbt DAG, and finally wire DQ/security/CI. Read the complementary [Git/CI/CD course](../git-cicd/README.md) before deploying beyond DEV.

**Interview checkpoint:** Distinguish Stream offset vs. audit log, Snowpipe vs. Snowpipe Streaming, target lag vs. refresh interval, and exactly-once transport vs. idempotent business output.


## Capstone — Incremental manufacturing and clinical data pipeline

1. Ingest a staged JSON/CSV feed using COPY/Snowpipe; document event infrastructure prerequisites.
2. Build a Stream + Task + deterministic MERGE and a comparable Dynamic Table transformation.
3. Implement a dbt DAG, source assertions, freshness checks, and a quarantine route.
4. Instrument query tags, task history, reviewable Git diffs and CI evidence.

### Definition of done

- [ ] Repeated or out-of-order input does not corrupt current state.
- [ ] Data-quality failure blocks publication.
- [ ] DEV deployment is repeatable with least privilege.
- [ ] Actual freshness and runtime cost are measured.

## How to study efficiently

Read the mental model, run the focused example, validate it using a tiny synthetic fixture, introduce one deliberate failure, and explain the trade-off without looking at the text. Use the AI prompt inside each chapter to generate test variants, then independently verify the result. Record what failed and why.

## Quality and compliance boundary

This repository is a learning resource, **not a validated pharma system** and not a source of clinical or manufacturing release rules. Never use real patient data, proprietary company extracts, credentials or unapproved model endpoints in exercises. For GxP-relevant uses, involve system owners, security and Quality to assess applicability, computer-system validation, electronic-record controls, retention and audit evidence. Cloud product features alone do not confer compliance.

## Sources and freshness

Primary sources are the official Snowflake docs linked from each module, plus official Git/GitHub, dbt, Airflow, Terraform and FDA references where applicable. Snowflake features and CLI syntax can change; verify your account's region/edition and installed tool versions before applying production examples. Content prepared November 2026.

## Other learning levels

- [Entry](../entry/README.md)
- [Medium](../medium/README.md)
- [Expert](../expert/README.md)
- [Git / CI/CD](../git-cicd/README.md)
