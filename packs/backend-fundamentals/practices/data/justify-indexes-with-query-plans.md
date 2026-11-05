---
anti_patterns:
  - description: Adding indexes by column name or forcing index scans without representative evidence for the access path, execution work, and write cost.
    id: backend-fundamentals.data.index-by-column-guess
    name: Index by column guess
    severity: warn
applies_when: deciding whether to add, change, or remove a PostgreSQL index for a concrete query workload
id: backend-fundamentals.data.justify-indexes-with-query-plans
severity: warn
stage: implementation
tech_stack:
  - postgresql
title: Justify Indexes with Query Plans
---

## When to apply

Apply when an index decision is proposed for a specific access path, not automatically for every
slow SQL statement. Start with the workload the candidate should improve. An index enforcing an
integrity rule has a separate correctness justification.

## Guidance

1. Capture the actual query, representative parameters, frequency, data distribution, existing
   indexes, and performance goal. Retain its joins, predicates, ordering, and limits; a simplified
   lookup may produce a different plan.
2. Inspect `EXPLAIN` and, when execution is safe, `EXPLAIN (ANALYZE, BUFFERS)`. `ANALYZE` executes
   the statement, including mutations and invoked functions. Use a safe environment or an explicitly
   reviewed execution plan; rollback does not undo sequence increments or external side effects.
3. Locate expensive rows, loops, filtering, reads, sorts, or spills and important estimate errors.
   Planner costs are not milliseconds; repeated nodes require their loop counts. If statistics,
   query shape, or a non-database bottleneck explains the problem, reject the index hypothesis or
   hand off that cause rather than forcing an index change.
4. Compare a candidate matching the relevant keys, order, expression, or predicate with the baseline
   under comparable parameters and cache conditions. Judge execution work and latency, not an
   `Index Scan` label. Include storage, write maintenance, and overlapping-index costs.
5. Keep only a supported candidate and use the existing migration process for its live-table rollout.
   Ordinary creation blocks writes; concurrent creation has restrictions and failure handling.
   Retain the concise decision evidence with the change.

Stop with a justified keep, change, or no-index decision. If evidence identifies a different cause,
hand it off without expanding this task into a general slow-query investigation.

## Anti-pattern

A report reads most orders and spends its time sorting. Adding an index for every filtered column
and disabling sequential scans changes plan labels but adds write cost without the intended benefit.

## Why

An index offers another access path, not guaranteed improvement. Representative plans expose the
work it changes, while measured execution and maintenance costs establish whether that trade is useful.

## Exceptions and boundaries

Sequential scans can be appropriate for small tables or broad results. A uniqueness index remains
necessary even with few retrieval scans. When execution is unsafe, label estimated evidence honestly;
do not claim an observed speedup. Plans do not explain all network, application, or pool-wait latency.

## Example

For `WHERE customer_id = $1 ORDER BY created_at DESC LIMIT 20`, an engineer compares a candidate
`(customer_id, created_at DESC)` index across frequent and infrequent customers. It stays only if
reduced sort and read work meets the goal at acceptable write cost; a broad report may still scan.
