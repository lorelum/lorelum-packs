---
anti_patterns:
  - description: Choosing a per-process pool maximum without accounting for simultaneous processes and replicas, rollout overlap, proxy limits, or bounded acquisition waits.
    id: backend-fundamentals.data.unbudgeted-deployment-connections
    name: Pool maxima exceed the deployment budget
    severity: warn
applies_when: changing database pool settings, process counts, replica limits, rollout overlap, or database proxy topology
id: backend-fundamentals.data.budget-deployment-connections
severity: warn
stage: implementation
tech_stack:
  - postgresql
title: Budget Deployment Connections
---

## When to apply

Apply when choosing pool limits or changing how many database-using processes can coexist. A small
local pool is not a deployment-wide limit. Decide how the deployment fits its allocated database
capacity and how callers behave when that allocation is busy.

## Guidance

1. Establish the database's usable connection allocation after other services, jobs, migrations, and
   administrative or reserved capacity. Treat `max_connections` as a ceiling, not evidence that the
   database can efficiently execute that many active queries.
2. Count simultaneous pools across processes per instance, maximum replicas, background workers, and
   rolling-deployment overlap. For direct connections, sum each pool maximum times its maximum
   simultaneous process count; keep the total within the allocation.
3. If the deployment uses a multiplexing proxy, budget its client connections separately from its
   database-facing pool. Use the deployed mode's limits and session-affinity rules rather than
   assuming every client consumes one backend connection or that multiplexing is unrestricted.
4. Set pool maxima and bounded acquisition waits from the allocation and operation's latency needs.
   Saturation must produce the agreed timeout or overload outcome, not an unbounded queue or an extra
   connection outside the pool. Confirm the timeout covers queued acquisition; query timeouts and
   abandoned promises do not necessarily stop that wait.
5. Revisit the calculation when topology or pool limits change. Use observed occupancy, waits, query
   duration, and database load to distinguish exhausted allocation from inefficient work before
   enlarging pools.

Stop when maximum simultaneous demand fits an explicit allocation and saturated acquisition has a
bounded outcome under the deployed driver and proxy configuration.

## Anti-pattern

Each replica allows twenty connections because that looked modest locally. Autoscaling and rollout
overlap multiply that setting beyond the service's allocation, while requests queue indefinitely.

## Why

Every pool competes for shared database capacity. Bigger pools can move waiting into an overloaded
database without improving throughput; deployment arithmetic exposes that multiplication before it
becomes a production limit.

## Exceptions and boundaries

There is no universal pool size, CPU formula, or connection-memory estimate. A direct maintenance
connection still consumes capacity but needs no pooling layer. This Practice budgets demand and
waiting; safe return or discard of acquired clients is a separate ownership responsibility.

## Example

An API receives 72 direct connections after other users are accounted for. At most six API processes
coexist, including rollout overlap. A maximum of ten per pool permits sixty connections, with a
bounded acquisition wait inside the request budget. Increasing replicas requires revisiting those
facts rather than copying the same pool setting.
