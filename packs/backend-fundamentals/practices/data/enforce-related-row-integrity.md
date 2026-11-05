---
anti_patterns:
  - description: Treating an application parent lookup as sufficient while another writer can delete the parent or persist a child linked to the wrong tenant.
    id: backend-fundamentals.data.application-only-parent-check
    name: Application-only parent check
    severity: critical
applies_when: parent-child foreign keys, orphan prevention, or parent deletion policies must preserve valid stored relationships across application and maintenance writers
id: backend-fundamentals.data.enforce-related-row-integrity
severity: critical
stage: implementation
tech_stack:
  - postgresql
title: Enforce Related-Row Integrity
---

## When to apply

Apply when references must identify real parents across endpoints, imports, jobs, and maintenance SQL.
Decide the permitted relationship and parent-removal outcome, not just whether a lookup finds a parent.

## Guidance

1. Define the complete parent identity and whether the relationship is mandatory. Include tenant
   identity when child and parent must share it; two unrelated foreign keys can allow mismatched pairs.
2. Add a named database foreign key to the corresponding primary or eligible unique parent key.
   Match column order and compatible types; require `NOT NULL` for mandatory components. Keep database
   enforcement even when a preliminary lookup improves feedback.
3. Specify absence deliberately. PostgreSQL normally skips a composite foreign-key check when any
   referencing component is null. Use `MATCH FULL` for an optional tuple that must be entirely
   present or entirely absent. For a required tenant plus optional parent ID, keep tenant non-null
   and allow only the parent component to be absent under the intended relationship semantics.
4. Choose deletion and key-update actions from object lifetime rules. Reject removal when references
   must preserve independent objects; cascade only for dependents that should disappear. Nulling
   a reference must satisfy remaining constraints, without nulling a required tenant key.
5. Resolve existing orphans and mismatched tenant pairs under an approved repair or retention decision.
   Arrange migration enforcement so live writers cannot recreate them. Translate only the expected
   relationship violation into the agreed outcome; recover failed transaction state before more SQL.

Stop when all ordinary writers are subject to the relationship constraint, existing rows have a safe
transition, and parent removal or reassignment has an explicit, enforceable outcome.

## Anti-pattern

An API finds a project, then inserts a task after a maintenance job deletes that project. An import
also accepts another tenant's project ID. Neither path has database enforcement of the full relationship.

## Why

Foreign keys maintain matching identities on child changes and referenced-parent changes. Application
observations leave a concurrency gap and cannot constrain writers that bypass the observing handler.

## Exceptions and boundaries

Historical snapshots or external identifiers may intentionally survive without a live local parent;
do not contradict that contract. A valid reference neither authorizes callers nor guarantees one child
per parent or any children at all. Soft deletion leaves the parent row, so a foreign key alone does
not enforce an active-parent rule.

## Example

Tasks require `(tenant_id, project_id)` and reference the projects table's unique pair in that order.
The foreign key uses `ON DELETE RESTRICT` because tasks must be retained until explicitly resolved.
An import cannot pair one tenant with another tenant's project, and a concurrent project deletion
cannot silently strand a task. Caller permissions remain a separate decision.
