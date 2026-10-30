---
anti_patterns:
  - description: Carrying low-value history into later work, then replacing it with a repetitive handoff that revives obsolete decisions or invents project locations from the current environment.
    id: agentic-coding.context.history-obscures-current-task
    name: Historical context obscures the current task
    severity: warn
applies_when: most accumulated history has little value for the intended continuation or is distorting current decisions, and the agent must decide whether to suggest a fresh conversation for ongoing work or the next phase
id: agentic-coding.context.recommend-session-handoff
severity: info
stage: context
tech_stack:
  - agentic-coding
title: Recommend a Fresh Session When History Adds Little Value
---

## When to apply

Apply when most history adds little value to ongoing work or the user's next phase, even before
mistakes occur, or when old directions already compete with accepted decisions. Completed
investigations, logs, and superseded plans can be left behind if a concise handoff preserves the
needed context. If a session boundary is already decided and only what to record remains open, write
a checkpoint.

## Guidance

1. Identify the intended continuation: remaining work and agreed next phases. Select the facts and
   clauses that govern its inputs, promised behavior, data handling, conditional outcomes, and
   planned or reused evidence. Preserve their qualifications and decision status, not a summary of
   every topic. Leave unrelated or superseded history behind; correct known drift and keep an
   unresolved next focus open.
2. Recommend a move when a compact record can replace substantial irrelevant history or remove
   interference. Do not wait for errors or use a turn-count threshold. Keep working when history
   remains useful without interference, or the work is ending without a continuation.
3. Map each selected source clause to its place in the handoff before writing the overview: brief
   text inline, or a precise location to read before a specified action. Keep this drafting map out
   of the handoff. Preserve independent obligations and conditional branches, not just topic
   coverage; data-handling and credential-storage limits do not collapse into "privacy". Retain the
   smallest complete clause with its conditions, exceptions, outcome, and decision status. Quote
   when paraphrase could change a boundary, not whole rule groups. Keep accepted requirements,
   proposals, and hypotheses distinct; "continue discussing" is not acceptance. When retaining
   experimental evidence, map its method controls as well as results and caveats. A later procedure
   may stay at a precise, accessible source location that preserves its rules and status; a document
   name or whole-history link is insufficient. Without a usable reference, include necessary
   clauses, not surrounding discussion. Redact sensitive values.
4. Write one compact action record: goal, current state, decisions, evidence limits, next actions,
   and necessary source pointers. Keep short constraints inline. Add an on-demand section only for
   essential detail that cannot be referenced elsewhere, not a second summary of every topic. Each
   fact appears once; the opener references the record. State unknown current facts without
   replaying obsolete snapshots. Adjacent tasks need dependencies, not plans or an inventory of
   omitted work. Keep a rejected option's reason only if it still constrains a choice. Leave out
   historical numbers, directory inventories, and debugging chronology that change no next decision.
5. Preserve historical paths as supplied. Use an absolute project root only when established for
   that project by the source or an explicit check; the current working directory alone is not such
   evidence. With an unknown root, retain the relative path and state that gap rather than guessing.
   A newly created handoff's actual location is valid new information: distinguish it from
   historical artifact locations and omit unrelated environment or test-harness details.
6. Compare every clause in the drafting map with its written text or precise required-reading
   location. Check input inclusions/exclusions, independent obligations, conditions, exceptions,
   outcomes, and method controls with their original status. A topic label, "preserve the contract",
   or an optional general source index does not cover an omitted rule. Remove material that changes
   no continuation decision, required behavior, or usable starting action; do not remove a
   controlling fact merely to shorten the file. At a stable boundary, explain the move and provide
   the handoff plus a ready-to-paste opener.

Stop when the user can resume from the record. If the user stays in the current session, continue
working; suggest a move again only after a material change.

## Anti-pattern

After a long gateway design discussion, implementation needs only the accepted behavior,
constraints, and relevant files. The agent waits for a mistake before suggesting a restart. Its
eventual handoff repeats the same design under background, status, and a full opening prompt, lists
rejected designs as open choices, and prefixes historical relative paths with its current directory.
The new session inherits extra reading and an unsupported project location.

## Why

History can add reading cost and compete with current instructions before visible drift occurs. A
handoff helps only when it preserves the controlling information without recreating that history or
filling gaps with the writer's environment.

## Exceptions and boundaries

Relevance, not length or coherence alone, guides the move. Keep working if preparing and loading a
handoff adds more effort than it removes; a completed phase can still be a useful boundary for the
next one. Independently pasted prompts need enough context or a usable shared reference. Remove
repetition before cutting a necessary constraint; no fixed word limit proves sufficiency. Shorter
text alone does not prove lower total cost or better downstream execution.

## Example

The user finishes a gateway design and asks to implement it. Most history concerns tokenizer trials
and a rejected text-type allowlist; no mistake has occurred. The agent records the accepted byte
estimate, reservation/reconciliation behavior, and known-file-payload exclusion while retaining
other content. It states once that implementation is unverified and retains the allowlist's
undercounting reason. A historical path such as src/estimate.ts stays relative when its project root
is unknown. The agent saves the handoff, gives its actual location, and supplies a short
implementation opener that reads it rather than repeating the discussion.
