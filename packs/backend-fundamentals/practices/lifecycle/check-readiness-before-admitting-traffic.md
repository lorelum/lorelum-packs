---
anti_patterns:
  - description: Advertising a listening process as ready before required initialization, or restarting healthy processes because a remote dependency is temporarily unavailable.
    id: backend-fundamentals.lifecycle.process-alive-means-ready
    name: A live process is treated as ready for traffic
    severity: warn
applies_when: a service starts or recovers while a router must decide whether this instance can handle its advertised requests
id: backend-fundamentals.lifecycle.check-readiness-before-admitting-traffic
severity: warn
stage: implementation
tech_stack:
  - backend
  - service-lifecycle
title: Check Readiness Before Admitting Traffic
---

## When to apply

Apply when a listener can open before required configuration, data, or connections are usable, or an instance must leave and rejoin routing after a dependency failure. Decide whether it can serve its declared contract now; a live process or open port does not establish readiness.

## Guidance

1. Define the minimum prerequisites of requests routed to this instance. Include required initialization and indispensable dependencies, not every remote integration. Keep optional-feature behavior within its accepted contract; do not invent empty-success fallbacks to make a probe green.
2. Start non-ready. Advertise only after required initialization, schema compatibility, and owned serving resources are actually usable. Connect existing routing to that state; binding a socket or starting an initialization promise is not completion.
3. Keep readiness observation cheap, bounded, and free of business effects. Observe initialization state and the required capabilities with appropriate timeouts. Do not run migrations, create records, or repeatedly perform expensive warming from a probe. Treat failure or an unknown required condition as non-ready rather than returning a cached success indefinitely.
4. Separate traffic admission from process recovery. An otherwise healthy process can remain alive while an essential dependency is unavailable. Do not copy remote-dependency checks into liveness and cause restart loops. Where the platform distinguishes startup from liveness, allow justified initialization time without claiming that elapsed sleep proves readiness.
5. Withdraw and restore admission through existing controls as conditions change. Account for propagation: handlers must reject work they cannot safely accept. Check slow startup, essential-dependency loss, recovery, and optional-feature failure against the intended routing outcomes.

Stop when the instance receives its advertised traffic only while the required serving conditions hold, and a dependency outage cannot masquerade as healthy service or automatically become a process-restart storm.

## Anti-pattern

A service returns 200 from `/ready` as soon as the HTTP listener opens. Its required database initialization is still pending, so newly routed requests fail. The replacement probe checks every remote integration in liveness and restarts all instances when an optional analytics service is down.

## Why

Readiness is an admission decision; liveness is a recovery decision. Keeping them distinct avoids routing work to an uninitialized instance and avoids destroying healthy processes that can recover without restarting.

## Exceptions and boundaries

A stateless service may need only completed local initialization. Optional features need not remove an otherwise usable instance when their declared failure behavior is preserved. No Kubernetes deployment, new health framework, or universal timeout is required. Readiness is an observation, not a guarantee that the next request cannot fail; graceful shutdown and dependency release remain separate decisions.

## Example

A quote service stays out of routing until its pricing rules load and its required store is usable. Store loss withdraws readiness while the live process waits for recovery. A failed optional metrics exporter does not block quotes or trigger restarts; the existing shutdown path separately stops intake and drains accepted work.
