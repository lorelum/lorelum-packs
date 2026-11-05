---
anti_patterns:
  - description: Attaching a requested business mutation to GET or HEAD because the action is easy to express as a link or query parameter.
    id: backend-fundamentals.api.mutation-behind-safe-read
    name: Mutation behind a safe read
    severity: warn
applies_when: a GET or HEAD route, link, preview, or query parameter is about to cancel, consume, approve, acknowledge, or otherwise change business state as part of the requested action
id: backend-fundamentals.api.preserve-safe-read-semantics
severity: warn
stage: implementation
tech_stack:
  - http
title: Keep Requested Mutations Out of Safe Reads
---

## When to apply

Apply when choosing or changing behavior behind an HTTP read route, link, or preview. Decide whether
retrieval requests a business change, not whether the implementation performs any writes at all.

## Guidance

1. State the requested effect independently of the route name: retrieve a representation or change
   business state. Include actions selected by query parameters and helpers invoked during rendering.
2. Keep GET and HEAD free of requested business mutations, including cancellation, approval,
   acknowledgement, and one-time-token consumption. Put the action behind an explicitly unsafe
   operation, such as an established POST action or an appropriate resource-update method.
3. Follow the server path used by link previews, prefetches, crawlers, and repeated retrievals. None
   may execute the business action just by fetching the URL. Hiding a link or changing a button does
   not repair a mutating read handler.
4. Separate safety from idempotence. Repeated cancellation may leave the same final state, but its
   first execution still changes business state. Choose mutation replay behavior separately; an
   idempotent action does not become suitable for GET.
5. Move existing business actions to the explicit mutation path and update every caller. Do not
   retain the old GET as a mutating compatibility alias. Keep incidental retrieval behavior, such as
   access logging or ordinary cache population, when it does not perform the requested business action.

Stop when GET and HEAD retrieve without requesting business mutations, actions use explicit mutation
paths, and callers no longer depend on mutating reads.

## Anti-pattern

`GET /orders/42?cancel=true` cancels an order before rendering confirmation. Calling it idempotent
does not help when a mail scanner follows the link and cancels the order before the customer acts.

## Why

Safe methods invite automated retrieval. Putting business actions behind them lets previews and
prefetches exercise authority intended for deliberate user actions.

## Exceptions and boundaries

Safe does not mean side-effect-free or publicly accessible: incidental logs and caches can remain,
and access checks still apply. A sensitive search may use POST to keep input out of the URI. This
Practice does not choose mutation retry, idempotency-storage, or CSRF policies.

## Example

`GET /orders/42/cancellation` displays the authorized customer's order and a confirmation form without
cancelling or consuming the action token. The form submits `POST /orders/42/cancellation`, which
checks action authority and cancels. A later GET shows the result; HEAD reads metadata without
executing the action.
