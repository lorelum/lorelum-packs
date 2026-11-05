---
anti_patterns:
  - description: Copying request properties into a persistent model without an operation-specific writable-field projection.
    id: backend-fundamentals.api.spread-input-into-persistent-model
    name: Record edit permission exposes every model property
    severity: critical
applies_when: a request binder can assign client-supplied roles, ownership, tenant identity, or nested metadata outside the endpoint's permitted property set
id: backend-fundamentals.api.bind-only-declared-write-fields
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Bind Only Declared Writable Fields
---

## When to apply

Apply when a request body, form, or patch becomes persistent attributes. Legitimate record editors may still lack permission to assign its owner, role, balance, or lifecycle state. Decide field eligibility independently of whether submitted values look valid.

## Guidance

1. Declare writable properties for this operation and caller capability. Separate creation, editing, and privileged commands where contracts differ. Keep ownership, tenant, computed amounts, role grants, and workflow metadata server-owned unless policy explicitly permits assignment. Hidden controls and response schemas are not write policies.
2. Construct a dedicated input object or explicit projection before persistence binding. Include only declared fields; never spread the original body afterward or bind a full domain model first. Reject or omit undeclared input under the existing API contract. Denylists leave future properties exposed.
3. Apply the projection at every assignable depth. Allowing an address object does not allow arbitrary account metadata inside it. For patch protocols, check destination paths and any source paths that can copy protected values, including parent replacement. Prevent alternate encodings, bulk handlers, or nested relationship updates from bypassing the same write contract.
4. Obtain server-owned assignments from trusted context or computation. Preserve omitted fields on partial updates and distinguish clearing from absence under existing semantics. Validate accepted values, but do not mistake a well-typed role or valid owner ID for assignment permission.
5. Keep delegated and administrative changes explicit: select their authorized field policy before binding, or use the existing privileged command. Record edit permission does not expose every field; ordinary-editor restrictions must not accidentally prohibit deliberately supported transfers.

Stop when every persisted client assignment belongs to the selected write contract, including nested and alternate mutation paths, and undeclared fields cannot become writable through model growth.

## Anti-pattern

A profile handler loads an authorized account, validates known JSON types, then passes the entire body to the ORM update method. An added `role` property is accepted because it exists on the model; ownership checks on the target never restrict that assignment.

## Why

Persistence models expose more state than a particular client operation should control. A positive projection makes new model properties non-writable until deliberately included.

## Exceptions and boundaries

This is field eligibility, not a new validation layer or resource authorization scheme. An intentionally arbitrary metadata map may accept arbitrary keys within its isolated namespace, but must not merge them into privileged attributes. Read visibility does not imply write permission.

## Example

An account-edit command projects `displayName` and `address.city`, while obtaining the tenant from trusted context. Submitted `balance`, `ownerId`, or `address.role` never reaches assignment. A separate authorized transfer command accepts its declared new-owner field without widening the ordinary editing contract.
