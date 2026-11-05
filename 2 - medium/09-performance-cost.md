# Query Performance and Cost Optimization

> **Level:** MEDIUM · **Module 09/10** · **Goal:** Diagnose scans, pruning, spills and warehouse utilization before changing compute size.

## 1. Concept and mental model

- **Micro-partition pruning:** efficient filtering depends on data distribution and optimizer metadata.
- **Query Profile:** measure expensive scans, joins and spills.
- **Clustering/search optimization:** workload-specific paid features; evaluate marginal benefit.
- **Warehouse sizing:** performance and credit trade-off, including queueing and concurrency.

**How to reason about it:** Start with filters on physically useful time/plant columns, remove unnecessary columns, and inspect pruning. Tune only after verifying identical answers.

## 2. Pharmaceutical data-engineering scenario

An analyst queries the last seven days of sensor QC results but a broad transformation scans years of history.

The example is a **synthetic Sanofi-inspired** design, not an assertion about Sanofi internal infrastructure, specifications, vendors or production records. Where the topic touches clinical, quality or manufacturing data, actual production use requires the organization's approved source definitions, access controls and regulatory assessment.

## 3. Practical implementation

The following is a focused working pattern. A few examples intentionally show a configuration sketch rather than a runnable service integration; read the comments and fulfill prerequisites first. Unless otherwise stated, run SQL after creating the `PHARMA_DEMO` schemas from the entry curriculum.

```sql
EXPLAIN USING TEXT
SELECT plant_id,product_id,
       SUM(units_produced) total_units
FROM PHARMA_DEMO.RAW.BATCH_EVENTS
WHERE event_ts >= DATEADD('day',-7,CURRENT_TIMESTAMP())
GROUP BY plant_id,product_id;

SELECT query_id,total_elapsed_time,bytes_scanned,
       partitions_scanned,partitions_total
FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY(
  DATEADD('hour',-1,CURRENT_TIMESTAMP()),CURRENT_TIMESTAMP(),100))
WHERE query_text ILIKE '%BATCH_EVENTS%'
ORDER BY start_time DESC;
```

**Execution walkthrough**

1. Read the Snowflake object names, business grain and access assumptions before executing anything.
2. Execute the example only in a disposable DEV environment after satisfying any stated prerequisites.
3. Inspect results against the validation criteria; do not infer correctness from an exit status alone.
4. Repeat with a deliberate duplicate, null, failure or late-arriving input and document the expected behavior.

## 4. Validate the result

**Acceptance check:** Record credits, p50/p95 latency, scanned bytes, partitions pruned, and result equality before/after each optimization.

Keep at minimum the source record count, authoritative business keys, run timestamp/ID, relevant SQL query IDs or Git SHA, DQ outcome and the reviewer of any exception. For regulated use, follow the approved evidence retention and change-control procedure; these learning exercises are not a validated system by themselves.

## 5. Failure modes and production safeguards

- Scaling warehouse before finding a cross join.
- Applying functions to filter columns without testing pruning effects.
- Optimizing a query from a cold tiny fixture only.

**Engineering decision:** First reduce work, then tune physical access paths, then select compute shape and scale.

**Operational checks:** confirm least-privilege access, idempotency/replay behavior, source-to-target reconciliation, latency/freshness expectations, cost ownership and an actionable alert or runbook. Not every topic needs every control, but omissions should be justified.

## 6. AI-assisted engineering workflow

Use an approved AI coding assistant with a narrow, read-only or sandboxed scope. Example prompt:

> In the hypothetical pharmaceutical project, implement or review **Query Performance and Cost Optimization**. The requirement is: Diagnose scans, pruning, spills and warehouse utilization before changing compute size. Use only synthetic examples and the documented `PHARMA_DEMO` contract. Produce the smallest reviewable SQL/Python/configuration diff, tests that fail on invalid or duplicate input, a source-to-target reconciliation plan, and expected outputs. Identify permissions, cost, freshness, privacy and rollback risks. Never invent proprietary Sanofi details, query unauthorized records, or deploy to production.

**Human review gate:** evaluate the proposed code against the acceptance check above; compare source semantics, query plans/tests as relevant, and explicitly approve any access changes or production operations.

## 7. Exercise

Benchmark weekly batch-yield SQL with 1M synthetic rows; record baseline and proposed optimization.

**Deliverable:** a short implementation note including assumptions, a code diff, tests and observed results. For a production-shaped exercise, also include a failure-injection test and recovery steps.

## 8. Interview questions

**Q: When would you apply query performance and cost optimization?**  
A: An analyst queries the last seven days of sensor QC results but a broad transformation scans years of history.

**Q: What is the key implementation trade-off?**  
A: First reduce work, then tune physical access paths, then select compute shape and scale.

**Q: What would break after retry, duplicate events, late data, or a schema change?**  
A: Use the described validation and failure checks, collect reproducible evidence, and explain authorization and recovery assumptions.

**Q: Which tests and monitoring signals would block publication?**  
A: State explicit acceptance criteria and test against the intended data/business grain with a restricted deployment identity.

## 9. Memorize

> **Prune data before paying for more compute.**

## 10. Official reference

- https://docs.snowflake.com/en/user-guide/ui-query-profile

---

**Navigation:** Previous: [Security and Governance Fundamentals](08-security-governance.md) · Next: [CI/CD and Operational Observability](10-cicd-observability.md) · [All medium topics](README.md)
