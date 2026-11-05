---
anti_patterns:
  - description: Exempting state-changing browser requests because they use POST or JSON despite accepting automatically attached credentials.
    id: backend-fundamentals.security.exempt-cookie-writes
    name: A cookie-authenticated API is assumed immune to CSRF
    severity: critical
applies_when: adding or changing a state-changing browser endpoint, its accepted authentication modes, CSRF protection, or invoking client flow
id: backend-fundamentals.security.protect-cookie-authenticated-writes
severity: critical
stage: implementation
tech_stack:
  - backend-fundamentals
title: Protect Cookie-Authenticated Writes Against CSRF
---

## When to apply

Apply when a browser action uses automatically attached credentials, especially session cookies. Include forms, JavaScript requests, login or session establishment, and mixed-auth endpoints. Determine the actual credential path rather than inferring safety from JSON, a route name, or JWT usage.

## Guidance

1. Identify all accepted credentials and whether the browser attaches them without an explicit application-supplied authorization header. Include login CSRF: a forged login can bind a victim's browser to an attacker's account before the victim has an authenticated session.
2. Use the framework's maintained CSRF defense on susceptible paths, with documented middleware ordering and enforcement before effects. Remove unjustified exemptions; do not disable checks or replace them with homemade comparisons to make a client request work.
3. Complete the legitimate client flow. For token-based protection, obtain the framework-generated token and submit it through the documented field or header. The server must validate its expected session or cookie binding, not merely token presence. Keep tokens out of URLs, logs, and unrelated-origin requests.
4. Configure intended origins, HTTPS handling, and cookies for the real deployment. Do not broaden trusted origins or credentialed CORS to bypass failures. POST, JSON, CORS response policy, and cookie flags are not substitutes for CSRF protection. Retain safe read semantics, authentication, and target authorization.
5. Justify exemptions against every accepted authentication mode. A bearer-only route must not fall back to ambient credentials. Ensure the chosen defense accepts legitimate client requests and rejects missing or forged proof before mutation, using the framework's real enforcement path.

Stop when ambient-credential actions and their client flows use the maintained defense, including login and cookie fallback paths, with narrowly justified exemptions.

## Anti-pattern

A settings form becomes JSON but retains session authentication. Its CSRF failure is “fixed” with a broad exemption and reliance on POST and an `HttpOnly` cookie. The browser still supplies credentials; the intended-action check is gone.

## Why

A session identifies the caller but does not establish intent for a request initiated by another site. Framework CSRF defenses supply the missing request-context check; payload formats and token-presence checks do not.

## Exceptions and boundaries

Explicit bearer-header-only APIs without ambient authentication need no mandatory browser CSRF token. A JWT in a cookie remains ambient. Separately authenticated webhooks need sender verification, not browser tokens. Supported origin or Fetch Metadata defenses can replace token schemes under their documented trusted-origin and missing-header policy. Same-site siblings are not automatically trusted; CSRF defense does not repair XSS or missing authorization.

## Example

A Django address-change request keeps session authentication and `CsrfViewMiddleware`, sending the framework token in `X-CSRFToken` only to the same-origin endpoint. Missing proof prevents the update. A separate bearer-only integration route has no session fallback and retains its narrow exemption.
