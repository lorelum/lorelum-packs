---
anti_patterns:
  - description: Fetching relations once per parent, preloading an unbounded graph, or dropping child access predicates merely to use a prefetched cache.
    id: backend-fundamentals.data.per-row-relation-fetch
    name: One related lookup per rendered row
    severity: warn
applies_when: implementing or changing a list, serializer, template, or batch operation that reads related records for multiple parent rows
id: backend-fundamentals.data.batch-required-relations
severity: warn
stage: implementation
tech_stack:
  - backend-fundamentals
title: Batch the Relations the Result Actually Needs
---

## When to apply

Apply when rendering or processing several parents accesses related records for each one, including
lookups hidden in serializers, templates, properties, and deferred fields. Improve the bounded
result's fetch plan without changing which records the consumer may receive.

## Guidance

1. Trace the actual consumer fields and relation accesses. Use the existing query-capture mechanism
   to distinguish per-parent lookups from fixed overhead such as authentication or a page count.
2. Establish the parent page or batch in the database, preserving filters, ordering, and limits.
   Fetch related data only for those parents; do not materialize the whole collection before slicing.
3. Join suitable single-valued relations and prefetch or bulk-load multi-valued relations. Preserve
   every original child predicate, including tenant, viewer, visibility, and soft-delete conditions,
   as well as ordering and required fields. A batching change must return equivalent results;
   removing a filter to hit the cache is not an optimization.
4. Match the consumer to that exact fetch shape. In Django, use explicit `select_related()` paths
   and a filtered `Prefetch` with a named `to_attr` list where appropriate. Read that list rather
   than calling a filtered manager again. Equivalent bulk queries must retain the same restrictions
   when associating children with parents.
5. Bound child volume too. Use an aggregate for a count or summary rather than loading every child;
   preserve required child limits and continuation semantics without silently truncating output.
   Compare the full consumer's results and retrieval growth with populated, filtered relations.

Stop when the bounded result is equivalent, its child access rules remain intact, and relation
queries no longer grow accidentally once per parent without replacing them with unbounded loading.

## Anti-pattern

A serializer filters comments by viewer visibility. Its replacement prefetch loads all comments and
removes that filter so the cache is used, reducing query count while exposing hidden records.

## Why

Batching removes repeated network and database work. It is useful only when the result stays correct;
fewer queries do not compensate for changed access control, multiplied rows, or excessive memory.

## Exceptions and boundaries

A small conditional lookup may be cheaper left alone. Chunked jobs may issue a fixed query set per
chunk, not per entire job. Separate prefetch queries do not guarantee a common snapshot; preserve
existing consistency requirements. No universal one-query target applies.

## Example

A paginated project list joins owners and prefetches its bounded active-reviewer subset into
`visible_reviewers`, retaining the original tenant, viewer, visibility, and soft-delete predicates.
The serializer reads that list. It obtains unbounded comment counts through an aggregate, not comment
bodies, so both the response and its access boundaries remain unchanged.
