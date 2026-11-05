# Snowflake Prep

A structured study guide for Snowflake data engineering, hands-on practice, and interview preparation. Work through **42 modules across four learning tracks**, from SQL and warehouse fundamentals to enterprise architecture and controlled deployments.

The lessons use a shared fictional pharmaceutical data platform to connect concepts with practical examples, validation checks, and engineering trade-offs.

## Learning tracks

| Track | Modules | Topics | Start here |
| --- | ---: | --- | --- |
| **1 · Entry** | 8 | Architecture, objects and RBAC, SQL, data types, staged loading, JSON, dimensional modeling, and basic operations | [Foundations](1%20-%20entry/README.md) |
| **2 · Medium** | 10 | Snowpipe, Streams and Tasks, Dynamic Tables, MERGE and SCD, Snowpark Python, dbt, data quality, security, performance, and observability | [Incremental pipelines](2%20-%20medium/README.md) |
| **3 · Expert** | 12 | Enterprise architecture, streaming, advanced optimization, governance, GxP, recovery, Iceberg, Airflow, Cortex AI/ML, platform engineering, FinOps, and AI agents | [Enterprise platform design](3%20-%20expert/README.md) |
| **4 · Git / CI/CD** | 12 | Git, branching, pull requests, repository design, Snowflake CLI, migrations, GitHub Actions, testing, identity, Terraform, deployments, and AI-assisted DevOps | [Version control and delivery](4%20-%20git%20-%20cicd/README.md) |

**Suggested order:** Entry → Medium → Expert. Study Git / CI/CD alongside the technical tracks, especially before attempting deployment exercises.

Each track README contains the full module index, a capstone, interview checkpoints, and completion criteria.

## Repository structure

```text
Snowflake-Prep/
├── README.md
├── 1 - entry/         # 8 foundational modules + track guide
├── 2 - medium/        # 10 pipeline modules + track guide
├── 3 - expert/        # 12 enterprise modules + track guide
└── 4 - git - cicd/    # 12 delivery modules + track guide
```

The repository contains Markdown lessons with embedded SQL, Python, shell, and configuration examples. External tools and services are configured separately for the exercises that need them.

## Get started

You can read every lesson without a Snowflake account. For hands-on practice, use a disposable Snowflake development or trial environment and an editor or SQL worksheet. Install Git, Snowflake CLI, Python, dbt, Airflow, or Terraform as required by the module you choose.

1. **Understand the platform.** Read [Snowflake Architecture](1%20-%20entry/01-architecture.md) and its warehouse example.
2. **Set up access.** Follow [Objects and RBAC](1%20-%20entry/02-objects-rbac.md) to understand roles and grants.
3. **Create the shared lab.** Run the bootstrap example in [Data Types and Table Design](1%20-%20entry/04-data-types-tables.md) to create `PHARMA_DEMO`, its schemas, and the synthetic batch-event table.
4. **Load a small fixture.** Use the [synthetic CSV starter](1%20-%20entry/README.md#synthetic-csv-starter-fixture) with [Stages, File Formats, and COPY INTO](1%20-%20entry/05-loading-stages.md).
5. **Follow your track.** Work through numbered modules, validate the results, and complete the track capstone.

Examples can create billable resources and require specific privileges. Check each module's prerequisites and use a restricted development role with an auto-suspending warehouse. Some integrations are configuration sketches that require additional infrastructure.

## Shared lab: `PHARMA_DEMO`

The exercises use synthetic manufacturing, laboratory, and clinical scenarios. The shared database separates data by purpose:

| Schema | Purpose |
| --- | --- |
| `RAW` | Landing data and source records |
| `CORE` | Validated and transformed data |
| `MART` | Analytical models and reporting outputs |
| `OPS` | Operational records and monitoring |

Some modules introduce additional domain schemas. The foundational warehouse example uses `WH_PHARMA_ETL`.

## How to study

Each module includes a mental model, a pharmaceutical scenario, an implementation example, validation criteria, failure modes, an AI-assisted workflow, an exercise, interview questions, a takeaway, and official references.

For each topic:

1. Explain the concept and its main trade-off in your own words.
2. Run the focused example against a small synthetic dataset.
3. Check row counts, business keys, data quality, and expected outputs.
4. Introduce a duplicate, null, late arrival, or failure and observe the behavior.
5. Record the assumptions, results, and recovery steps, then answer the interview questions without the notes.

Use the AI prompts to generate variations or review your work, and independently verify the proposed code and conclusions.

## Capstone projects

| Track | Project | Evidence of completion |
| --- | --- | --- |
| Entry | Batch manufacturing analytics | Load synthetic data, model batch grain, calculate yield, and reconcile results. |
| Medium | Incremental manufacturing and clinical pipeline | Handle replay and late arrivals, test data quality, and measure freshness and cost. |
| Expert | Enterprise pharmaceutical platform design | Document architecture decisions, governance, recovery, validation strategy, and operating controls. |
| Git / CI/CD | Protected PR to controlled Snowflake deployment | Demonstrate automated checks, scoped identity, environment promotion, and a recovery plan. |

See each track guide for its full definition of done.

## Data and usage boundaries

All company scenarios and records are **fictional, pharma-inspired learning examples**. They do not describe pharma's internal systems. Use synthetic or explicitly approved data, and keep credentials, patient data, and proprietary extracts out of the repository and AI prompts.

These exercises are educational and are not a validated production system or a source of clinical or manufacturing release rules. Any regulated use requires the appropriate organizational review and validation.

Modules link to official references for their topics. Check the relevant documentation, installed tool versions, and account capabilities before applying an example outside the lab.
