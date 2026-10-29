# CEAIA workflow contract v5

This file owns shared state, invalidation and repair policy. Generation and review use it; `ceaia-sdlc-jira-export` consumes its current validated handoff. It defines local workflow metadata, not a scoring rubric or remote tool schema.

Keep the three active Skill folders together when using filesystem links. On a platform that resolves resources by Skill name instead of sibling directories, resolve this contract as Skill ceaia-sdlc-story-spec-generation, resource references/workflow-contract.md; apply the same name-plus-resource rule to sibling templates. Do not read workspace artifacts from a Skill resource root.

## Identity and paths

Each new candidate owns one Story, Test Case and SPEC. Each update ticket owns one Story/Test Case set and one or more distinct reviewed SPECs when selected attachments require distinct contents. paths.specPath identifies the primary; state.specArtifacts lists every current SPEC. New Story slugs must be unique lowercase kebab-case. Update identity is `source + ticketKey`.

| Value | new_story | existing_ticket_update |
|---|---|---|
| outputRoot | outputs/ceaia/<slug> | outputs/ceaia/updates/<SOURCE>/<TICKET-KEY> |
| internalRoot | .ceaia-work/stories/<slug> | .ceaia-work/updates/<SOURCE>/<TICKET-KEY> |
| planningPath | <internalRoot>/plan-revision-<nnn>.md | <internalRoot>/plan-revision-<nnn>.md |
| storyPath | <outputRoot>/STORY.md | <outputRoot>/STORY.md |
| testCasePath | <outputRoot>/TEST_CASE.md | <outputRoot>/TEST_CASE.md |
| specPath | <outputRoot>/<name>-spec.md | <outputRoot>/<name>-spec.md |
| scoreRecordPath | <internalRoot>/story-quality-score-attempt-<nnn>.md | <internalRoot>/story-quality-score-attempt-<nnn>.md |
| reviewPath | <internalRoot>/review-attempt-<nnn>.md | <internalRoot>/review-attempt-<nnn>.md |
| statePath | <internalRoot>/state.json | <internalRoot>/state.json |
| updateManifestPath | not applicable | <internalRoot>/update-manifest.md |

Determine final SPEC name before Story generation. Internal references use complete workspace-relative paths. Jira-facing Story names only the intended uploaded SPEC, or explicitly states no attachment change.

Every generated SPEC, including a workspace-only SPEC for an update with attachmentAction=none, must have the exact basename `<name>-spec.md`. `<name>` is a non-empty reviewed Story/business-function name in lowercase kebab-case; validate the complete basename against `^[a-z0-9]+(?:-[a-z0-9]+)*-spec\.md$`. Multiple distinct SPECs for one update need distinct evidenced business-function names before the fixed suffix. There is no filename exception for `SPEC.md` or another `.md` basename. If an explicitly requested filename conflicts, resolve a compliant name before Story scoring rather than silently creating or uploading an alias.

The actual Workspace filename is canonical. Keep paths.specPath, every specArtifacts.path/fileName, the SPEC File Name field, planning, Story attachment names when uploaded, standalone Test Case Related SPEC path, update manifest/replacement mappings, review bindings, and upload plan consistent with that file. Final user-facing messages must read back and report the same actual filename/path; never display a planned or shortened name as if it were the saved file.

Jira source is WPB, ALM, DATA, FCR or GO, explicitly confirmed per target. Validate safe path components; reject separators/traversal in ticket/slug components. Do not silently relocate older workspace artifacts: explicitly map and revalidate earlier work when adopting it.

## Latest outputs and retained internal history

Keep only the latest deliverable Story, Test Case and SPEC files under `outputs/ceaia/...`; replace those paths after supported repairs. Do not create historical Story/Test Case/SPEC copies in outputs or `.ceaia-work`.

Preserve workflow evidence under each candidate's `.ceaia-work` internal root. Every planning revision, score invocation result, independent review execution, post-repair verification and Jira export attempt receives a monotonically numbered file and is never overwritten. Examples: `plan-revision-001.md`, `story-quality-score-attempt-001.md`, `review-attempt-001.md`, `review-attempt-001-post-repair.md`, and `jira-export-attempt-001.md`. Never renumber or reuse an earlier attempt file. State points to the latest applicable file and may be compactly replaced.

The score record is Markdown for user-readable audit. It contains a concise readable summary and the complete untouched tool JSON text in a fenced `json` block. The readable summary must not replace or edit the raw response. Review records remain full Markdown reports. Preserve original user evidence and original Jira baseline separately.

Maintain compact candidate state plus `.ceaia-work/batch-state.json`: selected candidate IDs, source assignments, per-candidate status, global source coverage and known write outcomes. Do not duplicate document bodies in state. State and batch state are mutable control files, not audit logs. For final preparation retain each numbered Jira export-attempt record under `.ceaia-work`; `jira-preview.md` remains the one current user preview required by the exporter. State references the latest export attempt, request revision and indices.

Invalidate affected gates before modifying a file; after writing and reading back, advance its workflow revision and record the supported change. Interrupted writes remain invalid and resume at the earliest affected step. Existing stale Test Case/SPEC may remain at current paths but are not deliverable until rebuilt/revalidated. Do not delete files to satisfy initial-generation sequencing.

## Text-only execution and current-content checks

This platform does not execute code. Never generate or run Python, shell, JavaScript or other scripts for this workflow. JSON examples/state/task files are text data, not programs. Use the platform's existing file, scoring, review and Jira tools directly; no new checksum/execution tool is required.

Set executionProfile=text-only and bindingMethod=readback-and-revision. Maintain workflow-owned integer revisions, initially 0 for unwritten files: storyRevision, testCaseRevision, and each SPEC's bodyRevision/fileRevision. Advance the affected revision after a real edit and successful read-back. SPEC body edits advance both revisions; header-only synchronization advances fileRevision only. These counters describe observed workflow edits, not cryptographic fingerprints or platform version IDs.

After independent content-review PASS, synchronize and read back the primary SPEC Review Result/Result and full file. Before scoring read the saved Story in full and reuse the just-verified complete SPEC read-back when no intervening write occurred; otherwise re-read that SPEC. Record storyRevision, primary SPEC fileRevision/path, reviewAttempt, source `Result: PASS` and submitted case-sensitive `specFileStatus: Pass`. Call the scorer with the Story relativePath, that mapped value and this SPEC's specFilePath; never send raw `PASS` or lowercase `pass`. Do not edit either file during the call. Allocate the next unused score attempt path, save/read back the complete Markdown response, and record assessmentOrdinal plus actual invocation/review IDs if returned. Confirm current files still match the submitted content using available reads and the observed edit sequence. Never fabricate checksums or invocation IDs; preserve a service content_checksum only as returned. A cache hit can reuse the Story score while persisting the new SPEC metadata.

Reviewer reads actual current source and artifact text and records the revisions it reviewed. It does not require or invoke a score before content-review PASS. Only the Review Result header fields Result, Reviewer Notes and the derived Story Score display are excluded from substantive SPEC body review. A currentness label or matching counter alone never proves unchanged content. Review PASS is a content verdict; final Jira readiness additionally requires a passing current Story score bound to that verified SPEC status/path.

After a real numeric score is persisted, apply [SPEC score display](spec-score-display.md) before readiness: write only the third header field, read back every current SPEC and finalize the primary SPEC revision through the existing normal cached metadata call when needed. Keep the first two review fields and reviewed business body unchanged. A displayed number never replaces the raw score/parser gate or authorizes an export by itself.

Before Jira export, read the complete current request and intended attachment text, record requestRevision and per-file revisions, and show the exact final preview. Immediately before sending, re-read the request and attachments and compare them with that preview. Any difference invalidates the export binding and affected gates; regenerate the preview and repeat the preflight. Do not claim this procedure provides atomic or cryptographic immutability.

On resume, read current files and the latest state/report before reusing gates. Use real platform version references if already exposed, without requiring that capability. If an external edit, interrupted write, lost context or unclear association prevents establishing currentness, mark only affected gates stale and re-establish them from the readable current content. Do not repeatedly invalidate a gate just because no hash exists. If safe final comparison cannot be made, pause for re-review, regenerated preview or clarification rather than sending uncertain content.

At intake check file reading/writing, the scoring tool and independent-review capability. Lack of a script runtime or hashing capability is NOT a WAITING_TOOL reason. Missing access to the actual files/tools can still block the affected step.

## Minimum current state

Initialize from [state template](../templates/state-template.json) and [batch-state template](../templates/batch-state-template.json). Replace example identity/paths, derive update paths from the table and add updateManifestPath for updates. Templates create metadata only, not future Test Case/SPEC files. Unknown values remain null or pending; never copy a ready/pass assertion from a prior candidate.

Valid JSON, real booleans/numbers, null for unavailable values, no secrets:
- `contractVersion: 5`, `candidateId`, `mode`, `source`, `ticketKey`, `attachmentAction`.
- `evidenceRevision`: changes only for substantive supported source/decision changes, with a short reason.
- `paths`: latest planningPath, storyPath, testCasePath, specPath, latest scoreRecordPath, latest reviewPath; updateManifestPath for updates. Add `historyRoot` for the candidate's retained internal records.
- `status`: IN_PROGRESS / READY / WAITING_USER / WAITING_TOOL / STOPPED / COMPLETE; `nextAction`. Initialize IN_PROGRESS, never READY before all gates.
- `score`: status (missing/pending/pass/fail/unknown), storyRevision, scoreAttempt, assessmentOrdinal, responseReadBack, persistenceMode, invocationRef, ok, finalScore, inputMode=workspace, specFileStatus, specFilePath, specFileRevision and reviewAttempt. Store the actual call inputs separately from the untouched raw response. A pre-change score without verified SPEC metadata is not the final-score handoff.
- `review`: result (Not Reviewed/PASS/NEEDS_REVISION/NEEDS_HUMAN_CLARIFICATION/FINAL_WITH_UNRESOLVED), reviewAttempt, recordPath, storyRevision, testCaseRevision, specBodyRevision, contentReadBack, testCaseTitleValidation, unresolvedIssueIds, specHeaderSync.
- `repair`: defaultRoundLimit=1, round (0–1 without an authorized extension), usedInRound, lastBlockingIssueIds, reasonForBudgetReset, extraRoundsAuthorized (additional rounds, default 0), cumulativeRounds. Retain stagnantRounds/diagnosticUsed only for compatibility; they grant no extra attempt.
- `technicalRecovery`: operation, attempts, outcome.
- `executionProfile: text-only`, `bindingMethod: readback-and-revision`, `artifactRevisions` (storyRevision/testCaseRevision), `jiraExportRequested`, `jiraReady`, `updateReady`, `finalSpecRevision`, `exportPreparation` (status, exportAttempt, requestRevision, attachmentRevisions, contentReadBack), per-operation `writeResults`. In the bundled end-to-end workflow initialize `jiraExportRequested=true` even when the opening request does not mention Jira export. Set it false only for an explicit preparation-only/no-Jira request or an explicit whole-workflow preparation-only assignment. A task's internal generation-only stage partition preserves the true value for its later Jira stage.
- `specArtifacts`: every current SPEC's path/fileName/uploaded/bodyRevision/fileRevision; `attachmentReplacementMapping`: each selected returned attachment ID and corresponding reviewed relativePath/fileName.
- `specScoreDisplay`: pending/verified state, current storyRevision, exact finalScore, source scoreRecordPath, verifiedSpecPaths, primarySpecFileRevision and separate metadataFinalized flag. Display verification may be true for a low/parser-blocked score while export readiness remains false; do not make metadata-only calls for that blocked score. No prior Story's score is current after a Story edit.
- `exportContext`: update target/source/project/actual Story type with available evidence, baseline Description/AC retrieval statuses and saved paths, completed attachment inventory path, known current field/relationship values and explicit unavailable-field markers. Reuse intake reads; do not invent unsupported Jira response fields. Export resolves missing destination facts through actual evidence or focused clarification, not another routine remote baseline read. Keep a selected persistent user-profile key and current settings-confirmation revision here; user-workspace profiles never contain conversation artifact paths or score bodies.
- `reviewAttempt`: monotonic number of actual reviews, not repair allowance. `scoreAttempt` is the monotonic number of actual score-tool invocations. `aiSelfRepairCount` is a compatibility alias of repair.cumulativeRounds; the active allowance comes from repair.round/evidenceRevision/extraRoundsAuthorized. Preserve each numbered score/review record and keep only pointers/counters in state.

Normalized workflow facts are separate from the complete current score response; never insert state fields into that response.

## Invalidation matrix

| Change | Invalidated | Return point |
|---|---|---|
| Any Story content | score, tests/SPEC alignment, content review, readiness, preview/export binding | repair dependent artifacts, review current content, synchronize SPEC, then score final Story |
| Test Case content | SPEC test embedding, affected content review, final SPEC score-metadata binding, readiness, export binding | fix tests and SPEC; review, synchronize SPEC and submit current score metadata |
| Any primary/additional SPEC body | affected SPEC/cross-artifact review, final SPEC score-metadata binding, readiness, export binding | affected content review, synchronize SPEC and submit current score metadata |
| SPEC filename/path or reference change | affected artifact references, review/readiness, score metadata and export binding; Story score content if STORY.md changes | synchronize actual file/references, review affected content and submit final score metadata |
| Accurate review-header synchronization only | final SPEC file revision, score metadata and export binding | read back header, then score/submit final metadata; no substantive review rerun |
| Derived Story Score header only | final SPEC file revision, score metadata and export binding; retain unchanged business-content review | follow spec-score-display.md; read back and finalize metadata once as needed, without a display/rescore loop |
| Business source/scope or attachment selection | affected planning/content/preservation review/readiness/score metadata/export binding | impact analysis; review affected content and submit final metadata after stabilization |
| Jira routing-only field | destination compatibility and export binding | destination checks/preview; review if business/mapping changes |
| External change to original ticket | preservation review and export binding | reconcile baseline with requested delta |

Existence is not currentness. Never carry a score to changed Story content or an old SPEC-status binding to changed SPEC content/status/path. A revised SPEC with unchanged Story may use the scorer's normal cache while saving current SPEC metadata; do not force_review merely to refresh metadata. Low score does not authorize altering source requirements.

## Repair budget and transitions

A repair round is one consolidated artifact-edit batch followed by affected independent verification, SPEC header synchronization and final Story scoring where eligible. Initial generation/review is round 0. Review-driven and score-driven repairs share **one automatic round per candidate per evidenceRevision**. The effective limit is `1 + repair.extraRoundsAuthorized`; only explicit user authorization can add a finite number of rounds. Normally this means one initial content review and, if repaired, one post-repair content review, not two repair allowances.

Collect all known supported findings into the one targeted batch. Before editing increment round and cumulativeRounds once, set usedInRound=true, and record affected files/evidence. Recheck every invalidated gate and cross-artifact consistency, retaining still-current evidence for unchanged gates. Post-repair verification is read-only with aiSelfRepairAllowed=false. A post-score content repair also requires affected independent review before scoring the changed Story; the earlier PASS cannot authorize changed material.

Classify:
- Supported generation defect: local repair from existing evidence.
- Missing/conflicting business fact: WAITING_USER immediately.
- Initial generation precheck: keep its one mechanical correction/check before independent review. Once the initial review starts, any Story/Test Case/SPEC body, filename or reference repair, including formatting or parser-shape correction, consumes the shared artifact-repair allowance. Do not bypass it by labeling an edit mechanical or technical.
- Review/score header-only synchronization and tool/persistence problems: use their existing bounded recovery, not another artifact-repair round. An unchanged write retry stays in its original batch under existing retry/outcome rules; technical recovery cannot introduce another artifact correction.

Track blockers by requirement/behavior/location, not score. If the sole repair is used and a required review or score still needs artifact correction, stop automatic editing immediately: keep jiraReady/updateReady=false, set WAITING_USER and return the remaining blockers and smallest evidence/decision/finite-extension/defer/stop choice. Do not run a second repair, unchanged rescore or another diagnostic reviewer to keep trying. Summarize diagnosis from existing findings; pure technical failures retain their bounded WAITING_TOOL/recovery path.

Resume with the existing counters and numbered records. A new session, new stage, failed check, score result, generated revision or cosmetic rename never restores the allowance. For older state without defaultRoundLimit use 1; an old three-round default does not authorize another edit once round>=1. Preserve valid current PASS gates and finish eligible verification/scoring without adding an edit. Record any explicit finite extra-round authorization, candidate/evidence scope and added count before editing; never infer it from an automatic retry/resume or a generic continuation that does not authorize another artifact repair.

Actual substantive source evidence or user decisions may start a new evidenceRevision with one automatic round for affected candidates only. Record the actual source/decision and reset reason; repair output itself is not new evidence. Reset only the active round/usedInRound and revision-scoped extra authorization, never cumulativeRounds, aiSelfRepairCount or score/review attempt numbering. Retain historical records without reloading them into every review.

## Handoff, batch and internal boundaries

Reviewer reads actual sources/artifacts, not generator summaries. All selected candidates need current independent content-review PASS, verified SPEC header/status/path, passing final workspace Story score with that SPEC metadata, and batch-level source coverage before Jira preparation. After those gates pass, the bundled workflow proceeds automatically to Jira create/update; it does not require export wording in the initial request or a continue prompt. Record globalCoverage.status=pass only after reconciling original inScopeSRIds with coveredSRIds, explicitly supported exclusions and zero pendingSRIds. Bind that conclusion to current evidence revisions; do not silently remove an SR to make counts match. A blocked candidate does not prevent independent work on unrelated candidates. Removing it from the selected Jira batch requires user direction.

Handoff to `ceaia-sdlc-jira-export` contains statePath, actual source/planning paths, identity, artifact paths, exportContext and updateManifestPath where applicable. Include pointers to the already completed original Description/AC and attachment inventory for updates. Receiving Skill validates current workspace evidence before resuming, not repeating Jira intake calls. A complete matching persistent user-workspace field profile skips project/type/metadata queries across sessions; no time-based refresh is required. Each export confirms its actual settings, including update Ticket Number and Epic/Parent decision, before exact preview/preflight. Only profile miss, explicit change/refresh or a documented schema-error recovery needs the affected query.

Internal update manifest contains baseline preservation and attachment selection, not score/audit fields. Score records/provenance stay internal; only the exact numeric Story Score total may appear in the designated SPEC header. Final Jira preview shows actual business changes and upload mapping, not diagnostics. Jira issue fields never receive the score; the approved SPEC attachment includes its score header. Test Case is user-visible workspace content, never a standalone Jira attachment.

The export binding refers to the exact serialized Jira-write payload and final attachment content checked by the read-back procedure above. The Story-only SDLC exporter calls the Jira export tool directly after this preflight; no separate approval tool is used. Current state holds bindings/results; numbered internal export-attempt records are retained, while historical Story snapshots are neither needed nor allowed.

## Waiting and completion

WAITING_USER/WAITING_TOOL returns one blocker and next action, then yields. Do not loop without new input. STOPPED requires explicit stop and is not PASS. Stage completion is distinct from confirmed Jira success. Whole-workflow success requires every intended operation confirmed.
