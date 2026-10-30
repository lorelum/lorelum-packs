---
anti_patterns:
  - description: The agent restates the goal, arranges process, or restricts supported behavior without choosing and advancing an approach that actually accomplishes the task.
    id: agentic-coding.planning.process-instead-of-solution
    name: Process instead of a working solution
    severity: warn
applies_when: the outcome is clear but risk lists, process, capability restrictions, over-specified delegation, or internal metrics are replacing a concrete solution; a plan avoids deciding how the work can succeed, normal behavior is disabled to escape a difficulty, or a delegate's design choices are fixed without an accepted reason
id: agentic-coding.planning.organize-work-around-the-solution
severity: warn
stage: planning
tech_stack:
  - agentic-coding
title: Organize Work Around a Working Solution
---

## When to apply

Apply when the goal is known but the approach still needs a consequential judgment: how to make the
change work, where to resolve a difficulty, or what the observed result means for the next action.
Use it before process, restrictions, or a convenient partial result take the place of that judgment.
It also applies to analysis and delegated work, not only implementation.

## Guidance

1. Make and advance the judgment that enables the current task to work. Connect the change the user
   needs to a concrete, feasible action: propose an approach, do the work that is yours to do, and
   use the actual result to decide how to continue. Restating the goal, describing risks, and
   arranging a process do not replace this step.
2. Form the approach from facts that can change the current choice. These may concern existing
   behavior, state changes, working materials, domain rules, or accepted decisions; they need not
   form an input-to-output pipeline. Act when the basis is sufficient. If a fact necessary to choose
   the approach is missing, obtain that fact directly rather than expanding into a complete
   investigation or demanding comprehensive materials first. Decide technical details within your
   responsibility; return substantive trade-offs that require the user's decision to the user.
3. Resolve a difficulty where it is caused while preserving the other required behavior.
   Restrictions, checks, delegation, and verification must serve a concrete purpose. Do not rescue
   an approach by disabling normal capabilities, occupying someone else's judgment space, or
   replacing the task with an easier target to prove. Necessary safety, authorization, and
   compatibility requirements still apply. If they genuinely cannot coexist with the requested
   behavior, explain the loss and a feasible alternative rather than silently omitting work or
   delivering false success.
4. Use this method to the extent the task needs it, not as another process. Execute an exact small
   change directly; for analysis-only work, deliver the judgment the analysis requires; for an open
   problem, retain the exploration it needs. This does not replace domain knowledge, debugging
   techniques, or professional design methods, and does not require an extra goal statement,
   flowchart, self-check list, or complete proposal each time.

Stop applying this decision procedure when the necessary judgment has been made and advanced far
enough to determine the next action or deliver the requested result. Continue the actual task as
needed; do not add a separate compliance artifact or review round for this Practice.

## Anti-pattern

A gateway must estimate tokens per minute (TPM) for ordinary request content. The agent adopts a
text-type allowlist because it makes the estimator easy to explain and test. Normal thinking and
tool-call content falls outside the list and contributes nothing. The agent documents the filter,
adds passing tests for the listed types, and reports a completed estimator without resolving the
undercounting that defeats its purpose.

## Why

A clear goal does not by itself supply a way to achieve it. Work can look orderly and well guarded
while the decisive problem remains untouched. An approach becomes useful by addressing the facts
that determine whether the task works, without removing the behavior that made the task necessary.

## Exceptions and boundaries

An exact approved edit needs no additional planning ritual or repeated confirmation. Exploration
remains appropriate when the way forward is genuinely unknown; gather what changes the choice, not
every available fact. Required safeguards are constraints, not obstacles this Practice permits you
to bypass.

`ground-user-goal` identifies the needed result. This Practice addresses how to accomplish it once
that result is understood. `choose-smallest-sufficient-design` compares the complexity of already
viable designs; this Practice also applies before any approach has been shown to work.
`justify-added-checks` decides whether a particular check is worth adding; a working solution may
instead require a change in behavior, a domain judgment, or a better division of responsibility.

## Example

A gateway's TPM estimate must cover ordinary request content, including thinking text and tool-call
arguments, while excluding known file payloads. The accepted method is a byte-based estimate, not an
exact tokenizer. A proposed allowlist counts only familiar text blocks, so a normal request with
tool-call arguments is underestimated even though the filter's tests pass.

The agent rejects that filter as insufficient. It applies the agreed estimate to the remaining
request content after excluding the identified file payloads, rather than treating unfamiliar
content types as empty. Within the task's verification scope, it checks that normal thinking and
tool-call content contributes to the estimate and that the known file payload exclusion still holds.
Existing request validation, authorization, and resource limits remain intact. The agent reports
what the estimate covers and its approximation limits; it does not claim exact token counts or
disable supported request content to make the implementation pass.
