---
anti_patterns:
  - description: Committing required local database changes separately and reporting success when a later failure can leave only part of the business operation persisted.
    id: backend-fundamentals.data.partial-business-write
    name: Partial business write
    severity: critical
applies_when: one business operation changes multiple records in the same database and those changes must succeed or fail together
id: backend-fundamentals.data.group-atomic-database-writes
severity: critical
stage: implementation
tech_stack:
  - postgresql
title: Group Atomic Database Writes
---

## When to apply

Apply when several local database writes form one all-or-nothing result, such as an order and its
required lines. Choose that business boundary rather than wrapping an entire request or a set of
independently acceptable operations in a transaction by default.

## Guidance

1. Name the local invariant and the writes required to preserve it. Check whether one statement
   already supplies the needed atomicity; group only changes whose partial persistence is invalid.
2. Use the repository's transaction API and pass its transaction-bound client or context through
   every participating call. `BEGIN`, all grouped writes, and `COMMIT` must use the same checked-out
   connection; separate pool queries do not join it automatically.
3. Commit only after every required write and expected affected-row check succeeds. On failure or
   an unmet precondition, roll back the group and propagate failure. Do not swallow a failed
   statement and describe the partial operation as complete.
4. Return the connection through its lifecycle owner after transaction cleanup. Discard it through
   the driver if rollback fails or the connection is broken, rather than returning unsafe state.
5. Keep the transaction limited to the required database work. Do not wait for user input or a remote
   payment, email, or storage operation while holding it open, or describe those effects as covered
   by the local rollback.

Stop when every required local write shares one commit or rollback, required preconditions are
checked, and no partial result is reported as the completed operation.

## Anti-pattern

An order insert commits through a pool helper, followed by separately committed line inserts. The
third line fails, but the handler returns the order ID as success. Downstream work sees an order
that was never completed.

## Why

A transaction makes its grouped changes persist and become visible as a unit. This guarantee applies
only to the statements actually executed in that transaction, not to surrounding application calls
or unrelated connections.

## Exceptions and boundaries

A lone statement already runs in a PostgreSQL transaction. Independent import items may deliberately
commit separately when partial progress is accepted. Savepoints can isolate genuinely optional work,
not turn required failures into success. Sequence values may be consumed on rollback.

Atomicity alone does not prevent stale read-modify-write races or coordinate remote effects. Preserve
any existing concurrency controls and state those limits without requiring a new isolation or remote
coordination design for this grouping change.

## Example

A stock adjustment increments quantity with `UPDATE ... SET quantity = quantity + $1 ... RETURNING
quantity`, requires one returned row, and inserts its audit record on the same transaction client.
If the audit insert fails, both changes roll back and the caller receives failure. A later
notification is not part of this database guarantee.
