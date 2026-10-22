---
anti_patterns:
  - description: The agent adds individually plausible protections to the steps the task's own acceptance scenario must travel until that scenario cannot complete without interventions the request never named; each protection cites a risk category, and none names a reachable failure in this deployment.
    id: agentic-coding.planning.acceptance-blocking-protection
    name: Acceptance-blocking protection
    severity: warn
applies_when: proposed validation, authorization, permission, confirmation, readiness, or compatibility checks must be composed into a required user workflow, including backend startup, shutdown, repair, or recovery, and the agent must walk whether the acceptance scenario can complete through their combined effects
id: agentic-coding.planning.keep-acceptance-path-completable
severity: warn
stage: planning
tech_stack:
  - agentic-coding
title: Keep the Acceptance Path Completable
---

## When to apply

Apply while planning or implementing a workflow whose proposed checks must work together. Walk
normal operations, startup, shutdown, and supported repair or recovery commands, even when the task
never mentions gates. Completeness and added friction cover the required paths, including error,
repeat, and reopen cases. A single proposed check's necessity is the `justify-added-checks` decision;
this Practice decides how checks compose without making the accepted workflow unusable.

## Guidance

Write each acceptance condition as ordered steps from initiating action to observable result,
including every check the change adds. Walk all conditions for completeness: a required step with no
owner is a mapping gap and is resolved before weight. Walk the required paths for friction: every
added check that can reject, delay, or demand intervention is a gate. Give each gate the lowest rung
that still catches a failure you can name — observe (log), mark (annotate the result), confirm (ask
once, answerable where it fires), block (reject). A rung above observe is forced only by an actor
and path that exist in this deployment's facts or contracts; a risk category or generic attacker
forces nothing. Prefer narrowing a justified gate to the risky subset over gating the whole path.
Run the deletion test: name what concretely breaks, for whom, if the gate is removed; no answer
means remove the gate. A confirm nobody in this context can answer is a block — lower it or scope it
with a documented bypass. Then walk one failure: pick the check most likely to fail, let it fail
alone, and state what survives; if a single failure voids every other satisfied condition, name who
needs that severity and why attributed or partial results would mislead. Record the rung and the
named failure beside each gate's plan item. Stop when every acceptance condition completes with no
intervention beyond those acceptance or governing policy already name, and every gate names the
failure its rung blocks. For a change too small to plan, ask: can this added check block or delay a
required path, and what failure or mandatory policy justifies that effect? Keep justified blocks;
otherwise lower the rung, narrow the scope, or omit the check.

A repair command must not require the damaged index to be healthy; stopping an identified old
process need not require its business API to match the new client. Keep operation-specific
authorization, process identity, activity
protection, and consistency requirements. If a required safeguard conflicts with the recovery
contract, surface that conflict rather than bypassing it to make the command pass.

## Anti-pattern

A task adds a config-set command to a local CLI. The agent ships it with an allowlist of keys, a
path-traversal validator, an ownership permission check, and a confirmation prompt on every write.
The acceptance scenario — developer runs the command, config updates — now requires an interaction
and a grant the request never named, and scripts calling the CLI cannot answer the confirmation. The
calibration arrives after delivery, as deletion.

## Why

Per-item review sees each protection's story, never the composed path, and training prices visible
incidents above invisible unusability, so unwalked plans rationally over-protect. A Practice
improves only the dimension it names: completeness, friction, and failure composition must each be
walked to improve.

## Exceptions and boundaries

Governing policy or compliance can force a rung without local reachability — cite the policy, and
the gate stays. A real, reachable threat justifies block; this Practice calibrates and does not
remove justified protection. Pre-existing system guards are constraints, not deletion-test
candidates; if they make acceptance impossible, surface the conflict instead of removing them. Where
a check belongs and whether it duplicates another rule is a boundary-ownership question.

## Example

The config-set walkthrough writes the primary path with all four checks on: command, allowlist,
permission, confirmation, write, verify. Deletion tests: traversal input cannot reach a path, the OS
already enforces file ownership, and the confirmation catches typos acceptance does not name — all
drop or downgrade. One concern survives with evidence: overwriting the security-token key forces
re-authentication elsewhere. The gate narrows to a single confirm on that key subset; every other
key writes without interruption, and the change is logged.

A backend's repair command must rebuild a damaged derived index while the service is not ready.
The proposed shared readiness preflight rejects that exact state before repair begins. The agent
keeps readiness enforcement for ordinary queries but gives repair its own authorized path with the
required exclusive mutation protection. Repair can now rebuild the index without first querying it;
an unauthorized caller or a conflicting writer is still refused. No global "skip checks" switch is
introduced.
