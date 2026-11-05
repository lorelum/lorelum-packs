---
anti_patterns:
  - description: Returning an acquired connection only on success, reusing a broken or unrolled-back client, or closing a shared pool while work still needs it.
    id: backend-fundamentals.data.leaked-acquired-connection
    name: Acquired connection has no safe return path
    severity: warn
applies_when: backend code explicitly acquires a database connection or changes its transaction or cleanup paths
id: backend-fundamentals.data.return-acquired-connections
severity: warn
stage: implementation
tech_stack:
  - postgresql
title: Return Acquired Connections
---

## When to apply

Apply when code checks out a database client or changes the success, failure, early-return, or
cancellation paths around that checkout. Decide who owns the client until it is safe to reuse or
must be discarded. Pool capacity planning is a separate decision.

## Guidance

1. Reuse the process's existing pool. Prefer its acquire-and-release helper for an independent query
   when that helper provides the needed semantics; do not create a pool per request.
2. After each successful checkout, establish one scoped cleanup path using the existing resource
   API or `try`/`finally`. Cover success, exceptions, early returns, and cancellation. Keep all
   statements in one transaction on that checked-out client.
3. Commit completed transactions or roll back unfinished ones before returning the client. If rollback
   fails or the driver reports a broken connection, discard it through the supported driver API;
   never put unsafe transaction or connection state back into circulation.
4. Hold the client only for work that requires it. An abandoned acquisition wait can still deliver a
   client later: use the driver's cancellation mechanism or arrange cleanup for that late result.
   A caller timing out does not itself return a connection.
5. Return or discard individual clients after their work. Close the shared pool only through its
   lifecycle owner after work using it has settled or stopped, not at an ordinary request's end.

Stop when every successful acquisition reaches a safe return or discard on every exit path, and
shared-pool closure cannot run ahead of its users.

## Anti-pattern

An insert throws before `release()`. Each failed request consumes another pool slot, so the proposed
fix increases the pool maximum instead of repairing ownership.

## Why

A pool can reuse only the connections callers give back. Cleanup must also preserve reusable state;
returning an aborted transaction or broken client passes one request's failure to the next caller.

## Exceptions and boundaries

A short-lived maintenance command may own one direct connection and close it reliably without a pool.
Use the deployed driver's rules for transaction errors, discard, cancellation, and session state.
Reliable release does not establish deployment capacity or bound acquisition waits; those belong to
the connection-budget decision.

## Example

A transaction helper acquires one client, commits after both writes succeed, and rolls back on
failure. Its `finally` returns the client unless rollback failed, in which case it discards it.
The request never calls `pool.end()`; the process closes that pool after its users drain.
