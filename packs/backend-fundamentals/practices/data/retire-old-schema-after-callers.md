---
anti_patterns:
  - description: Removing or renaming a schema object because current source no longer references it while deployed callers or live writes still require the old contract.
    id: backend-fundamentals.data.contract-schema-before-deployment
    name: Contracting the schema before callers have moved
    severity: critical
applies_when: dropping, renaming, or replacing a database column or table used by deployed applications, workers, scripts, or integrations
id: backend-fundamentals.data.retire-old-schema-after-callers
severity: critical
stage: implementation
tech_stack:
  - postgresql
title: Retire Old Schema After Its Callers
---

## When to apply

Apply when a migration removes an object or makes its old name unavailable. Even a data-preserving
rename breaks an older binary's next query. Gate retirement on deployed readers, writers, and live
migration behavior, not just the current checkout.

## Guidance

1. Identify actual readers, writers, database dependencies, running versions, long-lived jobs, and
   permitted rollback versions. Inspect generated SQL: an application-field rename may keep its
   physical mapping, while a generated drop-and-add may destroy values rather than rename them.
2. Choose a coordinated maintenance cutover only if every caller can stop. When versions overlap,
   expand first and establish the authoritative value and how every live writer maintains the
   replacement. Retire old-only writers or use a deliberate existing synchronization mechanism;
   deploying one new writer does not upgrade the rest.
3. Backfill through the existing migration process with bounded work and a concurrency rule that
   preserves live edits. Do not copy values fetched earlier over newer writes. Establish historical
   coverage and agreement with transition-time writes before switching readers; non-null replacement
   rows alone do not prove correctness.
4. Deploy readers and writers that no longer need the old object, including fallback reads. Gate
   contraction on deployment inventory and relevant runtime evidence: old processes and jobs must be
   gone, the backfill complete, and incompatible rollback versions excluded. A quiet interval does
   not establish that an infrequent caller migrated.
5. Apply contraction with reviewed dependencies and a live-workload lock plan. PostgreSQL
   `ALTER TABLE` generally takes `ACCESS EXCLUSIVE` unless documented otherwise; metadata-only does
   not mean nonblocking. Do not use `CASCADE` to bypass unexplained dependencies. Remove temporary
   dual writes, fallback paths, and synchronization objects once their users are gone.

Stop before contraction if any deployed or supported rollback caller still needs the old object,
or migration coverage and live-write consistency remain unestablished. Finish removal of temporary
compatibility paths once the gate is met.

## Anti-pattern

A release drops a populated column and adds its renamed replacement before old replicas finish.
A true rename would preserve values but still break those replicas and the unmigrated nightly job.

## Why

Schema changes and application deployment are not atomic together. Replacement needs historical and
ongoing values; removal needs evidence that every supported caller has left the old contract.

## Exceptions and boundaries

A verified unused object may be dropped directly. A maintenance window stopping every reader and
writer can permit a coordinated data-preserving rename. A field mapped to the same physical column
may need no database rename. No universal dual-write mechanism is required. Recreating a dropped
column or reverting code does not restore lost values.

## Example

A shipment API and dispatch job move from `delivery_note` to `courier_note`. During overlap both
writers maintain both columns transactionally. After old-only writers leave, a row-locked backfill
copies current values. Readers switch after coverage and consistency checks; fully deployed callers
then remove old access. With incompatible rollback images excluded, contraction drops the old column
and retires the temporary dual path.
