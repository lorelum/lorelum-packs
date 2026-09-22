# Redaction-withholding decision probe — 2026-09-22

Evaluation-only record for the `0.6.0` extension of
`agentic-coding.planning.keep-acceptance-path-completable` to information
withholding. Ad-hoc three-arm smoke test plus a retrieval A/B, not a run of the
`eval-decisions` emission pipeline; scoring is judgment with quoted evidence.

## Trigger

Sanitized internal trial feedback (2026-09-22): while planning a local
single-user CLI's diagnostics and logs commands, an agent drafted the blanket
requirement that text and JSON output never expose real paths, permission
bits, log contents, or internal errors — conflating the local diagnostic
surface with material bound for external feedback.

## Method

Three fresh answering sessions (one per arm, no tools, identical task):
write acceptance criteria for a local developer CLI's `doctor` and `logs`
output (text and JSON), explicitly noting output may later be bundled into
user-submitted feedback. Injected arms received the full Practice text under
the compliance framing calibrated by the 2026-09-18 paired-delta run ("a team
practice you have decided to follow").

- **Arm A (blind)** — 0.5.1 Practice not shown.
- **Arm B (injected, current 0.5.1 text)** — published Practice as installed.
- **Arm C (injected, 0.6.0 candidate text)** — adds the withholding clause.

## Results

| Arm | Decision on local-output facts | Decision on redaction | Verdict |
| --- | --- | --- | --- |
| A blind | paths/observed values kept; log contents redacted "same as config"; raw stack traces behind a debug flag | masking justified *because output is expected to be pasted into feedback reports* | conflation present (the incident's mechanism arises spontaneously) |
| B 0.5.1 | checks well-calibrated ("never just 'config invalid'", no unanswerable confirms, labeled `REDACTED(api-key)` masking) | masking designed into local output so it is "safe to paste into a feedback report with no extra editing step" | `no-change` on the conflation; improves only the dimensions the 0.5.1 text names |
| C 0.6.0 | "real paths, permission bits, timestamps, log content, and failure reasons are never masked or omitted from local output" | "Redaction for external feedback happens solely at the export boundary" via an explicit bundle command that names redacted fields | `moved-toward` expected behavior |

Quoted evidence (verbatim from arm outputs):

- A: "Log entry contents must receive the same redaction as config" ·
  "Unexpected internal errors must not produce raw stack traces by default" ·
  "because output is expected to be pasted into user-submitted feedback reports".
- B: "Masking is the only sensitivity mechanism — neither command refuses to
  produce output, requires an unlock flag, or prompts to view entries — so the
  default output is safe to paste into a feedback report with no extra editing step."
- C: "Only values that could authenticate elsewhere — API tokens, keys,
  passwords, and credentials embedded in config values or query strings — are
  masked in text and JSON output, while real paths, permission bits,
  timestamps, log content, and failure reasons are never masked or omitted
  from local output." · "Redaction for external feedback happens solely at the
  export boundary."

## Retrieval A/B (project-local layers, lore 0.1.0-alpha.3, single-pack context)

Scenario queries against HEAD (0.5.1) vs the 0.6.0 candidate:

| Query | HEAD keyword | candidate keyword | HEAD semantic | candidate semantic |
| --- | --- | --- | --- | --- |
| "planning a local CLI diagnostics command, should text and JSON output hide real file paths…" | n/a (not run) | top-1 | top-1 | top-1 |
| "CLI 诊断输出要不要为了安全隐藏真实路径和权限位" (zh colloquial) | n/a | top-1 | rank 3 | top-1 |
| fixture p4 (new: doctor spec hides install locations) | not in top-3 | top-1 | not in top-3 | not in top-3 |

Fixture regression, keyword mode (the gating mode; semantic gate already
failing at 78% on the recorded 0.5.0 baseline and unchanged by this diff —
the miss set is byte-identical to an unmodified 0.5.1 control):

| Query | HEAD | 0.6.0 candidate |
| --- | --- | --- |
| keep-acceptance-path-completable.p1 | top-1 | top-1 |
| keep-acceptance-path-completable.p2 | rank 3 | top-1 |
| keep-acceptance-path-completable.p3 | rank 2 | rank 2 |
| keep-acceptance-path-completable.p4 (new) | not in top-3 | top-1 |
| neighbor n1 (expect validate-at-the-owning-boundary) | top-1 | rank 2 |
| neighbor n2 (expect validate-at-the-owning-boundary) | rank 3 | rank 3 |

Intermediate finding recorded for future edits: the first candidate draft
(+~120 common words across applies_when/body) pushed p2 out of the top-5 —
the keyword index scores the whole document and dilutes marginal matches.
Compacting the applies_when clause and adding the batch-import failure-walk
instance (aligning the Practice with its catalog paraphrase) restored p2 to
top-1.

## Boundaries

Single responder per arm; judgment scoring; probes cover one decision, not
multi-turn behavior. Retrieval numbers come from project-local layers, not
the isolated-store `eval-queries` harness (which cannot see layers); the
release gate should re-run keyword mode against the recorded baseline once
`0.6.0` has a Registry ref.
