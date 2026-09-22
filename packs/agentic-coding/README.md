# Agentic Coding

`agentic-coding@0.6.0` is an unreleased, tool-neutral Knowledge Pack of 34 decision-focused Practices for AI agents doing software engineering: clarifying goals, authority, acceptance, and non-goals; controlling scope and stop conditions; keeping decision-bearing sets (plans, acceptance criteria, checklists, standards) from growing past what they must decide; making proportionate implementation and validation choices; keeping validation, permission, and confirmation checks from blocking the acceptance path; reviewing subtractively before commit; and handling handoffs, delegated agents, delivery, recovery, and context loss with trustworthy evidence. The latest published release remains `0.5.1` until a new immutable ref and Registry entry exist.

Canonical English · [简体中文](./i18n/zh-CN/README.md)

The Practices cover requirements, planning, implementation, testing, verification, review, delivery, correction, and context recovery. Each entry targets one decision point and is intended to remain useful when retrieved alone; consumers should retrieve only the few entries relevant to the current work and moment.

## Scope

Use this Pack to improve engineering judgment around scope, risk, source authority, investigation boundaries, evidence, implementation drift, review findings, completion claims, corrections, long-session checkpoints, delegation context, and handoffs. Combine it with the domain Practices and project requirements that define what the system itself must do.

## Validation and recovery decisions

Use [Validate at the Boundary That Owns the Fact](./practices/implementation/validate-at-the-owning-boundary.md)
when the same data is parsed, normalized, authorized, or checked again across layers. It asks what
new invalid state is possible at each location and keeps external input, business-state, concurrency,
and independently justified trust checks distinct. An extra layer does not automatically need an
extra copy of the same rule.

Use [Give Recovery One Owner and an Explicit Outcome](./practices/implementation/make-recovery-behavior-explicit.md)
when a failure is about to gain a retry, empty default, alternate source, or degraded result. It
requires a known recoverable condition, a bounded owner, and an outcome the caller can understand.
Validation establishes a fact; recovery responds to a failure. Either decision can arise alone.

The surrounding requirements, planning, implementation, testing, and final-review Practices reinforce
these choices without requiring a new checklist or risk document for every change. Examples span
HTTP and CLI ingress, imports, typed domain calls, database constraints, caches, authorization, and
local lifecycle recovery. These rules preserve required protections and accepted degraded behavior;
they do not authorize deleting existing guards or changing project contracts without evidence.

## Non-goals

This Pack is not a workflow engine, task manager, test framework, compactor, automatic acceptance system, or replacement for project specifications. It does not prescribe a particular coding agent, repository layout, command, language, or framework. Practice retrieval or citation is not evidence that a task succeeded.

## Release history

- `0.6.0` extends `keep-acceptance-path-completable` to information withholding: hiding a fact from a surface joins the deletion test on the same terms as a gate — forced only by naming who is harmed by seeing it there — and redaction moves to the boundary where material leaves the machine (exported bundles, posted feedback) instead of degrading the local surface. `applies_when`, the anti-pattern, and the example gain the redaction shape; the fixture query set gains a fourth positive for this Practice, and the practice-catalog's expected and forbidden behaviors name blanket hiding. Grounded in sanitized internal trial feedback from 2026-09-22 (see [SOURCES.md](./SOURCES.md)). No Practice IDs change (34 entries).
- `0.5.1` is the immutable release at `agentic-coding-v0.5.1`, available through the official Registry. It is a metadata-only retune: the `pack.yaml` description — the routing surface agents see in the session-start pack catalog — now names the two planning-calibration moments added in `0.5.0` (decision-bearing sets growing past what they must decide; validation, permission, or confirmation checks blocking the acceptance path) and tracks the `decide-scope-and-stop-conditions` merge. No Practice content changes. A representative official Registry install on September 21, 2026 resolved this ref (`954324c`), decoded the Pack, and read back all 34 Practice IDs including both planning-calibration entries; that verifies the supported materialization path, not retrieval quality or downstream Agent behavior.
- `0.5.0` rewrites every Practice as a step-style decision procedure with an explicit stop, merges the two pre-work scope Practices into `decide-scope-and-stop-conditions`, adds two planning-calibration Practices (`derive-committed-set-from-concerns`, `keep-acceptance-path-completable`; 33 to 34 entries), tiers severity into critical/warn/info, and retunes `applies_when` against the #17 keyword retrieval baseline. A representative official Registry install on September 21, 2026 resolved this ref, decoded the Pack, and read back all 34 Practice IDs; that verifies the supported materialization path, not retrieval quality or downstream Agent behavior.
- `0.4.0` adds two independent validation and recovery Practices and refines seven existing entries against unnecessary defensive complexity (tag `agentic-coding-v0.4.0` + Registry entry).
- `0.3.1` adds a Practice that limits repository investigation to sources that can change the current decision and clarifies source roles without making broad reading a default.
- `0.3.0` rewrites the Pack for clearer standalone retrieval and adds a Practice for giving delegated Agents the decisions they need to preserve scope and quality.
- `0.2.0` is the immutable first complete 29-Practice release.
- `0.1.0` is an immutable one-entry installation placeholder retained only as release history. Its tag and content are not rewritten.

See [SOURCES.md](./SOURCES.md) for public provenance and the distinction between issue-explicit evidence and author synthesis.

Localized content under [`i18n/`](./i18n/) is provided for human-facing consumption and does not change the canonical runtime Pack, Registry release, retrieval inputs, or Practice IDs.
