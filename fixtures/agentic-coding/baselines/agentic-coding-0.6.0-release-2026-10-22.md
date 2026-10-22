# agentic-coding 0.6.0: official release verification

Date: October 22, 2026. This record proves the supported distribution and readback paths for the exact release; it is not proof of stable automatic triggering or downstream decision improvement.

## Published identity

- Pack: `agentic-coding@0.6.0`, 35 Practices.
- Source: merged [PR #38](https://github.com/lorelum/lorelum-packs/pull/38), merge commit `547a13fb7cf150b29318aac85beaec7dfe3526a2`.
- Immutable artifact ref: [`agentic-coding-v0.6.0`](https://github.com/lorelum/lorelum-packs/tree/agentic-coding-v0.6.0), resolving to [`68a4be166a488166931330f9dfd5afd01d807421`](https://github.com/lorelum/lorelum-packs/commit/68a4be166a488166931330f9dfd5afd01d807421).
- Official Registry: `lorelum-official`, repository `lorelum/lorelum-packs`; entry added in [`40b4acef3a8b2e8df218080b64ebce1887d8231e`](https://github.com/lorelum/lorelum-packs/commit/40b4acef3a8b2e8df218080b64ebce1887d8231e), after the tag existed remotely.
- Installed artifact digest: `7b56576fa94309a07ce07b88f352083ece8625709db294b46e2ae1f3302a7cdf`.
- Unmerged PR #36 is excluded. No old tag was moved; the existing `0.5.1` artifact remains unchanged.

The release promotion changed version metadata, current fixture identity, and documentation only. Canonical Practice Markdown is unchanged from the merged PR #38 guidance. The Registry description now matches `pack.yaml`, including the pre-implementation trigger for added checks.

## Environment and source validation

Used the official Lorelum `0.1.0-alpha.6` Windows x64 archive, protocol 2. Its SHA-256 matched the release asset digest `00dce687cd152103377dc0e05d5d697e599f423733a91c5ae3c7d5310dde8ce9` before extraction/execution.

The release was prepared in a separate Git worktree. Validation and installation used isolated Store, HOME, and derived-cache locations; no user installation, Store, model, or configuration was updated. Model downloading was explicitly disabled and no model was configured in that isolated HOME.

`lore validate packs/agentic-coding --json` returned valid with no Pack diagnostics. All 35 zh-CN translations were current, none stale or missing. The release metadata passed the whitespace check and the selected-content sensitive-pattern scan.

## Official installation smoke

The commands below show the public argument path; `lore` was the verified alpha.6 executable and the Store arguments selected new temporary destinations. No `--path` source or custom Registry override was used.

```sh
lore --json --store-root <clean-store> pack install agentic-coding@0.6.0
lore --json --store-root <clean-store> pack list agentic-coding
lore --json --store-root <clean-store> get agentic-coding.implementation.justify-added-checks
lore --json --store-root <clean-store> query "Make backend startup reliable when the model directory contains thousands of files." --mode keyword --top-k 3
```

Observed:

- Install exit 0, `ok: true`, exact Pack/version `agentic-coding@0.6.0`, official Registry repository, exact ref and artifact commit above; 35 IDs added, empty Pack diagnostics, no pending cleanup.
- Catalog readback returned the same Pack/version and 35 Practices.
- `get` returned `agentic-coding.implementation.justify-added-checks` and its full body. Body SHA-256 `34b3d7b6585f55c50cacf5fabd90885b6b6878b8baa80a2d140e3f84bfc6ea24` matched the reviewed candidate guidance.
- The installed startup query ranked `justify-added-checks` first, `keep-acceptance-path-completable` second, and `surface-unconfirmed-assumptions` third.

## Existing-user upgrade smoke

A separate clean Store first installed `agentic-coding@0.5.1` through the official Registry. Then the unversioned public update path was exercised:

```sh
lore --json --store-root <upgrade-store> pack update agentic-coding
lore --json --store-root <upgrade-store> pack list agentic-coding
lore --json --store-root <upgrade-store> get agentic-coding.implementation.justify-added-checks
```

The default update resolved `0.6.0` at the same exact artifact commit. It added the new Practice and changed four existing entries: `confirm-product-surface-expansion`, `validate-at-the-owning-boundary`, `keep-acceptance-path-completable`, and `plan-sufficient-evidence`. `invalidated` was empty. Catalog comparison confirmed all original 34 IDs remained and the new ID was the sole addition; its full body was readable after upgrade.

The verification driver initially tried to print a nonexistent `delta.removed` field. The actual CLI delta fields are `added`, `changed`, and `invalidated`; the driver was corrected without repeating the successful old-version installation. Preservation was checked against the actual before/after ID sets, not an assumed absent field.

## Installed-release retrieval gate

Ran the existing `scripts/eval-queries` against the official installed artifact with `--mode keyword --require-coverage --check-discipline`, the current 200-query fixture, and its 90% positive top-3 threshold. The JSON wrapper only requested the standard public `--json` envelope; it changed no retrieval behavior.

- Exit 0; 200 runnable queries, none skipped; all 35 Practices covered.
- No query-discipline violations.
- Positive top-1: 107/120 (89.2%); positive top-3: 117/120 (97.5%), gate PASS.
- Neighbor expected top-1: 47/80 (58.8%); expected top-3: 69/80 (86.2%); trap top-1: 18.8%.

[Generated keyword report](./agentic-coding-0.6.0-keyword-2026-10-22.md) and [complete run JSON](./agentic-coding-0.6.0-keyword-2026-10-22.json) retain misses and confusion. These are observed selections for one fixture and environment, not a stability guarantee. Earlier expectation migrations and candidate comparisons remain separately documented in the [independent-entry evaluation](./agentic-coding-independent-check-alpha6-2026-10-22.md).

## Semantic and behavior limits

Installation succeeded, but its post-install `indexSync` reported `failed / embedding.not-configured`, consistent with this intentionally model-free, downloads-disabled environment. This is not counted as a successful semantic build. Keyword and get completed independently; Pack installation was not rolled back. No single-vector or multi-view retrieval quality result is claimed. The earlier alpha.6 Windows model-load deadline failure was not rerun merely to confirm it.

The isolated Backend was stopped with `backend stop --if-idle`, returning `stopped`, before temporary runtime cleanup. No user Backend was stopped.

The release does not deploy project AGENTS.md / required-skill routing or establish repeated host-level triggering. Earlier controlled design sessions remain limited evidence with no blind causal control. Publication and installation establish availability, not universal applicability, safety, or decision improvement.
