# ENTRY — Foundational Snowflake SQL and warehouse operation

Understand the core architecture, SQL, data contracts, ingestion and a batch mart. **8 learning modules + this README.** All examples assume an independently provisioned Snowflake sandbox and use fictitious data.

## Learning path

| # | Markdown file | Outcome |
|---:|---|---|
| 01 | [Snowflake Architecture](01-architecture.md) | Explain the separation of storage, virtual warehouses, and Snowflake cloud services and choose compute for distinct workloads. |
| 02 | [Snowflake Objects and RBAC](02-objects-rbac.md) | Navigate the object hierarchy and grant least-privilege access to a domain schema. |
| 03 | [SQL Essentials for Data Engineering](03-sql-essentials.md) | Apply joins, window functions, CTEs, QUALIFY, and safe aggregation to operational datasets. |
| 04 | [Data Types and Table Design](04-data-types-tables.md) | Define stable data contracts using DATE/TIMESTAMP/NUMBER/VARCHAR/BOOLEAN and appropriate table semantics. |
| 05 | [Stages, File Formats, and COPY INTO](05-loading-stages.md) | Load CSV/JSON into Snowflake using stages and COPY INTO, including validation and error handling. |
| 06 | [JSON and Semi-Structured Data](06-semi-structured-data.md) | Parse VARIANT payloads, flatten arrays, cast types, and validate schema evolution. |
| 07 | [Basic Dimensional Data Modeling](07-basic-data-modeling.md) | Design a batch-oriented star schema and understand source grain versus reporting grain. |
| 08 | [Query History, Time Travel, and Basic Operations](08-basic-operations.md) | Investigate SQL failures, inspect query profiles, and understand recovery and warehouse usage. |

## Shared fictional laboratory

All companies, source feeds, people and sample records in this repository are **hypothetical Sanofi-inspired learning examples**, not claims about Sanofi's internal systems. Use synthetic or specifically approved datasets only. The consistent sandbox namespace is `PHARMA_DEMO`, with `RAW` (landing), `CORE` (validated), `MART` (consumption), and `OPS` (operational records). Some examples use additional domain schemas.

A minimal sandbox can be initialized from [`entry/04-data-types-tables.md`](../entry/04-data-types-tables.md). For role/warehouse prerequisites see [`entry/01-architecture.md`](../entry/01-architecture.md) and [`entry/02-objects-rbac.md`](../entry/02-objects-rbac.md). Statements may create billable resources or require privileges; execute only in an authorized disposable development environment. External services, cloud integrations and tools such as dbt/Airflow/Terraform must be set up separately.

**Business definition:** illustrative manufacturing yield = `(SUM(units_produced) - SUM(units_rejected)) / SUM(units_produced)`. Actual yield and release rules depend on approved source definitions, product specifications, batch lifecycle and QA decisions.


### Lab sequence

1. Create a Snowflake trial/development environment with a restricted role and an auto-suspending XSMALL warehouse. Do not assume account-admin rights are appropriate for every step.
2. Run [`04-data-types-tables.md`](04-data-types-tables.md) to create the shared synthetic batch event table. For standalone examples that use the warehouse, create `WH_PHARMA_ETL` in [`01-architecture.md`](01-architecture.md) first.
3. Practice joins/windows, stage CSV loading, JSON parsing and yield aggregation.
4. Use query history and compare source/target control totals. Discuss failures before claiming the capstone is successful.

**Interview checkpoint:** Explain why Snowflake storage and warehouses are separate, why batch event grain matters, and why primary-key constraints do not replace uniqueness tests on standard Snowflake tables.


## Synthetic CSV starter fixture

To try the stage-loading chapter, save these four lines as **`sample_batch.csv`** in your working directory (UTF-8, header included). The values and IDs are invented and have no relationship to real Sanofi records.

```csv
batch_id,plant_id,product_id,event_ts,status,units_produced,units_rejected,source_updated_at
B-101,PL-01,DRUG-A,2026-10-11 08:00:00,COMPLETE,1000,12,2026-10-11 09:00:00
B-102,PL-01,DRUG-A,2026-10-12 08:00:00,COMPLETE,800,20,2026-10-12 09:00:00
B-103,PL-02,DRUG-B,2026-10-12 10:00:00,COMPLETE,600,6,2026-10-12 11:00:00
```

Run the database/table bootstrap in [`04-data-types-tables.md`](04-data-types-tables.md), create the stage/file format in [`05-loading-stages.md`](05-loading-stages.md), upload the file via `snow stage copy`, then execute its `COPY INTO` example. Check that loaded quantities reconcile to the fixture (2,400 units produced, 38 rejected), accounting separately for any records previously inserted by the bootstrap exercise. Use a fresh disposable table for exact expected row counts.

## Capstone — Batch manufacturing analytics

1. Create `PHARMA_DEMO` and RAW/CORE/MART/OPS schemas with a dedicated DEV warehouse.
2. Load at least 12 synthetic batch rows and a JSON laboratory document; retain source IDs.
3. Deduplicate by business key and source revision; calculate plant/product yield from additive units.
4. Report rejected rows, loaded counts and replay behavior; produce a short architecture explanation.

### Definition of done

- [ ] No duplicate batch key in the current-state output.
- [ ] No negative or over-100% yield rates.
- [ ] Raw data remains available for reruns.
- [ ] Every command runs under a documented development role.

## How to study efficiently

Read the mental model, run the focused example, validate it using a tiny synthetic fixture, introduce one deliberate failure, and explain the trade-off without looking at the text. Use the AI prompt inside each chapter to generate test variants, then independently verify the result. Record what failed and why.

## Quality and compliance boundary

This repository is a learning resource, **not a validated Sanofi system** and not a source of clinical or manufacturing release rules. Never use real patient data, proprietary company extracts, credentials or unapproved model endpoints in exercises. For GxP-relevant uses, involve system owners, security and Quality to assess applicability, computer-system validation, electronic-record controls, retention and audit evidence. Cloud product features alone do not confer compliance.

## Sources and freshness

Primary sources are the official Snowflake docs linked from each module, plus official Git/GitHub, dbt, Airflow, Terraform and FDA references where applicable. Snowflake features and CLI syntax can change; verify your account's region/edition and installed tool versions before applying production examples. Content prepared November 2026.

## Other learning levels

- [Entry](../entry/README.md)
- [Medium](../medium/README.md)
- [Expert](../expert/README.md)
- [Git / CI/CD](../git-cicd/README.md)
