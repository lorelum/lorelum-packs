---
anti_patterns:
  - description: Rechecking or normalizing the same established fact in every layer without a new input, mutation, caller, or failure mode, creating divergent rules and rejecting valid work.
    id: agentic-coding.implementation.defensive-check-per-layer
    name: A defensive check in every layer
    severity: warn
applies_when: a data path is gaining or repeating validation, normalization, authorization, integrity checks, or defensive wrappers, and the agent must decide which boundary actually needs each check
id: agentic-coding.implementation.validate-at-the-owning-boundary
severity: warn
stage: implementation
tech_stack:
  - agentic-coding
title: Validate at the Boundary That Owns the Fact
---

## When to apply

Apply when adding a guard or reviewing repeated checks along a data path. The question is whether
this location can encounter a distinct invalid state, not whether validation is generally useful.
Choosing what to do after a known failure is a separate recovery decision.

## Guidance

1. Trace the relevant input and its handoffs only. Name the fact each existing check establishes,
   where it first becomes trustworthy, and the component responsible for keeping it true.
2. Assign each check to its owning boundary: validate external shape at ingress, apply business
   rules where their state is owned, normalize into the agreed representation once at its owning
   conversion, and pass the established result through ordinary internal calls. An internal type
   should express that result where practical; a cast alone establishes nothing.
3. Before adding another check, identify what could invalidate the earlier result at this location:
   another entry point, untrusted deserialization, a transformation, concurrent mutation, expired
   authorization, or an independently required protection. Keep the check only when such a condition
   exists — a new function, layer, or package name is not itself a new boundary.
4. Compare the extra rejection, maintenance, latency, and recovery cost with the distinct failure
   the check prevents.

Stop with a clear owner per fact and only the checks the actual path needs; a short explanation in
the existing design or review is enough.

For a controlled internal producer, trust the postconditions its contract and real production path
establish. A process hop or local HTTP call alone does not make the payload independently
untrusted. Keep decoding, authentication, protocol matching, and request–response correlation where
needed; they do not imply repeating the complete business schema. A representation conversion
justifies checks for failures it introduces, not every failure imaginable for an arbitrary object.

Do not make protection against another internal implementation violating its types and contract a
default production responsibility. Prefer types, producer contract tests, and necessary development
assertions. A mock that invents an output the producer cannot emit does not justify a downstream
guard. Preserve tests for reachable external input, real corruption, and supported recovery.

Ordinary reads, status calls, and encoding should not add whole-store, model, or executable scans,
repeated hashing, deep copies, or recursive schema walks for states already excluded upstream.
Once JSON has been decoded and validated without introducing new values, checking for cycles,
Symbols, or arbitrary object prototypes establishes no new fact. Remove duplicate work before
adding validation caches, trusted flags, locks, or generic wrappers to make it cheaper. Retain
adapters that own a real conversion or lifecycle; distinct boundaries need not share one validator.

Preserve external input validation, model/native integration quality checks, publication integrity,
transaction and snapshot consistency, authentication, process ownership, and regeneration of
corrupt derived caches. Change an accepted checking contract through its normal change process
before removing its required checks.

## Anti-pattern

An HTTP adapter parses a quantity into a positive integer. A service, domain helper, and repository
each accept that typed value, but independently parse, clamp, and reject it because each module is
meant to be robust. Their limits later drift: the adapter accepts a bulk order while a hidden helper
silently clamps it. Every layer has tests, yet a valid request is changed after acceptance and the
caller cannot tell which rule defines the order.

## Why

A check adds value when it establishes a fact that can actually be false there. Repeating an already
established fact creates more rule owners, failure paths, and maintenance without improving the
guarantee. Distinct boundaries can still need distinct protections even when they inspect the same
field.

## Exceptions and boundaries

An API and CLI may each validate raw input before sharing a typed domain call. A database uniqueness
constraint remains necessary when a preflight check cannot prevent concurrent inserts. A worker
deserializing a queued message has a new input boundary; an unchanged object passed to a private
helper does not. Encoding for HTML output solves a different problem from parsing input.

Use confirmed deployment and caller facts when sizing authorization. An internal service behind an
enforced gateway need not recreate an identity platform for every helper; a bypassable gateway or
cross-tenant operation can require its own authorization decision. Retain separately justified
defense in depth and explicit policy requirements. Before removing existing checks, establish their
callers and contract; this Practice does not authorize changing accepted behavior by itself.

## Example

An inventory service accepts orders through an HTTP endpoint and a CSV import. Each adapter parses
its raw quantity and creates the same typed command. The domain service checks current available
stock, and the database atomically prevents competing reservations from overselling. The agent keeps
those distinct checks and removes a proposed third string parser in the repository: no string or
unchecked caller can reach it. A stock change still gets checked at the mutation boundary, while the
parsed quantity is passed through without another normalization policy.
