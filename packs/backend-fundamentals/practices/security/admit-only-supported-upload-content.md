---
anti_patterns:
  - description: Publishing or processing uploaded bytes because their client MIME type, extension, or storage key looks acceptable.
    id: backend-fundamentals.security.trust-upload-type-labels
    name: Treating an upload label as content proof
    severity: critical
applies_when: untrusted file bytes need type verification, streaming size enforcement, bounded decoding or extraction, or quarantine before public release
id: backend-fundamentals.security.admit-only-supported-upload-content
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Admit Only Supported Upload Content
---

## When to apply

Apply when deciding whether received files may enter application processing or become available to users, including direct-upload completion. Define what content the feature supports and what must succeed before release. A safe destination name and a verified uploader do not establish acceptable bytes.

## Guidance

1. Declare the required file-type allowlist and intended use: download, inline display, or transformation. Reject unnecessary active formats and archives rather than applying one universal sanitizer. Specify the content checks and any malware or document-disarm controls required by this risk and format; do not claim a clean scan proves harmlessness.
2. Enforce a finite byte limit while receiving the stream, before unbounded buffering or storage growth. Do not rely only on Content-Length or a check after the complete body is loaded. For direct uploads, constrain intake through supported storage controls and keep completion unavailable to consumers until actual object size and content checks pass.
3. Verify actual bytes against the supported type using signature checks and maintained, securely configured parsers as appropriate. Client MIME and extension are hints, not evidence; matching magic bytes alone is insufficient. Bound parser work, memory, dimensions or decoded output as relevant. If archives are explicitly supported, constrain actual expanded bytes, entries, nesting, and processing time during extraction; compressed size or declared totals are not sufficient.
4. Keep unaccepted originals in quarantine, unavailable to serving and downstream jobs. Promote only the exact checked object or approved derived output; prevent replacement between checking and release. Reject unsupported or malformed input and keep validation failures, timeouts, and required-scanner outages from becoming accepted content. Remove rejected temporary data under the existing retention policy.
5. Serve accepted files without server execution or upload-controlled configuration. Derive response type from the admission result, not client labels. Choose attachment delivery or an appropriately isolated serving origin when active content could execute in a browser; constrain inline rendering to supported content and prevent MIME sniffing.

Stop when unsupported, oversized, unchecked, and failed uploads cannot reach consumers, and admitted files have a serving policy matching their actual use.

## Anti-pattern

An avatar endpoint accepts anything labeled `image/png`, saves it under a UUID, and immediately exposes the original inline. The random key prevents name selection but neither verifies an image nor constrains browser interpretation.

## Why

Untrusted bytes can exploit processors, exhaust resources after expansion, or execute when served. Admission must cover both what is received and what later processing or delivery makes possible.

## Exceptions and boundaries

A binary-artifact repository can intentionally retain executables as non-executing downloads under its contract. Archives need controls only when supported. No antivirus vendor, object store, or new upload platform is mandatory. Destination ownership and resource permissions remain separate decisions.

## Example

A profile service accepts JPEG and PNG, checks streamed size, decodes within pixel and time budgets, and publishes only its re-encoded image. A failed decode leaves no public avatar; the quarantined original never becomes the fallback response.
