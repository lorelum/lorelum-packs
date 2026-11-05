# Sources and synthesis boundaries

Original research snapshot: 2026-11-01. The API, original security and core database source maps are in [sources.yaml](sources.yaml); connection cleanup and deployment budgeting have separate entries. Further inputs are in [lifecycle sources](references/lifecycle-sources.yaml), [query and request safety sources](references/query-safety-sources.yaml), and [event and file sources](references/event-files-sources.yaml). The nine-decision expansion uses the additional primary-source maps below. These files record provenance, not runtime instructions or a license grant.

## Fixed Skill inputs

| Area | Source | Fixed revision | Use |
| --- | --- | --- | --- |
| API | [wshobson API Design Principles](https://github.com/wshobson/agents/blob/0f73766f80b0b5eb6aa9faaa2fdf90df85a6793f/plugins/backend-development/skills/api-design-principles/SKILL.md) and its `references/details.md` | `0f73766f80b0b5eb6aa9faaa2fdf90df85a6793f` | Safe reads, bounded collections, outward HTTP failures. |
| Security | [wshobson Auth Implementation Patterns](https://github.com/wshobson/agents/blob/be57c0b2e3c05c528ca6132b87410b385718775f/plugins/developer-essentials/skills/auth-implementation-patterns/SKILL.md) and its `references/details.md` | `be57c0b2e3c05c528ca6132b87410b385718775f` | Protected ingress, target-resource permission, password verifiers. |
| Data | [Supabase Postgres Best Practices](https://github.com/supabase/agent-skills/blob/c9be0e931b7930f7d02126d04774d904c381e7d7/skills/supabase-postgres-best-practices/SKILL.md) and seven linked rules | `c9be0e931b7930f7d02126d04774d904c381e7d7` | Uniqueness, short local transactions, query evidence, pooling and capacity. |

The wshobson revisions are path-specific commit-feed snapshots, not claims about the latest repository-wide revision. The Supabase input declares MIT in its Skill metadata. Source prose and code are not reproduced: the Pack's guidance and examples are independently authored. This file does not assert that the Pack license relicenses upstream materials.

## Additional primary inputs for the 30-Practice source

- [Integrity and exact values](references/integrity-and-values-sources.yaml): PostgreSQL concurrency, relationship constraints, exact numeric representation, and explicit transport/currency boundaries.
- [HTTP request and response boundaries](references/http-boundary-sources.yaml): shared-cache semantics, writable-field projection, and browser-origin response access.
- [Runtime admission](references/runtime-admission-sources.yaml): outbound destination enforcement, file-content admission, and readiness versus liveness and shutdown.

These additions are independently authored from primary technical references rather than copied Skill prose. Each map records supporting claims, limitations, and author synthesis; an unversioned documentation URL is not an immutable research pin.


## Primary technical support

- [RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html): safe versus idempotent methods, GET/HEAD, error status and required headers.
- [RFC 9457](https://www.rfc-editor.org/rfc/rfc9457.html): safe error disclosure; Problem Details is optional, not a mandatory replacement protocol.
- [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html): enforcement placement, deny-by-default, object/action/relationship policy.
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) and [argon2-cffi documentation](https://argon2-cffi.readthedocs.io/en/stable/howto.html): dedicated salted verifiers, cost selection, verify and rehash APIs.
- PostgreSQL 18: [constraints](https://www.postgresql.org/docs/18/ddl-constraints.html), [unique checks](https://www.postgresql.org/docs/18/index-unique-checks.html), [transactions](https://www.postgresql.org/docs/18/tutorial-transactions.html), [isolation](https://www.postgresql.org/docs/18/transaction-iso.html), [plans](https://www.postgresql.org/docs/18/using-explain.html), [indexes](https://www.postgresql.org/docs/18/sql-createindex.html), [limits](https://www.postgresql.org/docs/18/queries-limit.html), and [connection configuration](https://www.postgresql.org/docs/18/runtime-config-connection.html).
- node-postgres: [pool API](https://node-postgres.com/apis/pool), [pooling](https://node-postgres.com/features/pooling), and [pool sizing](https://node-postgres.com/guides/pool-sizing): client affinity, cleanup, queues and aggregate budgeting. Driver-specific behavior must be checked for the deployed driver rather than assumed universal.

## Independent boundaries

The Pack does not inherit blanket indexing, universal speedup factors, fixed pool formulas, universal token lifetimes, owner-only authorization, or framework mandates from discovery material. Local atomicity does not promise remote-effect atomicity or arbitrary concurrent-update isolation. Password costs and connection counts are deployment decisions, not copied constants. Primary web documentation was read at the research snapshot; unversioned OWASP and library pages can subsequently change.

Examples are constructed illustrations, not observed incidents or backend benchmarks. The evaluation-only catalog, queries, and workflows under `fixtures/backend-fundamentals/` cover the 30 current decisions. The source-check workflow observes format validity, local activation and full readback, plus one keyword query; it is not a full retrieval benchmark or Agent-effectiveness experiment. Larger authoring records and historical baselines are retained locally outside this source submission. Structural validity, retrieval selection and downstream behavior remain separate claims.
