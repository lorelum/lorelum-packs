---
anti_patterns:
  - description: The agent grows a decision-bearing set one locally reasonable item at a time without a stated concern set, until the set's own size becomes the failure — diluted judgments, unfair gaps, or all-or-nothing brittleness — and later removals swing it the other way.
    id: agentic-coding.planning.criterion-accretion
    name: Criterion accretion
    severity: warn
applies_when: a decision-bearing set — plan items, acceptance criteria, checklists, definition-of-done lists, standards, checks, rules, or scoring dimensions that will determine what is built, what passes, what scores, or what is allowed — is being formed or revised by adding, merging, or removing items, whether working alone or in discussion
id: agentic-coding.planning.derive-committed-set-from-concerns
severity: warn
stage: planning
tech_stack:
  - agentic-coding
title: Derive the Committed Set from Concerns
---

## When to apply

Apply while any set that will settle future judgments is under construction: a plan's committed
tasks, acceptance or definition-of-done lists, review standards, test batteries, validation or
permission rules, or scoring dimensions. The test is function, not name: if consulting the set will
decide pass or fail, build or defer, allow or deny, or a score, this Practice applies. It applies at
the first proposed item, again at any later addition or removal, and when inheriting an
already-grown set, by reconstructing its concerns first. Lists that only record or explore — notes,
inventories, outlines — do not trigger it. It includes the situations teams describe colloquially: a
checklist that keeps growing until it blocks everything, a definition of done someone keeps adding
to, a rubric whose overlapping entries each carry too little weight, or a plan whose task list no
longer shows what is actually required.

## Guidance

Before proposing items, write the concerns the set must serve: the outcomes current acceptance must
distinguish, failures with reachable evidence, and expansions someone with authority has named.
Items exist only as derivations from concerns. Admit a candidate through three questions: which
uncovered concern does it serve; which judgment would differ with it present — build or defer for
plans, accept or reject for checks, two plausible candidates told apart for scoring; and does an
existing item already measure the same thing. Merge overlaps instead of keeping both. Record the
operating points chosen for the named tensions: coverage against parsimony, present requirements
against named expansions, protection against usability. When a discussion proposes an item, convert
it to a concern first; one that changes no judgment is parked, not added. On inheriting a grown set,
reconstruct concerns backwards from its items, then re-derive and drop what no concern owns. Stop
when every concern has an item, every item changes some judgment, no overlap remains unmerged, and
each item's failure semantics are chosen; after that freeze, a new item requires a new concern. For
a change too small to plan, the concern set is the goal sentence plus acceptance, and derivation is
trivial.

## Anti-pattern

A team's review standard for data migrations starts as three checks. Each incident adds one more
reasonable-sounding criterion, until every migration is blocked by something and weeks are lost
arguing exceptions. A purge follows, and the standard now misses the failures that started it. The
count was adjusted in both directions while the concern set behind it was never written.

## Why

Every addition is locally justified and every removal is locally costly, so unmanaged sets grow
until size itself fails. Item count is a derived quantity: fixing unfairness means adding a concern,
fixing dilution means merging one, and adjusting items directly produces the oscillation.

## Exceptions and boundaries

A governing contract, policy, or compliance requirement can force an item that serves no locally
statable concern; cite the source and keep it. Exploratory lists may stay uncommitted scratch until
a decision-bearing set is needed. This Practice forms the set; whether an assembled plan's path
stays completable is decided by walking it, and whether work has a current reason is decided at
admission.

## Example

A team defines what a release must pass before a schema migration ships. Concerns: the change must
be reversible, live data must survive it, and failure must be observable. Four items derive: a
tested rollback path, an integrity check on migrated rows, an alert on write failures during
cutover, and a stop condition for aborting. A reviewer proposes a fifth — a style check on migration
files — and it is parked: no concern owns it, and it changes no build-or-abort judgment.
