---
anti_patterns:
  - description: Copying a quota without defining its key, protected operation, deployment scope, or failure behavior.
    id: backend-fundamentals.security.unscoped-rate-limit
    name: A copied limiter replaces an enforced policy
    severity: warn
applies_when: implementing or changing a request limit for an abuse-prone or costly operation, including its key, proxy trust, or counter store
id: backend-fundamentals.security.enforce-scoped-rate-limits
severity: warn
stage: implementation
tech_stack:
  - backend-fundamentals
title: Enforce Rate Limits in Their Intended Scope
---

## When to apply

Apply when an operation's abuse exposure or resource cost requires request admission limits, such as login attempts or expensive reports. Define that operation's policy rather than applying one sample quota everywhere.

## Guidance

1. Name the operation, budget owner, counted attempts, burst or window, and resumption rule. Derive authenticated-principal keys from verified identity, not submitted IDs or raw secrets. Keep unrelated operation budgets separate.
2. For login attack budgets, an unverified normalized target-account identifier can be a key, but is not proof of identity or a trusted principal. Use bounded policy that prevents attacker-driven victim lockout. For network keys, use configured proxy trust and supported address handling; arbitrary forwarding headers are not authoritative. Account for shared NATs.
3. Match counter storage to promised scope. Memory is process-local and can reset on restart. Shared-worker quotas need the existing gateway or supported shared store with atomic counting, expiry, and separate namespaces. Independent counters cannot promise a strict global allowance.
4. Admit before protected expensive work, retaining authentication needed to establish trusted keys. Use the maintained limiter's concurrency-safe mechanism. Define store-outage failure or bounded fallback behavior explicitly under service policy: an outage is not quota exhaustion and must not become an accidental unlimited fallback or a claim of continued quota enforcement.
5. Reject actual over-budget requests before effects, using the service's error format and HTTP 429 with truthful retry timing when known. Distinguish unavailable admission infrastructure from exceeded quota rather than reporting its failure as 429.

Stop when the operation has a bounded policy whose key, counting, deployment scope, rejection point, and outage behavior match its legitimate traffic contract.

## Anti-pattern

A replicated service uses process-local per-IP counters, sees every request as the load balancer, then trusts raw `X-Forwarded-For` to fix it. Customers block one another while attackers choose fresh counters.

## Why

Operation, key, time bounds, and shared state jointly define a limit. A forgeable key permits bypass; an overly broad key causes collateral denial. Storage failure cannot establish that a caller consumed a budget.

## Exceptions and boundaries

Best-effort single-process memory limits are valid when their reset and scope are acceptable. Gateway enforcement need not be duplicated when all relevant ingress passes through it. Request rate does not bound concurrency, request size, query cost, or billing, and is not a denial-of-service guarantee.

## Example

A login flow budgets attempts against a normalized submitted account name separately from its trusted-proxy source budget. That account key grants no identity. Its bounded throttling policy avoids permanent victim lockout, and counter-store failure follows the configured service-unavailable policy instead of fabricating quota exhaustion.
