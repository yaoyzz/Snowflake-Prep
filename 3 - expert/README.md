# EXPERT — Own enterprise platform architecture and regulated controls

Defend architectural trade-offs, risk-based validation, resilience and AI controls. **12 learning modules + this README.** All examples assume an independently provisioned Snowflake sandbox and use fictitious data.

## Learning path

| # | Markdown file | Outcome |
|---:|---|---|
| 01 | [Enterprise Pharmaceutical Data Platform Architecture](01-enterprise-architecture.md) | Design Snowflake as a governed, multi-domain enterprise data platform with clear ingestion contracts and tenancy boundaries. |
| 02 | [Real-Time Streaming and Kafka](02-real-time-streaming.md) | Design low-latency event ingestion with high-performance Snowpipe Streaming and Kafka connector v4 considerations. |
| 03 | [Advanced SQL and Physical Query Optimization](03-advanced-sql-optimization.md) | Interpret cost-based plans and optimize large-scale, high-concurrency Snowflake analytical workloads. |
| 04 | [Advanced Governance, Privacy, and Lineage](04-advanced-governance.md) | Design policy-driven access, sensitive data classification, lineage, and evidentiary audit trails. |
| 05 | [GxP, Data Integrity, and Computer System Validation](05-gxp-validation-compliance.md) | Recognize regulated-use boundaries and plan appropriate electronic record controls and validation evidence. |
| 06 | [Resilience, Recovery, and Disaster Readiness](06-resilience-recovery.md) | Set RPO/RTO, distinguish historical recovery from regional failover, and test recovery plans. |
| 07 | [Apache Iceberg and Lakehouse Interoperability](07-iceberg-lakehouse.md) | Evaluate Iceberg table architecture, catalog ownership, and cross-engine data access. |
| 08 | [Airflow and Cross-Platform Orchestration](08-advanced-orchestration.md) | Coordinate reliable end-to-end pipelines across SAP, LIMS, MES, S3, and Snowflake. |
| 09 | [Cortex AI, Retrieval, and Snowflake ML](09-cortex-ai-ml.md) | Design governed AI features over analytical data without confusing model outputs with validated facts. |
| 10 | [Platform Engineering and Infrastructure as Code](10-platform-engineering.md) | Define enterprise templates for object lifecycle, environment promotion, identities and policy-as-code. |
| 11 | [FinOps, SLOs, and Incident Response](11-finops-observability.md) | Operate Snowflake using cost attribution, freshness SLOs, DQ telemetry and incident runbooks. |
| 12 | [AI-Agent-Assisted Data Platform Engineering](12-ai-agent-engineering.md) | Use coding agents to accelerate investigation while preserving reproducibility, least privilege and review. |

## Shared fictional laboratory

All companies, source feeds, people and sample records in this repository are **hypothetical Sanofi-inspired learning examples**, not claims about Sanofi's internal systems. Use synthetic or specifically approved datasets only. The consistent sandbox namespace is `PHARMA_DEMO`, with `RAW` (landing), `CORE` (validated), `MART` (consumption), and `OPS` (operational records). Some examples use additional domain schemas.

A minimal sandbox can be initialized from [`entry/04-data-types-tables.md`](../entry/04-data-types-tables.md). For role/warehouse prerequisites see [`entry/01-architecture.md`](../entry/01-architecture.md) and [`entry/02-objects-rbac.md`](../entry/02-objects-rbac.md). Statements may create billable resources or require privileges; execute only in an authorized disposable development environment. External services, cloud integrations and tools such as dbt/Airflow/Terraform must be set up separately.

**Business definition:** illustrative manufacturing yield = `(SUM(units_produced) - SUM(units_rejected)) / SUM(units_produced)`. Actual yield and release rules depend on approved source definitions, product specifications, batch lifecycle and QA decisions.


### Architecture review checklist

For each system write an Architecture Decision Record (ADR): context, alternatives, selected design, data ownership, sensitivity, failure modes, regulatory impact, measurable SLO, operational cost, assumptions, and migration path. Demonstrate a threat model and an approved validation strategy, not just a diagram. Enterprise examples are architectural proposals, not internal Sanofi knowledge.

**Interview checkpoint:** Defend the choice among Snowflake-native, Iceberg, Kafka/streaming, dbt and external orchestration while explaining data integrity and incident response.


## Capstone — Enterprise regulated pharmaceutical platform design

1. Create an architecture decision record for clinical/R&D, MES, LIMS and ERP source domains.
2. Define streaming/event identity, CDC, late arrivals, recovery, replication and per-domain SLO.
3. Create access and data-classification matrices with privacy and GxP applicability assessments.
4. Design IaC, secure AI-assisted investigation, audit evidence, cost attribution and incident runbooks.

### Definition of done

- [ ] GxP conclusions are use-specific and reviewed by quality/regulatory experts.
- [ ] RPO/RTO are proven in a recovery rehearsal.
- [ ] Agents cannot deploy unreviewed changes or access unauthorized data.
- [ ] All material figures have traceable sources and validation.

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
