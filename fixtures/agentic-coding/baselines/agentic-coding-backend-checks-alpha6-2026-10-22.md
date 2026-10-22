# Backend-check refinement: alpha.6 evaluation

Evaluation-only observations for unpublished working-tree content. [Full query rankings, content fingerprints, blind/injected answers, and quoted verdict evidence](./agentic-coding-backend-checks-alpha6-2026-10-22.json).

## Runtime and method

- Official [Lorelum 0.1.0-alpha.6 prerelease](https://github.com/lorelum/lorelum/releases/tag/v0.1.0-alpha.6), protocol 2, Windows x64. The extracted archive SHA-256 matched `00dce687cd152103377dc0e05d5d697e599f423733a91c5ae3c7d5310dde8ce9` from the release asset metadata. The user installation and Store were not upgraded or modified.
- Before: this working tree before the lifecycle refinement, including the existing PR #38 content. Final: the two-Practice refinement. Both retain Pack version 0.5.1 and 34 IDs; neither comparison arm is a new Registry release.
- Self-contained project layers (`base: none`), public `lore query --mode keyword --top-k 3` loops, isolated derived cache. This avoids the existing `eval-queries` installed-Store catalog path. Both arms use the same 191-query fixture, including the 11 new hypotheses; expectations were fixed before querying.
- Final `lore validate`: valid, no diagnostics; all 34 zh-CN translations current, none stale or missing. The existing `eval-queries` discipline function found no shared four/five-word violations against runtime metadata. All 34 IDs were successfully fetched through `lore get`.

## Retrieval, not automatic lookup

| Keyword observation | Before | Final |
|---|---:|---:|
| Positive top-1 | 104/117 | 107/117 |
| Positive top-3 | 113/117 (96.6%) | 114/117 (97.4%) |
| Neighbor expected top-1 | 45/74 | 43/74 |
| Neighbor expected top-3 | 62/74 | 62/74 |
| Original positive top-3, excluding the 11 additions | 109/110 | 108/110 |

The aggregate positive gain hides an existing positive regression. Expected top-3 losses: `planning.keep-acceptance-path-completable.p2` (whole-batch rejection) and `planning.plan-sufficient-evidence.n1` (mapping already-passed checks). Gains: `testing.justify-regression-protection.n1`, and the new repair/stop queries `planning.keep-acceptance-path-completable.p5` and `.p6`. The JSON retains every before/final ranking.

Four new queries still miss their expected top-3: ownership `.n3` and `.n4`, acceptance-path `.p4` and `.n4`. New examples and metadata do not resolve recovery/evidence near-neighbor confusion.

Nine exploratory natural-task queries were held unchanged from the preceding discussion. These are development probes, not a held-out generalization set:

- Ordinary service/repository implementation and local HTTP consumption: ownership remains first.
- Large model-directory startup: ownership moves from second to first; acceptance-path enters second.
- Repairing a damaged index while Backend is not ready: acceptance-path moves from absent in top-3 to first, ownership is second.
- Third-party JSON and tenant administration: ownership remains in top-3. These are protection-preservation cases, not deletion targets.
- Concurrent last-item reservation: ownership still misses; responsibility-boundaries remains first.
- Both Chinese startup/repair queries still return no keyword results. Companion translations are not canonical runtime retrieval input.

## Body provision and decision smoke

The existing `eval-decisions` runner generated six workflow probes per arm using real top-1 query/get results. Each fresh answering session completed all blind answers before reading injected prompts, then read the rubric and scored. The final metadata retune produced byte-identical blind/injected payloads to the completed candidate arm.

| Observation | Before | Candidate |
|---|---:|---:|
| Selected top-1 outside the declared expected set | 3/6 | 1/6 |
| `moved-toward` | 0/6 | 0/6 |
| `no-change`, already correct blind | 6/6 | 6/6 |
| `harmful` | 0/6 | 0/6 |

The candidate still injects recovery guidance for the loopback schema scenario. Startup and repair select ownership instead of the previous delivery/recovery entries. Accepted sets allow either ownership or acceptance-path where both decisions apply.

All blind answers already preserved independent ingress, tenant authorization, atomic stock reservation, transport identity/correlation, native/model quality, and repair/stop prerequisites. No causal decision improvement was observed. One model-judged round per arm, with different answering sessions and no sensitivity control, cannot establish stable safety or task-quality improvement. This is a single-decision smoke, not a real coding benchmark.

The runner's printed `injection_source_mismatches: 0` checks only string expectations, not workflow expected-set lists. The table above uses actual set membership; the JSON records this reporting limitation and actual selected IDs.

Forced body injection proves provision of text in these probes, not autonomous trigger recognition, lookup timing, or comprehension. The README routing instruction is an adoption suggestion only: no Lorelum project AGENTS.md, skill, or host Plugin was changed or exercised.

## Semantic attempt and limits

An isolated HOME/runtime used the existing fixed GGUF model through an explicit read-only model path, CPU/4 threads, downloads disabled. The initial single-vector query failed with `backend.deadline-exceeded`; model status reported `embedding.deadline-exceeded`. After guarded shutdown of this isolated instance, a restart with an explicit evaluation-only 120000 ms request budget still failed `model load` with `embedding.deadline-exceeded`. This does not establish that the native operation itself ran for 120 seconds.

Semantic quality was not evaluated; multi-view was not attempted after the shared model-load failure. No empty or failed result is counted as a successful retrieval. The isolated Backend was stopped with `--if-idle`; the user's Backend was not stopped. No product guard, contract, timeout default, or user configuration was changed to make evaluation pass.

These observations support a limited keyword routing improvement for lifecycle tasks and successful body delivery. They do not establish stable automatic triggering, semantic improvement, or changed downstream decisions.
