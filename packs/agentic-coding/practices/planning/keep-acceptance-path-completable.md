---
anti_patterns:
  - description: The agent adds individually plausible protections to the steps the task's own acceptance scenario must travel until that scenario cannot complete without interventions the request never named; each protection cites a risk category, and none names a reachable failure in this deployment.
    id: agentic-coding.planning.acceptance-blocking-protection
    name: Acceptance-blocking protection
    severity: warn
applies_when: the plan, or a mid-implementation addition, is about to place validation, authorization, confirmation, permission, or other blocking checks on the steps the task's own acceptance scenario must travel, and the combined effect of those checks on completing that scenario has not been walked end to end
id: agentic-coding.planning.keep-acceptance-path-completable
severity: warn
stage: planning
tech_stack:
  - agentic-coding
title: Keep the Acceptance Path Completable
---

## When to apply

Apply before committing a plan that adds checks on the task's own delivery path, and again when
implementation is about to add such a check mid-flight. Completeness covers every acceptance
condition, including error, repeat, and reopen cases; friction covers the primary path only. For a
change too small to plan, the Practice reduces to the one question at the end of Guidance.

## Guidance

Write each acceptance condition as ordered steps from initiating action to observable result,
including every check the change adds. Walk all conditions for completeness: a required step with no
owner is a mapping gap and is resolved before weight. Walk the primary path for friction: every
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
failure its rung blocks. For a change too small to plan, ask only: does any check I am adding block
the acceptance path? If yes, lower the rung or narrow its scope.

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
