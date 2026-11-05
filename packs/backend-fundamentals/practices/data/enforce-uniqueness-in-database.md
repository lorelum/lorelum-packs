---
anti_patterns:
  - description: Treating an existence check as a uniqueness guarantee while concurrent requests or another writer can persist the same business key.
    id: backend-fundamentals.data.check-then-insert-uniqueness
    name: Check-then-insert uniqueness
    severity: critical
applies_when: a preflight SELECT cannot reserve a business key against a racing INSERT, or updates, imports, and migrations must obey the same scoped uniqueness rule
id: backend-fundamentals.data.enforce-uniqueness-in-database
severity: critical
stage: implementation
tech_stack:
  - postgresql
title: Enforce Uniqueness in the Database
---

## When to apply

Apply when a persisted business key must be unique across every writer, such as one membership per
organization and user. An earlier lookup can improve feedback but cannot reserve an absent row
against a competing write.

## Guidance

1. Define the exact key and scope, including tenant or parent identity where appropriate. Preserve
   the approved equality rules for case, normalization, soft deletion, and null values.
2. Enforce that key with a named `UNIQUE` constraint or an appropriate unique expression or partial
   index. Required components also need `NOT NULL`. PostgreSQL normally allows multiple nulls;
   choose supported `NULLS NOT DISTINCT` semantics only when absent values must conflict.
3. For existing data, resolve duplicates under an approved merge or retention decision, preserving
   dependent references. Arrange enforcement so live writers cannot recreate duplicates during the
   transition. Account for the deployed version's index-build and locking restrictions.
4. Attempt the write and translate only the expected constraint violation into the agreed conflict
   outcome. Preserve unrelated database failures. Use `ON CONFLICT` only when ignoring or updating
   the existing row is the operation's actual contract, not as a generic duplicate repair.
5. In PostgreSQL, a unique violation inside a transaction leaves that transaction aborted. Roll back
   the transaction, or roll back to a savepoint established before the write, before executing more
   SQL. Do not catch the error and query the existing row on the still-aborted transaction.

Stop when database enforcement covers every writer, existing records have a safe migration path,
and concurrent attempts produce the defined outcome without duplicate records or invalid recovery SQL.

## Anti-pattern

Two requests both find a workspace slug unused and then insert it. The endpoint trusts its check,
and an import bypasses it altogether. Several rows now represent a supposedly unique slug.

## Why

Database uniqueness coordinates conflicting writes at insertion or update. A separate application
observation leaves a race and cannot constrain writers that do not use the same endpoint.

## Exceptions and boundaries

Similar-looking fields need not be unique: repeated display names may be valid. Partial indexes must
reflect the approved business rule, not invent soft-delete behavior. Partitioning and version limits
can constrain enforcement. Uniqueness does not make other writes atomic or prevent unrelated lost
updates.

## Example

Memberships require `org_id` and `user_id`, with a named unique constraint on their pair. Creation
attempts the insert directly. If that constraint rejects a competing request, the transaction helper
rolls back before the service returns the existing-membership outcome or performs a follow-up query.
A violation of any other constraint remains an error, and imports obey the same database rule.
