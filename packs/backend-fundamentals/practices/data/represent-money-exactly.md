---
anti_patterns:
  - description: Passing monetary values through binary floating point or an implicit two-digit scale and treating rounded or corrupted amounts as authoritative.
    id: backend-fundamentals.data.inexact-or-unitless-money
    name: Inexact or unitless money
    severity: critical
applies_when: monetary amounts cross parsing, arithmetic, persistence, or API boundaries where approximation or ambiguous currency units can change their value
id: backend-fundamentals.data.represent-money-exactly
severity: critical
stage: implementation
tech_stack:
  - language-agnostic
  - postgresql
title: Represent Money Exactly
---

## When to apply

Apply when amounts become authoritative for billing, balances, refunds, or settlement. Preserve exact
meaning across the value path; display formatting cannot repair approximation or a mistaken unit.

## Guidance

1. Carry currency and units explicitly or through an enforced surrounding contract. Obtain supported
   currencies and operation-specific minor-unit rules from authoritative metadata or the receiving
   API. Neither two fractional digits nor one provider denomination is universal.
2. Use exact decimals or checked integers in a declared minor unit. Choose precision, scale, and
   range for allowed amounts and intermediate calculations. Check overflow and preserve precision
   needed before rounding. Do not combine unlike currencies as matching units.
3. Parse the original decimal text or integer units directly into that representation, without an
   intermediate binary float. Reject non-finite values, unsupported currency, out-of-range amounts,
   and excess precision unless the operation explicitly permits rounding that input.
4. Identify the rounding boundary, target scale, and mode, including negative amounts and ties.
   Avoid repeatedly rounding intermediate results. PostgreSQL `numeric` with declared scale rounds
   on assignment; ensure this coercion agrees with the business contract.
5. Preserve exactness through drivers and serialization. JSON numbers do not guarantee exact decimal
   or large-integer handling across consumers. Define decimal-string or integer-string fields where
   needed; numeric minor units must fit every participant's exact range. Receivers must parse exactly,
   not through a float.

Stop when amount, currency, and denomination retain their intended value from input through storage
and output, with rounding occurring only at the agreed boundary.

## Anti-pattern

A service parses amounts through binary floats and a hard-coded hundred, then sends large stored
integers beyond a JSON consumer's exact range. An exact database column cannot recover the discarded
fraction or changed serialized value.

## Why

Decimal quantities are not all exactly representable in binary floating point. Exact storage alone
also cannot prevent currency-unit confusion, unintended scale coercion, or precision loss between languages.

## Exceptions and boundaries

Approximate analytics may use floats when their error is explicitly acceptable; they must not silently
become payment instructions. Exact representation does not select tax policy, exchange rates, allocation
rules, or prices. Fractional intermediate values may legitimately exceed a currency's payable scale.

## Example

A refund contract accepts `{"currency":"USD","amount":"7.25"}` as major-unit decimal text and stores
it exactly. Its payment adapter converts to 725 minor units. A JPY refund of `"725"` converts to 725,
not 72500. The adapter uses its provider's currency rules, and any large stored units travel as strings
when consumers cannot preserve them as JSON numbers.
