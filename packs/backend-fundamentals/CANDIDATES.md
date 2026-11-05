# Selection and decision boundaries

The Pack contains 30 English agent-facing Practices. Each keeps one decision, a short procedure and stop, a concrete failure, and only the constraints needed when retrieved alone. Detailed technical evidence belongs in the source maps, not repeated tutorials.

## Deliberate separations

- Entry access establishes an enforced caller boundary; resource authorization decides the action on a particular target, including public visibility. CSRF addresses ambient browser credentials; webhook verification authenticates the provider's signed message.
- HTTP failures define outward status and representation. Logging chooses safe diagnostic content. Both retain the minimum secret-exclusion rule needed on their own.
- Pagination bounds retrieval and continuation. Relation batching controls repeated lookups while preserving child predicates. Index evaluation concerns a concrete access path and can end without an index.
- Database uniqueness enforces a key; local transactions couple required writes; event deduplication couples the durable event claim to its local effect. None promises exactly-once remote effects.
- Acquired-client cleanup and deployment pool budgeting have independent triggers. Process shutdown orders intake, in-flight work and dependency release; it is not another connection-acquisition procedure.
- Upload storage keys keep destination selection server-owned, not automatically authorized or content-safe. Schema retirement gates destructive changes on actual deployed callers and live data transition.

## Nine additional decisions

| Decision | Separate boundary, not a renamed existing Practice |
| --- | --- |
| Current-state write protection | Coordinates competing updates and transition predicates; committing several writes together does not by itself prevent a lost update. |
| Related-row integrity | Enforces persisted parent/child identity and deletion rules; uniqueness and caller permission do not prevent orphaned relationships. |
| Exact monetary values | Preserves representation, currency, denomination, and rounding; atomic writes do not correct an already inexact amount. |
| Personalized HTTP cache isolation | Governs storage and reuse before origin authorization can run; target permission alone cannot protect a shared cache hit. |
| Writable-field binding | Chooses the properties eligible for assignment in an authorized operation; record permission and valid input types do not authorize every model field. |
| Browser-origin response access | Controls which browser scripts may read responses; it neither authorizes mutations nor substitutes for cookie-write protection. |
| Outbound fetch destinations | Restricts where the backend may connect, including DNS and redirects; authenticated entry does not authorize every destination. |
| Upload content admission | Decides which actual bytes may be processed and served; server-owned keys do not establish content safety. |
| Startup/recovery readiness | Decides whether an instance can admit its advertised traffic; shutdown handles already accepted work and dependency release. |


## Connection migration

`backend-fundamentals.data.bound-and-release-database-connections` is removed, without an alias. Its two decisions are now `data.return-acquired-connections` and `data.budget-deployment-connections` under the same Pack prefix. Existing query text and IDs are retained with explicit target migration; history is not relabeled to improve scores.

## Excluded scope

Generic validation ownership and caller-side retry/fallback policy stay with `agentic-coding`. Receiver-side durable event deduplication is a distinct backend decision. No framework mandate, authentication platform, distributed transaction, queue, outbox, universal index formula, or universal limiter threshold is introduced.

## Evaluation

`practice-catalog.yaml` records direct, paraphrase and near-miss behavior hypotheses. Primary and secondary neighbors distinguish separate applicable decisions and preserve historical contrasts. Frozen English `queries.yaml` expectations precede retrieval; `workflows.yaml` composes distinct guarantees. A positive query is not evidence that neighbors are distinguishable, and retrieval is not evidence of backend behavior.
