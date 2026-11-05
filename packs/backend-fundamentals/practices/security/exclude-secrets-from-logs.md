---
anti_patterns:
  - description: Dumping requests, configuration, or credential-rich exceptions and assuming private logs or field-name redaction make them safe.
    id: backend-fundamentals.security.dump-sensitive-log-context
    name: Dumping sensitive diagnostic context
    severity: critical
applies_when: adding or changing logs, serializers, or error diagnostics that can observe credentials or other sensitive data
id: backend-fundamentals.security.exclude-secrets-from-logs
severity: critical
stage: implementation
tech_stack:
  - security
title: Exclude Secrets from Logs
---

## When to apply

Apply when a log statement, automatic request logger, serializer, or failure handler observes sensitive inputs or configuration. Choose the diagnostic event's necessary fields before choosing an object to serialize. Internal logs are still a disclosure surface.

## Guidance

1. Identify affected producers, including framework logs, child-logger bindings, dependency diagnostics, and error handlers. Trace secrets through headers, cookies, bodies, raw URLs, connection settings, and nested exception causes or attached responses.
2. Construct an allowlisted structured record: fixed event, operation, outcome, safe category, approved identifier, correlation ID, status, or duration as needed. Prefer route templates over sensitive URLs. Personal identifiers need a justified diagnostic purpose; JSON formatting does not make them safe.
3. Never log passwords, bearer tokens, session cookies, API keys, private keys, or credential-bearing connection strings, including inside free-text messages. Do not spread or stringify requests, responses, or configuration on sensitive paths.
4. Review exception serialization independently. Messages, stack messages, causes, request options, and attached responses can embed secrets. Map unreviewed errors to safe categories; retain stacks only through a reviewed representation excluding sensitive messages and data. An automatic `err` serializer is not proof of safety.
5. Use existing redaction and serializers as additional safeguards, including for automatic records. Cover nested fields, but do not expect field-path redaction to remove tokens embedded in strings. Preserve output encoding against forged records and establish that affected success and failure output excludes synthetic secret markers.

Stop when all affected producers emit useful, justified context without exposing direct inputs or nested exception secrets. Configure unsafe dependency diagnostics before enabling their output.

## Anti-pattern

A token-exchange handler omits the password from its normal log, then dumps a client exception containing an authorization header and a token in its message. Redacting the original request field does not touch either copy.

## Why

Logs spread across collectors, archives, and support tools with different access and retention. Allowlisted events preserve diagnostic facts without exposing credentials through arbitrary future object properties or error strings.

## Exceptions and boundaries

Keep security events such as failed authentication; omit their secrets, not their existence. Debug mode and private destinations are not exceptions. Authorized sensitive diagnostic capture needs separate purpose, access, and retention controls. Existing leaked credentials require the incident process; deleting a log statement does not revoke them or erase copies.

## Example

An account-link failure records `account_link_failed`, a request ID, and `upstream_unavailable`, not the provider exception. Automatic request logging uses the route template. Distinct markers in the header, body, and nested error are absent while correlation remains usable.
