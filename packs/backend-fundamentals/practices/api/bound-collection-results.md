---
anti_patterns:
  - description: Loading or returning an entire growing collection, or slicing only after full materialization, while claiming that a page-size parameter bounds the request.
    id: backend-fundamentals.api.unbounded-collection-materialization
    name: Unbounded collection materialization
    severity: warn
applies_when: a collection endpoint can return a growing number of records and the agent must decide its result limit, ordering, and continuation behavior before fetching or serializing them
id: backend-fundamentals.api.bound-collection-results
severity: warn
stage: implementation
tech_stack:
  - http
  - postgresql
title: Bound Collection Fetches and Define Continuation
---

## When to apply

Apply when implementing or changing a list or search response whose collection can outgrow a small,
established bound. Decide how much each request retrieves and how callers continue through results.

## Guidance

1. Read the existing navigation contract and expected collection size. Set a default page size and
   enforced maximum, with explicit handling for missing, invalid, and excessive sizes. An omitted
   limit must not mean unlimited retrieval.
2. Apply the limit at retrieval, before full materialization or serialization. Bound included child
   collections that can grow independently; a parent page does not bound unlimited children. Retain
   the same access scope and filters throughout traversal.
3. Define deterministic ordering with a unique tie-breaker. Describe what additions, deletions, or
   reordering between requests mean for callers. Stable ordering alone does not provide a snapshot.
4. Match continuation to navigation needs. Offset or page-number pagination is legitimate for small
   or stable lists and direct page jumps. Keyset cursors can suit deep sequential traversal when
   ordered keys support it. Encoding an offset as a cursor does not remove offset cost or movement
   under concurrent writes.
5. Return the established next link, cursor, or page metadata so callers can distinguish completion
   from truncation. Match continuation to the ordering and filters. A bounded extra-row fetch can
   detect another page; include exact totals only when required and their cost is acceptable.

Stop when retrieval enforces the default and maximum, ordering and concurrent-change behavior are
explicit, included collections are bounded, and continuation identifies the end without fetching all rows.

## Anti-pattern

`GET /events?limit=50` loads every matching event, sorts the array, and slices 50 rows. Its response
looks bounded while memory and transferred query results still grow with the complete history.

## Why

A response slice cannot undo unbounded retrieval. Fetch limits constrain materialization; ordering
and continuation make traversal predictable without implying an unchanged dataset.

## Exceptions and boundaries

A genuinely small fixed set can be returned whole. Preserve accepted streaming or asynchronous
export contracts rather than silently truncating them. Row limits do not bound row width, sort work,
expensive filters, or total query cost. Neither offset nor cursor pagination alone guarantees a snapshot.

## Example

An event feed defaults to 50 rows, permits at most 200, and orders by `occurred_at DESC, id DESC`.
A keyset continuation carries both values and retains tenant filters. A 50-row page fetches at most
51 rows, emitting a next cursor only when the extra row exists. Later inserts ahead of the cursor
appear on refresh, not in a promised snapshot.
