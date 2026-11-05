# Backend Fundamentals

`backend-fundamentals@0.1.0` is an unreleased source Pack with 30 concise English Practices for agents implementing backend services. Each entry owns one decision and remains usable when retrieved alone.

## Scope

Use this Pack while implementing ordinary backend operations. It complements `agentic-coding`: that Pack governs engineering judgment and evidence; this Pack supplies concrete backend contract and integrity decisions. Each Practice remains useful when retrieved alone and uses ordered guidance with an explicit stop.

| Category | Practice | Decision |
| --- | --- | --- |
| API | [Preserve safe reads](practices/api/preserve-safe-read-semantics.md) | Keep requested business mutations out of GET/HEAD handling. |
| API | [Bound collection results](practices/api/bound-collection-results.md) | Enforce a finite browsing response at the data access path. |
| API | [Express HTTP failures](practices/api/express-http-failures.md) | Preserve meaningful failure semantics without leaking internals. |
| Security | [Protect entry access](practices/security/enforce-protected-entry-access.md) | Enforce access at a boundary callers cannot bypass. |
| Security | [Authorize the target](practices/security/authorize-target-resource.md) | Decide permission for the actual action, resource, and tenant. |
| Security | [Store password hashes](practices/security/hash-passwords-for-storage.md) | Store dedicated password verification material, not recoverable passwords. |
| Data | [Enforce uniqueness](practices/data/enforce-uniqueness-in-database.md) | Protect uniqueness against concurrent writers. |
| Data | [Group atomic writes](practices/data/group-atomic-database-writes.md) | Commit one local database unit together or roll it back. |
| Data | [Justify indexes](practices/data/justify-indexes-with-query-plans.md) | Choose indexes from representative query evidence and write costs. |
| Data | [Return acquired connections](practices/data/return-acquired-connections.md) | Return or discard each acquired client on every exit path. |
| Data | [Budget deployment connections](practices/data/budget-deployment-connections.md) | Fit aggregate pool demand within the deployment's allocation. |
| Security | [Exclude secrets from logs](practices/security/exclude-secrets-from-logs.md) | Select safe diagnostic context before it reaches log outputs. |
| Lifecycle | [Drain work before closing resources](practices/lifecycle/drain-work-before-closing-resources.md) | Stop intake and handle in-flight work within the shutdown budget before releasing dependencies. |
| Data | [Retire old schema after callers](practices/data/retire-old-schema-after-callers.md) | Remove old schema only after supported callers and migration writes have moved off it. |
| Data | [Parameterize query values](practices/data/parameterize-query-values.md) | Bind values separately from controlled SQL structure. |
| Data | [Batch required relations](practices/data/batch-required-relations.md) | Avoid per-row relation queries without loading unbounded or unauthorized data. |
| Security | [Protect cookie-authenticated writes](practices/security/protect-cookie-authenticated-writes.md) | Apply CSRF protection where browsers automatically attach credentials. |
| Security | [Enforce scoped rate limits](practices/security/enforce-scoped-rate-limits.md) | Bound the intended caller or operation across the actual deployment scope. |
| Security | [Generate upload storage keys](practices/security/generate-upload-storage-keys.md) | Keep client filenames from controlling storage paths or replacing existing objects. |
| Security | [Verify webhook message signatures](practices/security/verify-webhook-message-signatures.md) | Verify the provider's signed message before trusting an event or applying effects. |
| Data | [Commit event deduplication with effects](practices/data/commit-event-deduplication-with-effects.md) | Persist the duplicate decision and local database effect in one atomic unit. |
| Data | [Guard state-dependent writes](practices/data/guard-state-dependent-writes.md) | Enforce current-state or version preconditions when concurrent writes race. |
| Data | [Enforce related-row integrity](practices/data/enforce-related-row-integrity.md) | Preserve parent/child identities and declared removal rules across writers. |
| Data | [Represent money exactly](practices/data/represent-money-exactly.md) | Keep amount, currency, units, and rounding exact across boundaries. |
| API | [Isolate personalized cache responses](practices/api/isolate-personalized-cache-responses.md) | Prevent shared HTTP caches from widening a response's intended audience. |
| API | [Bind only declared write fields](practices/api/bind-only-declared-write-fields.md) | Restrict which request properties an operation may assign. |
| API | [Allow intended browser origins](practices/api/allow-only-intended-browser-origins.md) | Grant cross-origin response access without confusing it with server authorization. |
| Security | [Constrain server fetch destinations](practices/security/constrain-server-fetch-destinations.md) | Enforce the actual outbound destination through resolution and redirects. |
| Security | [Admit supported upload content](practices/security/admit-only-supported-upload-content.md) | Check and bound actual file content before processing or serving it. |
| Lifecycle | [Check readiness before traffic](practices/lifecycle/check-readiness-before-admitting-traffic.md) | Advertise usable serving capabilities rather than mere process existence. |

## Boundaries

This is not a backend course, framework starter, architecture mandate, or complete security standard. It does not prescribe microservices, dependency injection, GraphQL, distributed transactions, OAuth/OIDC integration, message queues, Kubernetes, or a cloud provider. PostgreSQL-specific examples and semantics are not promises about every database.

Input validation ownership and retry/fallback design stay with the corresponding `agentic-coding` Practices. Resource authorization is distinct from establishing trusted caller identity. Database uniqueness and transaction atomicity address different failures; neither replaces concurrency control for arbitrary read-modify-write invariants.

## Evaluation and provenance

See [SOURCES.md](SOURCES.md) for research provenance and independently authored synthesis boundaries, and [CANDIDATES.md](CANDIDATES.md) for decision selection and neighboring boundaries.

The evaluation-only [catalog](../../fixtures/backend-fundamentals/practice-catalog.yaml), [queries](../../fixtures/backend-fundamentals/queries.yaml), and [workflows](../../fixtures/backend-fundamentals/workflows.yaml) cover all 30 decisions. Their 150 active query expectations are hypotheses, not runtime Pack input or proof of retrieval quality. Existing retired queries and explicit target migrations remain documented. Runtime content and fixtures are English; no localization is provided.

The [source-check workflow](../../.github/workflows/backend-fundamentals.yml) validates Pack format, fixture identities and coverage, then uses the repository's unchanged `scripts/eval-project-layer` helper and the public CLI to read every candidate Practice and run one keyword activation query. It does not add or modify an evaluator, run a full retrieval benchmark, invoke a model, or establish downstream usefulness. Larger local authoring experiments and historical baselines are deliberately outside this content PR.

For a focused manual check, run from the repository root with Python 3.10+, PyYAML, and a layer-aware `lore` executable (`0.1.0-alpha.3` or newer):

```sh
lore validate packs/backend-fundamentals --json
python scripts/eval-project-layer --pack backend-fundamentals --dir tmp/backend-source-project --lore <lore>
lore context status --project-root tmp/backend-source-project --store-root tmp/backend-source-store --json
lore query "A link preview fetches our GET confirmation URL and accidentally submits the purchase. Where should confirmation happen?" --mode keyword --project-root tmp/backend-source-project --store-root tmp/backend-source-store --json
lore get backend-fundamentals.api.preserve-safe-read-semantics --project-root tmp/backend-source-project --store-root tmp/backend-source-store --json
```

Replace `<lore>` with the executable path. The helper creates disposable local evaluation state; do not point it at an existing authored project. These commands verify local structure and activation only. No Registry entry, release tag, installability, semantic-retrieval result, or Agent-effectiveness claim is made.
