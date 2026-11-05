---
anti_patterns:
  - description: Interpolating validated or stored values into SQL, or allowing caller-selected identifiers as though value binding protected query structure.
    id: backend-fundamentals.data.interpolate-query-input
    name: Validated input becomes SQL syntax
    severity: critical
applies_when: constructing or changing SQL, ORM raw expressions, query-builder fragments, or stored-procedure calls with caller-influenced values or structural choices
id: backend-fundamentals.data.parameterize-query-values
severity: critical
stage: implementation
tech_stack:
  - backend-fundamentals
title: Parameterize Query Values
---

## When to apply

Apply when input reaches SQL, including raw ORM fragments, reporting sorts, bulk queries, and dynamic
SQL inside stored procedures. Queue messages, editable configuration, and previously stored values
remain data; their origin does not make them trusted query syntax.

## Guidance

1. Find the final execution API and separate variable data from structural choices. Names, dates,
   row identifiers, and search text are values; table names, columns, operators, and sort directions
   change statement structure.
2. Bind every variable value through the driver's or ORM's supported API. Use its placeholder syntax,
   leave placeholders unquoted, and pass values separately. Preparing an interpolated statement
   does not repair it. For variable-length lists, use collection binding or generate placeholders
   only, binding each element separately.
3. Keep structure fixed where possible. Map any permitted public structural choice to a finite set
   of application-owned identifiers or complete fragments for that query. Reject unknown choices
   through the existing input-error path. Identifier-shaped text and quoting do not establish that
   a caller may choose a particular database object.
4. Preserve authorization predicates, matching behavior, ordering, and result semantics. Keep
   meaningful type and range validation, but do not substitute it for binding. Apply the same rule
   to raw fragments and dynamic SQL within stored procedures, not just their outer calls.

Stop when values remain separate from statement text at execution and selectable structure comes
only from the query's allowed mapping, without introducing another sanitization layer.

## Anti-pattern

A report checks name length and manually escapes apostrophes before interpolation. It appends any
identifier-shaped sort input. Validation has neither separated values from syntax nor restricted
sorting to the report's approved columns.

## Why

Binding lets the database API distinguish data from the program using it. Structural mapping solves
the different problem of choosing an allowed query shape without granting arbitrary syntax control.

## Exceptions and boundaries

Static SQL has nothing to bind, and application-owned fragments may be composed. Use the driver's
identifier facilities when needed, after choosing from the allowed mapping. ORMs and stored
procedures are not automatically safe when their raw paths concatenate input.

Binding does not authorize rows, bound query cost, or make `LIKE` wildcards literal. Preserve the
existing search contract and separately escape pattern metacharacters when it promises literal
matching. Validation and database privileges remain independent controls.

## Example

An invoice report binds customer reference and tenant ID through Django's `cursor.execute()` values
list using unquoted `%s` placeholders. Its two public sort names map to fixed `issued_at` and
`total_amount` fragments with the existing direction and tie-breaker. Unknown sort names are rejected;
a stored apostrophe stays an ordinary value, and invoice visibility is unchanged.
