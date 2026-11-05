---
anti_patterns:
  - description: Approving a submitted URL string while redirects or unchecked DNS resolution can send the backend to another destination.
    id: backend-fundamentals.security.unchecked-fetch-destination
    name: Checking the URL but not the connection
    severity: critical
applies_when: a backend fetches a URL or network location influenced by client input or remotely supplied content
id: backend-fundamentals.security.constrain-server-fetch-destinations
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Constrain Server Fetch Destinations
---

## When to apply

Apply when implementing URL previews, remote imports, callbacks, or another server-initiated fetch whose destination can be influenced externally. Decide which destinations this operation may contact, including indirect redirects and resolved addresses. Authenticating the submitting caller does not establish that destination permission.

## Guidance

1. Declare the integration contract: permitted schemes, hosts, effective ports, and address ranges. Prefer server-owned destination configuration or an explicit allowlist for known integrations. If arbitrary public destinations are required, define public-network access explicitly and exclude metadata, loopback, private, link-local, and other disallowed ranges using maintained IPv4/IPv6 address classification. Permit legitimate internal integrations narrowly rather than opening all internal networks.
2. Parse through maintained URL and address libraries compatible with the actual HTTP client. Compare normalized scheme, complete hostname, effective port, and canonical IP values; do not use substring or prefix matching. Reject unsupported schemes, ambiguous parser interpretations, and unneeded userinfo. Rebuild fixed integration URLs from trusted configuration instead of forwarding unnecessary submitted components.
3. Resolve names and apply the address policy to all returned IPv4 and IPv6 candidates. Bind the connection to a validated address without a second unchecked resolution, preserving the intended hostname for HTTP and TLS certificate verification. Every connection attempt, including an existing retry or fallback path, must obey the policy. Reject when resolution or enforcement cannot establish an allowed destination.
4. Disable automatic redirects unless required. When following one, resolve its target against the current URL and repeat the full scheme, host, port, and address decision before connecting; bound the redirect chain. Enforce corresponding egress restrictions through the existing network or proxy controls, including the proxy's actual resolution and connection path. An application-side lookup followed by an unconstrained proxy fetch is not binding.

Stop when each possible outbound connection is restricted to the operation's permitted destinations, including redirects and DNS changes. A parsed URL alone is not this guarantee.

## Anti-pattern

A preview handler checks that a string begins with `https://`, resolves its hostname once, then lets the client resolve again and follow redirects. A public address passes the check; the actual connection reaches a local administrative service.

## Why

The backend can reach services unavailable to the submitting caller. Redirects, alternate address representations, and changed DNS answers can move the connection beyond a superficially acceptable URL. Connection-time enforcement protects the destination actually contacted.

## Exceptions and boundaries

Internal fetches can be intentional under a specific integration policy. CORS and inbound authentication do not constrain server egress. Destination controls do not establish response-content safety, remote authorization, or an unlimited-fetch resource budget.

## Example

A document importer accepts public HTTPS locations on port 443. Its client connects only to approved resolved addresses and rechecks each permitted redirect. A separate payroll integration allows one configured internal service; that exception does not apply to document imports.
