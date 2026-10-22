# Retrieval evaluation: agentic-coding-queries vs agentic-coding@0.6.0

- Generated: 2026-10-22T07:40:46+00:00  |  lore 0.1.0-alpha.6  |  mode(s): keyword  |  top-k threshold: 3
- Store: E:\lorelum-packs\tmp\pack-release-060-store  |  Queries: 200 runnable, 0 skipped

## keyword — gate PASSED (positive top-3 97.5% vs fixture minimum 90.0%)

- Positive queries: top-1 89.2% (120 queries), top-3 97.5%
- Neighbor queries: expected selected top-1 58.8% (80 queries), in top-3 86.2%, trap top-1 (query selects the Practice it resembles) 18.8%

Positive queries that missed top-3 (rewrite-priority input):

| query | actual top-3 |
|---|---|
| `planning.keep-acceptance-path-completable.p2` | review.run-subtractive-review-before-commit, planning.plan-sufficient-evidence, planning.map-plan-to-user-capability |
| `testing.anchor-tests-to-requirements.p3` | implementation.choose-smallest-sufficient-design, testing.justify-regression-protection, implementation.justify-added-checks |
| `planning.keep-acceptance-path-completable.p4` | implementation.justify-added-checks, implementation.validate-at-the-owning-boundary, implementation.inspect-and-reuse-existing-capability |

Most confused neighbor selections (queries worded near 'resembles' that selected 'actual top-1' instead of the expected neighbor):

| resembles | actual top-1 selection | count |
|---|---|---|
| `planning.keep-acceptance-path-completable` | `implementation.justify-added-checks` | 3 |
| `implementation.validate-at-the-owning-boundary` | `implementation.justify-added-checks` | 2 |
| `testing.justify-regression-protection` | `testing.justify-regression-protection` | 2 |
| `requirements.ground-user-goal` | `requirements.ground-user-goal` | 1 |
| `requirements.define-acceptance-and-non-goals` | `requirements.define-acceptance-and-non-goals` | 1 |
| `requirements.define-acceptance-and-non-goals` | `planning.derive-committed-set-from-concerns` | 1 |
| `planning.decide-scope-and-stop-conditions` | `planning.decide-scope-and-stop-conditions` | 1 |
| `planning.decide-scope-and-stop-conditions` | `implementation.replan-on-material-drift` | 1 |

> Evaluation-only evidence: observed retrieval selection for the fixture queries on one machine and one Pack revision. It is not a claim about content quality, other queries, other modes that were unavailable, or downstream Agent behavior.
