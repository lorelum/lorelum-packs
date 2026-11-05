---
anti_patterns:
  - description: Committing an event marker separately from its local effect, or claiming an absent marker without uniqueness, allowing crashes or concurrent workers to lose or repeat work.
    id: backend-fundamentals.data.detached-event-deduplication-marker
    name: The event marker and effect have different commits
    severity: critical
applies_when: redelivered events can repeat non-idempotent business changes in one transactional database and the handler needs durable deduplication across workers and restarts
id: backend-fundamentals.data.commit-event-deduplication-with-effects
severity: critical
stage: implementation
tech_stack:
  - postgresql
title: Commit Event Deduplication with Its Local Effects
---

## When to apply

Apply when redelivery, concurrent workers, restart, or lost acknowledgement could repeat a
non-idempotent local database effect. A completion marker must mean this consumer's effect committed,
not merely that a worker received the event.

## Guidance

1. Derive a stable event key from the authenticated source contract, with required provider,
   environment, account or tenant scope and consumer identity where needed. Use non-null components
   and immediate database uniqueness. A delivery-attempt ID, timestamp, or resource ID is not an
   interchangeable event identity.
2. In the effect's transaction, claim that exact key atomically, for example with PostgreSQL
   `INSERT ... ON CONFLICT (source_scope, consumer, event_id) DO NOTHING RETURNING event_id`.
   Only the transaction that inserted the marker performs the effect. A preliminary check or
   `SELECT ... FOR UPDATE` on an absent row does not reserve it.
3. Apply every covered local change through that same transaction and require its success conditions.
   Commit marker and effect together; any processing failure rolls both back. Never commit an
   in-progress marker first or add the marker after committing the effect.
4. Treat only the exact retained-key conflict as the duplicate-success outcome. Concurrent inserts
   wait for the competing transaction: its commit prevents another claim; its rollback leaves the
   key available. Timeouts, aborts, and unrelated constraint failures remain failures. Acknowledge
   completed work only after commit, not after an uncommitted observation or lock miss.
5. Route replay and recovery through the same rule. Retain durable evidence across restarts for the
   accepted redelivery horizon, including manual replay and backlog recovery. Before deleting older
   markers, preserve equivalent protection or explicitly restrict those replay paths.

Stop when one scoped key permits at most one committed local effect, failed attempts leave no
completion marker, and retained evidence protects redelivery after restart or lost acknowledgement.

## Anti-pattern

A worker commits a processed-event marker, then crashes before adding credits. Retry skips the
missing effect. Reversing those commits instead risks adding credits twice.

## Why

Uniqueness coordinates competing claims; a shared transaction couples the winning claim to completed
work. Durable evidence then distinguishes committed processing from a failed attempt.

## Exceptions and boundaries

An existing naturally idempotent operation or business constraint may already suffice. Different event
IDs can still describe one business action; deduplication does not solve ordering or lost updates.

Only same-database effects are covered. Remote calls, another database, and broker acknowledgement
do not roll back with this transaction. Do not claim remote exactly-once delivery or hold a transaction
around a network call as a substitute for the integration's actual idempotency contract.

## Example

A rewards consumer claims `(partner_account_scope, consumer, event_id)`, increments one account, and
commits both. A missing account rolls everything back. If commit succeeds but acknowledgement is lost,
redelivery finds the retained marker and skips the increment, including after a worker restart.
