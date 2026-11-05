---
anti_patterns:
  - description: Writing a value computed from an earlier read without protecting the state or version that made the change valid.
    id: backend-fundamentals.data.unguarded-state-dependent-write
    name: Unguarded state-dependent write
    severity: critical
applies_when: concurrent inventory updates, stale document edits, or order cancellation need a quantity, version, or status predicate at the actual database update
id: backend-fundamentals.data.guard-state-dependent-writes
severity: critical
stage: implementation
tech_stack:
  - postgresql
title: Guard State-Dependent Writes
---

## When to apply

Apply when overlapping writers can invalidate stock reservations, versioned edits, or allowed status
changes. A transaction around separate reads and writes does not itself preserve the earlier observation.

## Guidance

1. State the precondition that must hold when the write takes effect. Include jobs and administrative
   paths among competing writers. Distinguish a stored-value increment from replacing an old snapshot.
2. Prefer one conditional statement for a single-row rule. Compute a delta from the stored column
   and include its quantity or status predicate in `WHERE`. For replacement edits, compare an
   expected version and advance it on success; every relevant writer must advance that version.
3. Inspect affected rows or `RETURNING` before reporting completion. A single-target operation requires
   exactly one match. Zero means the precondition or target did not match; return the established
   unavailable, stale-edit, or conflict outcome, not success.
4. For a decision spanning more than one row predicate, use transaction-scoped locking or isolation
   covering the invariant. Lock existing rows before deciding and retain protection through commit;
   acquire multiple locks consistently. Locking returned rows does not reserve missing rows or
   prevent every competing insert.
5. Keep business rejection distinct from deadlock or serialization failure. Roll back failed work.
   If existing policy permits restarting a failed transaction, repeat its reads and decision from
   fresh state; never remove the predicate, reuse a stale replacement, or blindly retry a rejected
   transition. Do not hold locks across user interaction or remote calls.

Stop when each successful mutation enforces its current-state precondition, competing writers share
that protection, and a lost race cannot be returned as a completed change.

## Anti-pattern

Two checkout requests read one available item, calculate zero, and save that absolute value. Both
orders are accepted. An ordinary read-committed transaction around each request did not make its
earlier observation exclusive.

## Why

Protected decisions coordinate at the database rather than trusting timing. In PostgreSQL Read
Committed, competing updates wait and recheck their predicates against updated rows. Cross-row rules
need protection covering that larger decision.

## Exceptions and boundaries

Unconditional last-writer-wins replacement can be intentional when losing an intervening edit is an
accepted contract. Uniqueness, required writes sharing a commit, and repeated-event handling are
separate guarantees. Repeatable Read is not a universal defense against cross-row anomalies, and
Serializable can reject transactions rather than silently making every attempt succeed.

## Example

A reservation executes `UPDATE inventory SET available = available - $1 WHERE sku = $2 AND available
>= $1 RETURNING available`, with an already validated positive quantity. One returned row permits
success; none yields the established unavailable outcome. The loser does not retry without the
quantity guard, and an earlier display of availability is never treated as a reservation.
