---
anti_patterns:
  - description: Reusing a caller-specific response across users because the shared cache distinguishes only the URL.
    id: backend-fundamentals.api.share-personalized-cache-entry
    name: A successful private read becomes a public cache hit
    severity: critical
applies_when: configuring HTTP response storage or reuse where cookies, identity, tenant selection, or representation headers can change the returned content
id: backend-fundamentals.api.isolate-personalized-cache-responses
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Isolate Personalized Responses from Shared Caches
---

## When to apply

Apply when changing HTTP caching for an endpoint whose representation depends on the caller. Correct origin permissions cannot protect later requests answered without reaching that handler. Decide storage eligibility before freshness.

## Guidance

1. Classify actual bodies, headers, redirects, and errors, including anonymous, authenticated, and tenant variants. Trace shared caches that can answer before application code runs; cookies and `Set-Cookie` alone do not prohibit HTTP caching.
2. Exclude personalized responses from shared storage with unqualified `Cache-Control: private` when browser storage is acceptable. Use `no-store` when neither private nor shared HTTP caches should retain them. `no-cache` permits storage but requires successful validation before reuse. Set policy on every relevant response path.
3. Keep genuinely public representations cacheable with an intentional freshness policy. Do not attach personalized fields to that representation. Requests carrying `Authorization` have special shared-cache restrictions, but directives including `public`, `s-maxage`, or `must-revalidate` can permit reuse; do not add them indiscriminately to authenticated responses.
4. Include all representation-selecting request headers in `Vary`, preserving existing members, and align intermediary keys with those inputs. `Vary` cannot name an internal principal or tenant variable. Deliberate protected shared caching needs trusted identity partitioning, permission enforcement before every hit, and a defined revocation policy; a client-asserted tenant header or URL-only key is insufficient. Otherwise keep it private or unstored.
5. Account for already-stored entries when changing policy: expire or purge unsafe shared variants through the deployed cache mechanism. New response headers do not recall old fresh entries. Keep revalidation and validators within the same representation boundary.

Stop when each response has an explicit storage decision and no shared hit can cross its intended audience or representation boundary.

## Anti-pattern

An edge rule caches `/dashboard` by path for five minutes. The origin correctly selects the signed-in customer's account, but the next customer's request receives that first response without executing authorization.

## Why

Fresh cache hits can bypass origin logic entirely. Storage restrictions and correct selection keys prevent reuse from widening the audience of an otherwise legitimate response.

## Exceptions and boundaries

Public assets need no blanket cache ban. `private` controls storage location, not encryption or application-account isolation inside one browser. `no-store` is not a privacy guarantee against malicious caches, browser history, or separate application stores. Neither directive replaces target authorization.

## Example

A billing summary uses `no-store`; a non-sensitive personal preference read allows private storage. The anonymous product catalog remains shared-cacheable, with `Vary: Accept-Language` because language changes its representation. Signing in does not silently add account prices to that public variant.
