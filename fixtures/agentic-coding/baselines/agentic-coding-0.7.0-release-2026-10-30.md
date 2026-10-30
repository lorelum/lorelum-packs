# agentic-coding 0.7.0: official release verification

Date: October 30, 2026. This record establishes the observed release, official Registry installation, default update, and readback paths. It does not establish stable host triggering or downstream decision improvement.

## Published identity

- Pack: `agentic-coding@0.7.0`, 37 Practices.
- Reviewed content: [PR #39](https://github.com/lorelum/lorelum-packs/pull/39), merged at `4efd8b82d20ed6d873216056bc7fb74060715d5e`.
- [GitHub Release](https://github.com/lorelum/lorelum-packs/releases/tag/agentic-coding-v0.7.0), published, not a draft or prerelease.
- Immutable ref: `agentic-coding-v0.7.0`, pointing to `6715efa7cc152a4e222cca508ee6789a843854b0`. The promotion changes release-status documentation only; canonical guidance is unchanged from the merged PR.
- Official Registry promotion: `0d74cbe41b74c19b8a5cd85b2537b9426cd218b5`, added after the new tag existed remotely. The Registry description matches the Pack description.
- Installed runtime artifact digest: `9fdbea30326eebd39b855723362a826c60d751ba18934de8d4e26813e96d9e84`.
- No older tag was moved. The original 35-Practice `0.6.0` artifact remains available. No unrelated Pack or unmerged PR #36/#37 content was included.

## Official installation and default update

Used Lorelum `0.1.0-alpha.6`, protocol 2, with separate temporary Stores and an overridden temporary HOME/profile environment. No installed user Store was edited. The commands below use the default official Registry: no `--path` or custom Registry source.

```sh
lore --json --store-root <clean-store> pack install agentic-coding@0.7.0
lore --json --store-root <clean-store> pack list agentic-coding
lore --json --store-root <clean-store> get agentic-coding.context.recommend-session-handoff
lore --json --store-root <clean-store> get agentic-coding.planning.organize-work-around-the-solution

lore --json --store-root <upgrade-store> pack install agentic-coding@0.6.0
lore --json --store-root <upgrade-store> pack update agentic-coding
lore --json --store-root <upgrade-store> pack list agentic-coding
```

Observed:

- Exact-version install returned exit 0 and `ok: true`, the expected tag and full artifact commit above, and 37 catalog IDs.
- Both new full bodies matched the canonical source after frontmatter removal and newline normalization.
- The separate upgrade Store first contained 35 IDs at `0.6.0`. The unversioned update selected `0.7.0`, preserved every old ID, and added exactly the two new IDs. Its delta was `added: [the two new IDs]`, `changed: []`, `invalidated: []`.
- Both bodies were read again after upgrade and matched the fresh installation.

| New Practice | Readback body SHA-256 |
|---|---|
| `context.recommend-session-handoff` | `9ee440d87c487ab55deae856cf0ab1d454e835e4c5256fe491bb80e06314dec7` |
| `planning.organize-work-around-the-solution` | `73c0eb875ff122256d0c2b19e7eb30e1cd9e1d5dde929497af285169d8d272d0` |

The first exact-install attempt returned `source.unavailable` after about 24 seconds. Public and isolated-environment Git ref checks subsequently succeeded; a later diagnostic installation using the same official source succeeded. The initial failure record was retained, not counted as a pass or silently replaced. Its underlying cause was not established. Successful installation steps were not repeated when completing later observations.

GitHub CLI/API calls also encountered EOF responses. Publishing through REST with a process-local `GODEBUG=http2client=0` setting succeeded; no persistent user configuration was changed.

## Retrieval and evidence reuse

Two keyword queries from the fixed fixture were exercised against the newly installed official artifact: the gateway continuation case selected `recommend-session-handoff` first; the autosave acknowledgment case selected `organize-work-around-the-solution` first. These are distribution-path smoke observations, not new independent generalization samples.

The installed artifact digest equals the reviewed local candidate's runtime digest. Its prior 210-query keyword evaluation is therefore retained as evidence for the same runtime content rather than rerunning it and presenting duplicate observations as additional evidence:

- 37/37 Practices covered; zero query-discipline violations, skipped queries, or query errors.
- Positive top-3: 123/126 (97.6%); positive top-1: 111/126 (88.1%).
- New-entry positive top-3: 3/3 for handoff, 2/3 for solution-oriented work. The creative-delegation miss remains unresolved, along with two existing acceptance-path misses.
- Neighbor expected top-1: 49/84 (58.3%); expected top-3: 72/84 (85.7%); trap top-1: 15/84 (17.9%).

See the [content adoption and candidate record](./agentic-coding-0.7.0-content-2026-10-30.md) for the initial result, one metadata calibration, exact miss IDs, and the limits of earlier behavior comparisons. The pre-release validation reported zero Pack diagnostics and 37/37 current Chinese companions; this promotion did not change canonical content or its localization mapping.

Post-install semantic `indexSync` reported `ready`, with 37 vectors. That is an index-availability observation, not a semantic ranking-quality evaluation; no new semantic quality, autonomous host routing, causal benefit, token-cost, or superiority claim is made. No user Backend was stopped.
