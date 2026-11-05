---
anti_patterns:
  - description: Closing shared resources while accepted work can still use them, or waiting indefinitely without a defined termination outcome.
    id: backend-fundamentals.lifecycle.close-dependencies-before-work
    name: Closing dependencies before their users finish
    severity: warn
applies_when: implementing or changing shutdown for a backend process that accepts work and owns resources used by that work
id: backend-fundamentals.lifecycle.drain-work-before-closing-resources
severity: warn
stage: implementation
tech_stack:
  - service-lifecycle
title: Drain Work Before Closing Resources
---

## When to apply

Apply when wiring termination signals, deployment hooks, server closing, or cleanup callbacks. Order
intake, accepted work, and dependency cleanup around what the process actually owns. Closing its
listening socket alone does not establish that all its work has finished.

## Guidance

1. Identify existing intake paths, accepted work, and owned dependencies. Include background tasks,
   scheduled callbacks, consumers, or streams only when present. Define completion to include the
   writes or acknowledgements belonging to each work unit.
2. Set a shutdown deadline within the deployment's termination allowance, reserving cleanup time.
   Define cancellation and the incomplete or forced-termination outcome if work cannot stop safely.
   A fixed sleep is not a drain, and timing out a wait does not stop its underlying operation.
3. Withdraw readiness where available and stop timers or consumers from starting more work. Choose
   the HTTP intake cutoff under the deployment policy: reject new work immediately, or keep serving
   during a bounded routing-propagation allowance only while dependencies remain usable and the
   total shutdown deadline still permits completion and cleanup. At the cutoff, reject new work
   locally even if routing still sends it. Repeated triggers must join one shutdown operation.
4. Await accepted work within the budget while leaving its dependencies usable. Use the framework's
   lifecycle for work it tracks and join existing work outside that lifecycle explicitly. Give
   long-lived connections their established completion or cancellation policy; an aborted socket
   does not prove a database write stopped.
5. After users have settled or stopped, close and await owned resources in dependency order, keeping
   logging available while needed. At the deadline, use the defined termination outcome instead of
   closing resources beneath still-running code and calling that a graceful drain. Do not report
   interrupted work as successfully completed.

Stop with an explicit intake-stop, bounded work-completion, and dependency-release sequence for
this process, including a truthful deadline outcome.

## Anti-pattern

A signal handler runs pool closure and server closure concurrently, then exits after a sleep. An
accepted invoice request reaches its final query after its pool has already closed.

## Why

Dependencies must outlive their users. Stopping intake bounds the remaining work, awaiting that work
protects accepted operations, and a deadline prevents an indefinite shutdown. Each addresses a
different failure; none is replaced by a delay.

## Exceptions and boundaries

A process without background work needs no task registry; one without readiness can stop intake
directly. Use existing lifecycle facilities, not another platform. A crash or hard kill can bypass
hooks. Interrupted mutations have uncertain outcomes, so shutdown alone does not establish safe
replay or durable delivery.

## Example

A Fastify API with only HTTP work withdraws routing and reaches its chosen intake cutoff before
starting `app.close()`, while leaving its pool open. `preClose` can run before requests finish; the
pool closes in `onClose`, after the HTTP drain, with plugin ordering respected. Its coordinator
reserves cleanup time and uses the deployment's termination path if the bounded drain cannot finish,
without claiming interrupted requests succeeded or promising a zero-downtime rollout.
