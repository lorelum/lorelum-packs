---
anti_patterns:
  - description: Flattening HTTP failures into success-shaped responses or exposing raw exception details instead of the established status, headers, and safe error representation.
    id: backend-fundamentals.api.failure-response-loses-http-meaning
    name: Failure response loses HTTP meaning
    severity: warn
applies_when: an HTTP adapter or error handler must translate a known rejection or unexpected failure into an outward status, required headers, and a client-safe response without changing the established application protocol
id: backend-fundamentals.api.express-http-failures
severity: warn
stage: implementation
tech_stack:
  - http
  - security
title: Express Failures Through the Existing HTTP Contract
---

## When to apply

Apply when an HTTP adapter translates a rejection or unexpected failure into a response. Express the
outcome already decided by the operation; do not choose recovery behavior in the error serializer.

## Guidance

1. Read the existing status, header, media-type, and error-body contract. Retain its error shape and
   stable identifiers rather than introducing a new envelope or exposing exception messages.
2. Map the actual condition to its HTTP meaning: distinguish invalid input, missing credentials,
   refused access, missing resources, conflicts, and server failures where applicable. Preserve an
   established non-disclosure policy, including 404 for a concealed resource. Keep any body status
   consistent with the HTTP status.
3. Preserve required headers and method semantics. A 401 needs an applicable `WWW-Authenticate`
   challenge; a 405 needs `Allow`. HEAD errors have no response content. Do not turn failures into
   200 responses merely to simplify callers.
4. Return only safe, actionable details allowed by the contract. Exclude SQL, stack traces, internal
   paths, other tenants' data, and sensitive submitted values from bodies and headers. Never record
   credentials, tokens, or connection secrets, even in protected logs. Sanitize diagnostic causes
   before recording them through the existing restricted channel; retain an established correlation
   reference when available.
5. Keep unexpected failures generic externally. Add `Retry-After` only when it accurately describes
   the condition; neither that header nor error serialization makes replay safe.

Stop when failures retain truthful status and required headers, the established client protocol,
and safe outward details with secret-free diagnostics.

## Anti-pattern

A wrapper returns `200 {"items": [], "error": str(error)}` for every exception. Authentication failure
looks like an empty collection, while a database exception can expose connection credentials.

## Why

Clients and intermediaries interpret status and headers independently of JSON. Accurate semantics
support correct handling without making private exception details part of the API.

## Exceptions and boundaries

Preserve established GraphQL transport/application-error semantics rather than forcing REST-style
statuses onto every application error. A valid empty result remains successful. This Practice adds
neither retries nor a new error envelope, recovery policy, or telemetry system.

## Example

An API already uses `{"error":{"code":"...","message":"..."}}`. An order conflict returns 409 with
`order_state_conflict`; an unexpected database failure returns 500 with `internal_error`. The latter
uses the existing restricted diagnostic record only after removing credentials, tokens, and
connection secrets, without changing the response schema.
