# Selection and decision boundaries

The Pack contains 30 English agent-facing Practices. Each keeps one decision, a short procedure and stop, a concrete failure, and only the constraints needed when retrieved alone. Detailed technical evidence belongs in the source maps, not repeated tutorials.

## Decision boundaries

- Entry access establishes an enforced caller boundary; resource authorization decides the action on a particular target, including public visibility. Writable-field binding limits assignment within an authorized operation: record permission and valid types do not authorize every model field.
- CSRF protects writes made with ambient browser credentials. Browser-origin response access controls which scripts may read responses, not mutation authority. Webhook verification authenticates the provider's signed message; authenticated entry does not authorize arbitrary outbound destinations, which need controls covering DNS and redirects.
- HTTP failures define outward status and representation; logging chooses safe diagnostic content. Both retain their own minimum secret-exclusion rules. Personalized cache isolation governs storage and reuse before origin authorization can run, so target permission alone cannot protect a shared cache hit.
- Pagination bounds retrieval and continuation; relation batching controls repeated lookups while preserving child predicates. Index evaluation concerns a concrete access path and can end without an index.
- Database uniqueness enforces a key; related-row integrity enforces parent/child identity and deletion rules, which neither uniqueness nor caller permission guarantees. Current-state write protection coordinates competing updates and transition predicates; a transaction alone does not prevent lost updates.
- Local transactions couple required writes; event deduplication couples the durable event claim to its local effect. Neither promises exactly-once remote effects. Exact monetary values require representation, currency, denomination and rounding decisions; atomic writes cannot correct an already inexact amount.
- Acquired-client cleanup and deployment pool budgeting have independent triggers. Startup and recovery readiness govern admission of advertised traffic; shutdown orders intake, in-flight work and dependency release, not connection acquisition.
- Upload storage keys keep destination selection server-owned, not automatically authorized or content-safe. Content admission decides which actual bytes may be processed and served. Schema retirement gates destructive changes on actual deployed callers and live data transition.

## Excluded scope

Generic validation ownership and caller-side retry/fallback policy stay with `agentic-coding`. Receiver-side durable event deduplication is a distinct backend decision. No framework mandate, authentication platform, distributed transaction, queue, outbox, universal index formula, or universal limiter threshold is introduced.

## Evaluation

`practice-catalog.yaml` records direct, paraphrase and near-miss behavior hypotheses. Primary and secondary neighbors distinguish current applicable decisions and contrasting boundaries. The 150 frozen English `queries.yaml` expectations precede retrieval; `workflows.yaml` composes distinct guarantees. A positive query is not evidence that neighbors are distinguishable, and retrieval is not evidence of backend behavior.
