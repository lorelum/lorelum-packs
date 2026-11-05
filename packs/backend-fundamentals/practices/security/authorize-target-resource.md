---
anti_patterns:
  - description: Treating a session, role, or resource ID as permission without applying the target's action and visibility policy.
    id: backend-fundamentals.security.authenticated-means-authorized
    name: A login check replaces resource permission
    severity: critical
applies_when: a caller needs permission for a particular record, attachment download, or bulk change across tenant, ownership, delegated sharing, or intentional public visibility
id: backend-fundamentals.security.authorize-target-resource
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Authorize the Action on the Target Resource
---

## When to apply

Apply when an endpoint selects an object ID or tenant for a read, download, or update. Authentication can already be correct while a caller reaches another user's record. Anonymous public/shared requests still follow the target's visibility policy; do not require login everywhere.

## Guidance

1. Identify the subject, action, target, and tenant where applicable. Use verified identity when required; otherwise represent the anonymous caller explicitly. Resolve membership, grants, visibility, and resource attributes from authoritative state, not submitted ownership claims.
2. Apply the actual allow rule and deny unmatched cases. It may permit public reads, sharing, ownership, delegated actions, or explicit cross-tenant access. Neither “logged in” nor “owner only” substitutes for that policy. Validate any sharing capability through the established mechanism.
3. Bind data selection to that decision. Scope collections by permitted visibility and tenant; use a scoped lookup or load only policy inputs before releasing protected fields or changing state. Random IDs and tenant IDs in URLs are not permission.
4. Cover alternate routes and every affected bulk target. Keep state-dependent permission and mutation consistent through the existing conditional write, transaction, or enforced data policy. A previous page view does not authorize a later change. Denials must not disclose protected data.

Stop when the intended policy controls data and effects on every supported path, including legitimate public or shared access. Reuse the existing authorization mechanism.

## Anti-pattern

A handler checks a session, then updates a document by primary key without checking tenant or edit permission. Changing the ID updates another tenant's document. Adding an owner-only condition still wrongly rejects explicitly shared editing grants.

## Why

A valid caller can lack permission for a particular action, while an anonymous caller can legitimately read public content. Connecting the action to authoritative target policy prevents identifier substitution without breaking intentional sharing.

## Exceptions and boundaries

Single-tenant systems need no artificial tenant abstraction. A global role grants cross-tenant access only where policy says so. Mandatory database policies can own enforcement when application context cannot bypass them. Private helpers may reuse a still-valid decision rather than repeat it; entry authentication remains separate.

## Example

An invoice endpoint permits anonymous downloads only for published public invoices. Private downloads require the invoice tenant's membership and a billing-read grant. A guessed ID never widens its query scope. An explicitly authorized accountant may read a shared invoice without owning it; a valid session alone cannot expose another customer's bill.
