---
anti_patterns:
  - description: Letting a client filename, path, or object key select an upload destination or overwrite target.
    id: backend-fundamentals.security.client-controlled-upload-destination
    name: The client filename becomes the storage path
    severity: critical
applies_when: an upload handler, direct-upload grant, or completion path chooses the filesystem path or storage key for client-supplied bytes
id: backend-fundamentals.security.generate-upload-storage-keys
severity: critical
stage: implementation
tech_stack:
  - http
  - security
title: Generate Upload Storage Keys on the Server
---

## When to apply

Apply when assigning an upload's persistent destination, including existing direct-upload flows. Decide who selects the stored object. A display filename is not authority to choose a filesystem path, storage namespace, or replacement target.

## Guidance

1. Trace destination construction and later client handoffs. Identify submitted filenames, paths, extensions, tenant labels, and explicit keys. Removing traversal strings or taking a basename does not prevent a harmless-looking name from colliding with another object.
2. Select the root or bucket from server configuration and generate the key through the existing server-side identifier facility. Use an opaque random identifier unless an established naming contract requires otherwise. Derive namespace prefixes from authoritative application state under the access policy, not unchecked owner or folder strings.
3. Keep the original filename as separate display metadata. Omit extensions unless needed; choose required extensions through the accepted server-side file-type policy, not the submitted filename or MIME header. Preserve display encoding and use established create-only or collision handling to avoid unintended replacement. Local destinations must remain under a safe configured root.
4. Persist the authorized file-record-to-key association. For direct uploads, grant the exact server-selected destination. Completion must resolve the pending record under its access policy, not accept an echoed key as ownership proof. Resolve later replacement and deletion through authorized records too.

Stop when the client cannot choose another namespace, destination, or overwrite target, and upload handoffs preserve the server-selected mapping.

## Anti-pattern

A handler writes to `uploadRoot + filename`, then a patch strips separators. Another endpoint signs any submitted storage key. Both still allow selection of an existing file's destination; traversal filtering did not establish server control.

## Why

Destination control can permit traversal, collision, or replacement of another object's bytes. Separating generated keys from display names removes that input from storage selection. A persisted mapping prevents completion or later operations from reopening the choice.

## Exceptions and boundaries

Server-derived deterministic keys, including content-addressed or intentionally replaceable assets, can satisfy an established contract with explicit namespace, collision, and replacement rules. Random names are not authorization: uploads, downloads, signed grants, replacement, and deletion still require permissions. Keys do not establish content safety; size limits, validation, malware handling, processing, and serving remain separate. Do not introduce a new upload platform merely to change key ownership.

## Example

A support portal stores `quarterly-report.pdf` as display metadata while assigning `case-files/<internal-case-id>/<generated-id>`. Its existing direct-upload grant targets that key. Completion resolves the permitted pending attachment; submitting another object's key cannot change the association. Download still checks case permission.
