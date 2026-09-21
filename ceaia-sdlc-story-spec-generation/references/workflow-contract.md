# CEAIA workflow contract v4

This file owns shared state, invalidation and repair policy. Generation, review and the `jira-createissue-helper` SDLC gateway load it. It defines local workflow metadata, not a scoring rubric or remote tool schema.

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
| specPath | <outputRoot>/<final-spec-name>.md | <outputRoot>/<final-spec-name>.md |
| scoreRecordPath | <internalRoot>/story-quality-score-attempt-<nnn>.md | <internalRoot>/story-quality-score-attempt-<nnn>.md |
| reviewPath | <internalRoot>/review-attempt-<nnn>.md | <internalRoot>/review-attempt-<nnn>.md |
| statePath | <internalRoot>/state.json | <internalRoot>/state.json |
| updateManifestPath | not applicable | <internalRoot>/update-manifest.md |

Determine final SPEC name before Story scoring. Internal references use complete workspace-relative paths. Jira-facing Story names only the intended uploaded SPEC, or explicitly states no attachment change.

Default SPEC basename is short lowercase kebab-case. An explicit user-requested safe .md basename may override that default; retain the actual confirmation and exact name in explicitSpecFileNames. This exception does not allow unsafe paths or invented filenames.

Jira source is WPB, ALM, DATA, FCR or GO, explicitly confirmed per target. Validate safe path components; reject separators/traversal in ticket/slug components. Do not silently relocate older workspace artifacts: explicitly map and revalidate earlier work when adopting it.

## Latest outputs and retained internal history

Keep only the latest deliverable Story, Test Case and SPEC files under `outputs/ceaia/...`; replace those paths after supported repairs. Do not create historical Story/Test Case/SPEC copies in outputs or `.ceaia-work`.

Preserve workflow evidence under each candidate's `.ceaia-work` internal root. Every planning revision, score invocation result, independent review execution, post-repair verification and Jira approval attempt receives a monotonically numbered file and is never overwritten. Examples: `plan-revision-001.md`, `story-quality-score-attempt-001.md`, `review-attempt-001.md`, `review-attempt-001-post-repair.md`, and `jira-approval-attempt-001.md`. Never renumber or reuse an earlier attempt file. State points to the latest applicable file and may be compactly replaced.

The score record is Markdown for user-readable audit. It contains a concise readable summary and the complete untouched tool JSON text in a fenced `json` block. The readable summary must not replace or edit the raw response. Review records remain full Markdown reports. Preserve original user evidence and original Jira baseline separately.

Maintain compact candidate state plus `.ceaia-work/batch-state.json`: selected candidate IDs, source assignments, per-candidate status, global source coverage and known write outcomes. Do not duplicate document bodies in state. State and batch state are mutable control files, not audit logs. For final preparation retain each numbered approval record under `.ceaia-work`; `jira-preview.md` remains the one current user preview required by the helper. State references the latest approval attempt, request revision and indices.

Invalidate affected gates before modifying a file; after writing and reading back, advance its workflow revision and record the supported change. Interrupted writes remain invalid and resume at the earliest affected step. Existing stale Test Case/SPEC may remain at current paths but are not deliverable until rebuilt/revalidated. Do not delete files to satisfy initial-generation sequencing.

## Text-only execution and current-content checks

This platform does not execute code. Never generate or run Python, shell, JavaScript or other scripts for this workflow. JSON examples/state/task files are text data, not programs. Use the platform's existing file, scoring, review and Jira tools directly; no new checksum/execution tool is required.

Set executionProfile=text-only and bindingMethod=readback-and-revision. Maintain workflow-owned integer revisions, initially 0 for unwritten files: storyRevision, testCaseRevision, and each SPEC's bodyRevision/fileRevision. Advance the affected revision after a real edit and successful read-back. SPEC body edits advance both revisions; header-only synchronization advances fileRevision only. These counters describe observed workflow edits, not cryptographic fingerprints or platform version IDs.

Before scoring read the saved Story in full, record its path and storyRevision, and invalidate the old score. Do not edit that Story during the call. Allocate the next unused score attempt path, save/read back the complete Markdown score record, and record assessmentOrdinal plus actual invocation/review IDs if returned. Confirm the current Story still matches the submitted content using available file reads and the observed edit sequence. Never fabricate checksums or invocation IDs; preserve a service content_checksum only as returned.

Reviewer reads actual current source and artifact text, compares it with the current score association, and records the revisions it actually reviewed. Only the two-field Review Result header is excluded from substantive SPEC body review. A currentness label or matching counter alone never proves unchanged content.

Before approval read the complete current request and intended attachment text, record requestRevision and per-file revisions, and show the exact supported approval preview. Between approval and sending perform no edits; re-read the request and attachments and compare them with the content just approved. Any difference invalidates approval and affected gates. Do not claim this procedure provides atomic or cryptographic immutability.

On resume, read current files and the latest state/report before reusing gates. Use real platform version references if already exposed, without requiring that capability. If an external edit, interrupted write, lost context or unclear association prevents establishing currentness, mark only affected gates stale and re-establish them from the readable current content. Do not repeatedly invalidate a gate just because no hash exists. If safe final comparison cannot be made, pause for re-review/re-approval or clarification rather than sending uncertain content.

At intake check file reading/writing, the scoring tool and independent-review capability. Lack of a script runtime or hashing capability is NOT a WAITING_TOOL reason. Missing access to the actual files/tools can still block the affected step.

## Minimum current state

Initialize from [state template](../templates/state-template.json) and [batch-state template](../templates/batch-state-template.json). Replace example identity/paths, derive update paths from the table and add updateManifestPath for updates. Templates create metadata only, not future Test Case/SPEC files. Unknown values remain null or pending; never copy a ready/pass assertion from a prior candidate.

Valid JSON, real booleans/numbers, null for unavailable values, no secrets:
- `contractVersion: 4`, `candidateId`, `mode`, `source`, `ticketKey`, `attachmentAction`.
- `evidenceRevision`: changes only for substantive supported source/decision changes, with a short reason.
- `paths`: latest planningPath, storyPath, testCasePath, specPath, latest scoreRecordPath, latest reviewPath; updateManifestPath for updates. Add `historyRoot` for the candidate's retained internal records.
- `status`: IN_PROGRESS / READY / WAITING_USER / WAITING_TOOL / STOPPED / COMPLETE; `nextAction`. Initialize IN_PROGRESS, never READY before all gates.
- `score`: status (missing/pending/pass/fail/unknown), storyRevision, scoreAttempt, assessmentOrdinal, responseReadBack, persistenceMode, invocationRef, ok, finalScore.
- `review`: result (Not Reviewed/PASS/NEEDS_REVISION/NEEDS_HUMAN_CLARIFICATION/FINAL_WITH_UNRESOLVED), reviewAttempt, recordPath, storyRevision, testCaseRevision, specBodyRevision, contentReadBack, testCaseTitleValidation, unresolvedIssueIds, specHeaderSync.
- `repair`: round (0–3 by default), usedInRound, stagnantRounds, lastBlockingIssueIds, diagnosticUsed, reasonForBudgetReset, extraRoundsAuthorized (default 0).
- `technicalRecovery`: operation, attempts, outcome.
- `executionProfile: text-only`, `bindingMethod: readback-and-revision`, `artifactRevisions` (storyRevision/testCaseRevision), `jiraReady`, `updateReady`, `finalSpecRevision`, `approval` (status, requestRevision, attachmentRevisions, contentReadBack), per-operation `writeResults`.
- `specArtifacts`: every current SPEC's path/fileName/uploaded/bodyRevision/fileRevision; `attachmentReplacementMapping`: each selected returned attachment ID and corresponding reviewed relativePath/fileName.
- `reviewAttempt`: monotonic number of actual reviews, not repair allowance. `scoreAttempt` is the monotonic number of actual score-tool invocations. `aiSelfRepairCount` is a compatibility alias of repair.cumulativeRounds; the active allowance comes from repair.round/evidenceRevision/extraRoundsAuthorized. Preserve each numbered score/review record and keep only pointers/counters in state.

Normalized workflow facts are separate from the complete current score response; never insert state fields into that response.

## Invalidation matrix

| Change | Invalidated | Return point |
|---|---|---|
| Any Story content | score, tests/SPEC alignment, review, readiness, preview/approval | score current Story; repair affected downstream |
| Test Case content | SPEC test embedding, review, readiness, approval | fix tests; synchronize SPEC; review |
| Any primary/additional SPEC body | affected SPEC review, cross-artifact review, readiness, approval | affected SPEC checks and review |
| Accurate review-header synchronization only | final SPEC file revision and approval | bind final file; no score or review rerun |
| Business source/scope or attachment selection | affected planning/content/preservation review/readiness/approval | impact analysis; rescore only if Story changes |
| Jira routing-only field | destination compatibility and approval | destination checks/preview; review if business/mapping changes |
| External change to original ticket | preservation review and approval | reconcile baseline with requested delta |

Existence is not currentness. Never carry a score to changed Story content. Low score does not authorize altering source requirements.

## Repair budget and transitions

A repair round is one consolidated content-edit batch followed by affected scoring and independent verification. Initial generation/review is round 0. Score-driven and review-driven repairs share three rounds per candidate per evidenceRevision.

Before editing increment round, set usedInRound=true, record affected files and supporting evidence. Post-repair verification cannot edit again in that round. Another batch starts the next round only if budget remains. aiSelfRepairAllowed is false during post-repair-verification; returning a non-PASS report does not authorize another edit until the parent starts a permitted next round.

Classify:
- Supported generation defect: local repair from existing evidence.
- Missing/conflicting business fact: WAITING_USER immediately.
- Mechanical formatting/reference defect: checklist-based correction; not a content round, but at most one correction-and-check cycle per stage entry. Repeated failure becomes technical diagnosis.
- Tool/persistence problem: separate bounded recovery, not a content round.

Track blockers by requirement/behavior/location, not score. Same material blocker without improvement after two consecutive repair verifications stops automatic editing early. Exhausting the three-round budget also stops it.

At either boundary allow at most one read-only diagnosis per evidenceRevision when useful. It distinguishes missing evidence, wrong repair, rule conflict or invalid review finding. It does not edit, rescore, manufacture PASS or waive gates. Return the smallest needed decision: evidence, user correction, environment fix, an explicit finite extra repair budget, defer or stop. Extra rounds require the user's bounded authorization recorded in extraRoundsAuthorized.

Substantive new evidence/decisions may start a new evidenceRevision with its three-round budget for affected candidates only. Renaming or cosmetic edits never reset it. Keep compact cumulative counts/reset reasons in state and retain numbered score/review/diagnostic records as audit history; do not duplicate deliverable document bodies.

## Handoff, batch and internal boundaries

Reviewer reads actual sources/artifacts, not generator summaries. All selected candidates need current PASS and batch-level source coverage before Jira preparation. Record globalCoverage.status=pass only after reconciling original inScopeSRIds with coveredSRIds, explicitly supported exclusions and zero pendingSRIds. Bind that conclusion to current evidence revisions; do not silently remove an SR to make counts match. A blocked candidate does not prevent independent work on unrelated candidates. Removing it from the selected Jira batch requires user direction.

Handoff contains statePath, actual source/planning paths, identity, artifact paths and updateManifestPath where applicable. Receiving Skill validates state before resuming, not repeating calls unconditionally.

Internal update manifest contains baseline preservation and attachment selection, not score/audit fields. Scores/provenance belong in score record and state. Approval preview shows actual business changes and upload mapping, not diagnostics. Jira payload contains only supported approved fields and selected SPECs. Test Case is user-visible workspace content, never a standalone Jira attachment.

Final approval refers to the exact serialized helper payload and final attachment content checked by the read-back procedure above. Current state holds bindings/results; numbered internal approval records are retained, while historical Story snapshots are neither needed nor allowed.

## Waiting and completion

WAITING_USER/WAITING_TOOL returns one blocker and next action, then yields. Do not loop without new input. STOPPED requires explicit stop and is not PASS. Stage completion is distinct from confirmed Jira success. Whole-workflow success requires every intended operation confirmed.
