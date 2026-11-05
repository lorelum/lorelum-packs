---
anti_patterns:
  - description: Relying on a UI check or bypassable gateway while the protected backend entry accepts unauthorized requests.
    id: backend-fundamentals.security.ui-only-access-control
    name: Protecting the screen instead of the entry
    severity: critical
applies_when: adding or changing a protected backend entry or relying on middleware or a gateway to enforce its access requirements
id: backend-fundamentals.security.enforce-protected-entry-access
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Enforce Access at the Protected Entry
---

## When to apply

Apply when exposing a nonpublic route, download, or backend operation, or relying on upstream protection. Decide where required identity and operation-level permission are enforced on every reachable entry. Permission for particular records is a separate target-authorization decision.

## Guidance

1. Declare whether the entry is public, authenticated, or requires additional permission. Identify all routes to the protected behavior, including alternate endpoints and direct origin access. Do not make new routes public by omission.
2. Use the supported server-side session or credential verifier to establish identity. A submitted user ID, role header, or decoded-but-unverified token is not verified caller context. Apply the required operation-level permission before releasing data or starting work.
3. Establish that the enforcing middleware or gateway cannot be bypassed. If the gateway owns authentication, the origin must accept only its trusted traffic and caller context must not be forgeable. Protect a reachable bypass or close it; merely deploying a gateway is insufficient.
4. Terminate unauthorized requests before the protected effect. Missing, invalid, or unestablishable required identity must not fall through to the handler. Keep alternate entries under the same access contract rather than assuming callers arrive through the intended screen.

Stop when every reachable protected entry has an enforcing owner and denial prevents data disclosure or effects. Do not build another authentication stack inside private helpers that receive established context without crossing a new trust boundary.

## Anti-pattern

An administration screen hides an export button from ordinary users, but its endpoint accepts a submitted `userId`. A direct request obtains the export without a session. The UI check never runs on the path that releases the data.

## Why

Clients can construct requests without using the UI. Access control protects an operation only when it executes on the serving path before data or effects escape. A bypassable gateway leaves that boundary unenforced.

## Exceptions and boundaries

Public health summaries or login initiation can intentionally allow anonymous access. Machine callers can use service identities. Network location alone does not establish identity or permission. A fully enforced gateway can own credential verification, but does not replace separately required defense in depth or authorize every submitted resource ID.

## Example

A report export requires a valid session and `reports.export`. Its gateway replaces incoming identity headers with trusted context, and deployment blocks direct origin access. Both export routes use that enforcement before creating work; the handler separately checks the report's tenant and resource policy.
