---
anti_patterns:
  - description: Reflecting any supplied Origin while permitting credentials so unrelated sites can read authenticated responses.
    id: backend-fundamentals.api.reflect-arbitrary-browser-origin
    name: Credentialed response access trusts every requesting site
    severity: critical
applies_when: configuring cross-origin browser access to an HTTP endpoint, including credential mode, preflight handling, or dynamic response access headers
id: backend-fundamentals.api.allow-only-intended-browser-origins
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Allow Only Intended Browser Origins
---

## When to apply

Apply when another origin's browser scripts need response access. Decide permitted sites and credential mode. A CORS error does not establish that the server rejected the request or prevented effects.

## Guidance

1. Declare the accepted origins and credential mode for the endpoint. Match complete serialized origins, including scheme and port, against an application-owned set; do not use substring matches or arbitrary reflection. Treat `null` as untrusted unless deliberately required by a narrowly understood client policy.
2. For credentialed access, return the single accepted origin in `Access-Control-Allow-Origin` and `Access-Control-Allow-Credentials: true`. Never combine credentials with an origin wildcard. For intentionally public, noncredentialed access, `Access-Control-Allow-Origin: *` is legitimate. Do not enable credentials globally to repair one integration.
3. Handle preflight through the existing CORS mechanism without requiring a session cookie on `OPTIONS`. Approve intended origins, methods, and request headers; use explicit methods and headers for credentialed access. Preflight permits the browser to proceed, not the operation itself. Authenticate and authorize the actual request normally.
4. Apply matching access headers to actual responses, including errors intended for the accepted client. Preflight headers alone do not expose the final response. Expose additional headers only as needed. For unaccepted origins, omit the grant rather than reflecting their value.
5. When headers vary by request origin, merge `Origin` into `Vary`, including denied or absent-origin variants, and preserve existing members. If an intermediary caches preflight responses, distinguish other request inputs that change its decision or disable that caching. Do not confuse browser preflight caching with ordinary HTTP response caching.

Stop when intended browser clients receive only their declared response access, arbitrary origins receive no grant, and preflight and actual responses implement the same policy.

## Anti-pattern

Middleware copies any `Origin` into the allow-origin header and enables credentials. A hostile site's script can then read a signed-in user's account response; replacing `*` with reflection did not create a trust boundary.

## Why

The browser uses response headers to decide which requesting script can access a response. Credentialed reflection extends that access to attacker-controlled sites.

## Exceptions and boundaries

Same-origin clients need no CORS grant. CORS does not authorize server operations, protect cookie-based writes against CSRF, or restrict nonbrowser callers that forge or omit `Origin`. Cookie and browser privacy policies still apply even to allowed clients. Response-access variation does not isolate personalized cached bodies.

## Example

A portal permits credentialed reads from `https://console.example` only, returns that origin plus `Vary: Origin`, and retains authorization on the actual endpoint. A separate public exchange-rate feed uses the wildcard without credentials. Neither route treats its CORS policy as a substitute for write protection.
