---
anti_patterns:
  - description: Trusting webhook fields or triggering business work before provider-specific signature verification succeeds.
    id: backend-fundamentals.security.webhook-payload-before-verification
    name: Process the webhook and check its signature later
    severity: critical
applies_when: receiving provider-signed webhook messages whose fields drive application decisions or effects
id: backend-fundamentals.security.verify-webhook-message-signatures
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Verify Webhook Message Signatures Before Trusting Events
---

## When to apply

Apply when adding or changing a signed webhook receiver or middleware that reads its body. Establish provider-specific authenticity before event claims drive application behavior. HTTPS, plausible JSON, and a signature header's presence do not authenticate an incoming message.

## Guidance

1. Resolve the provider and endpoint configuration from trusted routing and settings. Follow its verification contract: signed representation and headers, endpoint secret or public key, accepted versions, and required freshness checks. Do not transplant another provider's HMAC format or header names.
2. Preserve the original representation required by that verifier through adapters, middleware, and proxies. Stripe requires unmodified raw body bytes and `Stripe-Signature`; parsing and reserializing JSON does not reconstruct those bytes. Prevent general body middleware from replacing the required input.
3. Use the provider's maintained SDK verifier when available, with the endpoint's correct environment-specific material. Stripe's webhook signing secret is not its API key. Preserve supported freshness checks. Manual verification must implement the documented signed input, format, versions, comparison, and freshness semantics exactly.
4. Gate every event-driven effect on successful verification: record changes, actionable queues, external calls, and processed-event markers. Invalid signatures or malformed messages terminate processing with the provider-appropriate failure response. Repair body or configuration errors rather than accepting unsigned input.
5. Dispatch the event returned or accepted by the successful verifier, not a previously parsed copy. Retain event-type, account, resource, and business checks after authenticity is established. Keep verification secrets out of responses and logs.

Stop when event-driven actions require successful provider-specific verification using the required original input and correct endpoint configuration.

## Anti-pattern

A handler activates a subscription from parsed `invoice.paid` JSON, then logs signature failure. Moving the check earlier still fails if it verifies reserialized JSON instead of Stripe's signed bytes. Accepting unsigned messages to restore service removes authenticity altogether.

## Why

Attackers can imitate event fields without possessing signing material. Verification binds the required message input to the configured sender; performing it after effects cannot undo trust already granted to forged claims.

## Exceptions and boundaries

SDKs can combine parsing and verification; parsing itself is not forbidden when original input survives and no unverified field is trusted. A signature-terminating intermediary needs an authenticated, provenance-preserving handoff, not a client-set verification flag. Valid signatures do not establish novelty, ordering, permitted targets, or current state. Retries still need existing duplicate-delivery handling; signatures are not stable deduplication keys.

## Example

A Stripe route retains raw bytes and calls its SDK event-construction verifier with `Stripe-Signature` and that endpoint's signing secret. Failure exits before subscription or ledger changes. Only the returned verified event reaches the existing invoice handler and its account and duplicate-delivery rules.
