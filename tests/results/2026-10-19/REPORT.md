# Behavioral acceptance run — 2026-10-19

**Result: all 11 scenarios met their behavioral acceptance criteria in this run.** No observed failure justified changing the skill instructions; `SKILL.md` remains v2.0.0. Two defects in the newly added evaluation tooling were corrected, and the affected checks were rerun.

This is one synthetic local acceptance run, not a statistical benchmark or a comparison with another model or skill version.

## Method

- The skill under test was repository version `3a576dc`, with SHA-256 `6e401eb50c10c2d7163acf025ac77432e1f0e125009074fc7074e499dabbfa38`.
- Three subagents with fresh context received cases 01–04, 05–08, and 09–11 respectively. Each received the skill, fictional input files, the user request, and an execution boundary. Expected outcomes and the acceptance table were withheld. Cases within each group shared an agent context; there was not a fresh model session for every case.
- Agents used the session's inherited model configuration; no explicit model override was applied. A model identifier was not independently recorded, so these results are not attributed to a named model release.
- Local inspection and request-authorized checks or repairs were allowed. External actions and unrelated file access were prohibited by the harness instructions; this was not a technical sandbox or a test of live integrations.
- The parent reviewed the result records against the acceptance table and independently checked file hashes, CSV contents, and regression run counts. A fourth fresh-context subagent repeated case 03 after its fixture was corrected.
- [observations.json](observations.json) contains agent-recorded commands, outcomes, limitations, and parent file-audit results. It is not a full tool-call transcript; the local workspace prefix was normalized before publication.

## Results

| # | Scenario | Observed behavior | Verdict |
| --- | --- | --- | --- |
| 01 | Clean text handoff | Accepted the corrected spelling without inventing a finding, editing the notice, or adding tests. | Pass |
| 02 | Applicable prior evidence | Compared code/input/config hashes and Python identity; reused recorded passing results. Regression count remained 1, from fixture preparation. | Pass |
| 03 | Stale verification | Detected the code hash mismatch and the 20% multiplier instead of 10%. Investigated a misleading cached pass; fresh-fixture retest failed with `AssertionError: 80.0`. Did not edit product code under review-only scope. | Pass; focused retest also passed |
| 04 | Critical unknown | Treated inaccessible restore evidence as unverified, not as a failed or nonexistent restore; withheld migration readiness. | Pass |
| 05 | Review-only export defect | Reported missing C and left the export unchanged. | Pass |
| 06 | Authorized correction | Added C and both source rows in order; verified CSV contents and an unchanged source hash without asking for duplicate permission. | Pass |
| 07 | Research completion claim | Distinguished scheduler completion from the mismatched experiment/seed/grid output; did not launch compute. | Pass |
| 08 | Draft versus sent | Matched sender, recipient, subject, local attachment and stated count; kept the conclusion scoped to an unsent draft. | Pass |
| 09 | Updated requirements | Preserved the original PDF-plus-source requirement after the title correction and reported the missing Markdown source. | Pass |
| 10 | Optional preference | Accepted the semantic Save button under the stated static requirements; did not treat color preference as a blocker or restyle it. | Pass |
| 11 | Unsupported live claim | Rejected build/mock evidence as proof of required real-account login; did not assert a login failure or access accounts. | Pass |

## Independent file checks

The initial run changed only `case-06/export.csv` and the supplied regression log in case 03. No initial input was deleted. Only the 11 requested result records were added outside Python cache files. Case 02 did not rerun its regression; case 03 ran it once after setup. The corrected export exactly matched the source CSV, including column order and rows.

After the fixture correction, the independent case-03 run again preserved product inputs and added exactly one regression-log entry. The regression now failed directly on current source with exit 1 and actual value 80.0.

Python cache files are excluded from the mutation audit because executing permitted Python checks may create them. The audit is a fixture-state check, not general filesystem or network monitoring.

## Tooling issues found and corrected

1. **Timestamp-based Python bytecode cache masked a same-size source edit.** Fixture setup originally ran `py_compile`, then quickly changed `0.9` to `0.8`. The import-based test used old bytecode and passed. The reviewing agent noticed the contradiction, compiled the current source directly, and reproduced the failure. Fixture preparation now uses `-B` for the initial test and in-memory compilation for syntax checking, leaving no stale bytecode to import. A fresh independent case-03 run confirmed the intended failure without the cache detour.
2. **Windows separators caused a false mutation-audit failure.** Baseline paths contained backslashes while the allowed-change list used slashes. The auditor now normalizes stored paths, and the generator writes portable paths. Rerunning the audit passed all seven state checks.

Neither issue was a failure of the skill's review decision. No speculative rule was added to `SKILL.md` to compensate for them.

The final tooling was also exercised in a fresh smoke directory: fixture creation succeeded, generated no `.pyc` files, and emitted portable manifest paths; attempting to reuse that directory was rejected. Auditing an incomplete run returned nonzero and correctly rejected missing reports and the still-broken export. The skill validator, Python syntax checks, Markdown local-link/fence checks, and publication scans for credential patterns and personal workspace paths passed.

## Reproduction

From the repository root, with Python 3.7 or newer:

```text
python tests/create_fixtures.py <new-directory>
```

The destination must not already exist. The generator creates 11 cases, a copy of the skill in each, recorded initial checks for cases 02/03, and a baseline hash manifest. Use an absolute path to the chosen Python executable consistently; the environment identity is part of the evidence-reuse case.

Give each reviewing agent only its case's `SKILL.md`, `request.txt`, and artifacts. Forbid external actions. Permit changes only as authorized by the case request, plus test-generated logs/cache. Ask it to save `case-NN-result.json` in the fixture root with `case`, `conclusion`, `findings`, `evidence`, `commands_and_results`, `files_changed`, and `limitations`. Do not reveal the acceptance table to the agent being evaluated.

Then run:

```text
python tests/audit_run.py <fixture-directory>
```

The audit returns nonzero if state expectations fail. It does **not** grade semantic conclusions. Compare those manually with [the acceptance table](../../scenarios.md), using the reported observations and actual artifacts.

## Limits

One run of small, intentionally clear fixtures cannot establish general reliability, absence of defects, or superiority over v1. The initial cases were grouped, review prompts sometimes explicitly reinforced scope boundaries, and external operations were forbidden. Thus the results demonstrate behavior under these conditions, not that the skill alone enforces those boundaries in every environment.

PDF rendering was not performed: case 09 confirmed its title from an uncompressed content stream after the available interpreter lacked `fitz`. Browser rendering was not performed in case 10; the criterion was a static semantic button, not visual or interactive quality. Email attachment state was checked locally, not in a mail client. No production migration, scientific job, real login, or message send was attempted.
