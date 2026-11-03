# CEAIA JSON-first workflow — contract 7

Version 6.1.0-preview.6. This contract owns orchestration, state, invalidation and bounded repair. Content criteria belong to [Q1–Q7](quality-contract.md); JSON field mapping belongs to [assembly](jira-json-assembly.md); actual scoring belongs to [scoreUserStory](scoring-tool-contract.md). Task and ordinary chat use this same sequence. Runtime is text-only on GPT-5.6 Luna or GPT-6 Luna.

## Fixed sequence

| Phase | Action | Output / next |
| --- | --- | --- |
| intake | Classify targets, confirm each Jira source, read original evidence and update baseline/inventory. | Fixed selected list; each target ready or precisely pending. |
| assemble | Read real profiles and current baseline; reuse shared components across create/update and query only missing/inapplicable facts. Resolve required values and the automatic SPEC delivery plan. | Complete final story.json4jira and assembly snapshot, not a future export TODO. |
| generate | Derive or synchronize tests and every affected Markdown SPEC from the saved JSON and approved delta, without needing separate update-SPEC wording; perform one precheck. | Actual consistent JSON, tests and all SPECs. |
| review | Fresh read-only reviewer reads sources and current JSON/tests/SPECs. Parent saves its report. | PASS → score; supported defect → affected repair/review; missing fact → clarification. |
| score | Parent synchronizes every SPEC Result/Notes and verifies unchanged bodies, then calls scoreUserStory on the JSON with SPEC status/path and saves the complete response. | Current passing score; JSON and all scoring inputs frozen. No score write into SPEC and no display-only S2. |
| global-coverage | Reconcile all original obligations against every selected candidate, current evidence and approved exclusions. | Zero pending obligations before the integrated export stage. Preparation-only ends here. |
| preview | Present the actual scored JSON through the platform's .json4jira preview and obtain explicit content/upload confirmation. | Confirmation bound to the current JSON and SPEC revisions. |
| export | Call the path-only exporter on each confirmed file, unchanged. | Actual issue/attachment/database-link/queue results; asynchronous Jira score-sync status separate. |

Default jiraExportRequested=true without needing export wording. Entry is Jira-backed; never show a preparation-only/Jira-backed delivery-route selector or recommend dropping Jira because configuration is missing. Follow assembly's first-entry path: read available profile/context, ask only missing destination/identity facts, then query the remaining schema. Missing inputs retain Jira intent during a resumable wait. Only the user's explicit preparation-only/no-Jira, scope change or stop changes that intent; do not solicit that exception as a setup shortcut. Preparation-only skips submission, not the real configuration needed for final JSON/scoring. Stage-only invocations stop at the assigned boundary without cancelling later whole-workflow intent. Prepare one ready candidate through assemble/generate/review/score at a time. Blocked candidates remain selected; they do not prevent unrelated preparation. All selected scope and global coverage must pass before integrated export unless the user explicitly changes scope.

## Files and resources

| Value | New Story | Existing-ticket update |
| --- | --- | --- |
| outputRoot | outputs/ceaia/<slug> | outputs/ceaia/updates/<SOURCE>/<TICKET-KEY> |
| internalRoot | .ceaia-work/stories/<slug> | .ceaia-work/updates/<SOURCE>/<TICKET-KEY> |
| Canonical Story | <outputRoot>/story.json4jira; omit issueIdOrKey | Same; root issueIdOrKey identifies the confirmed existing target |
| Other deliverables | TEST_CASE.md and <name>-spec.md | Same, with distinct mapped SPECs when required |
| State | <internalRoot>/state.json | Same, plus update-manifest.md |
| Numbered evidence | plan-revision-<nnn>.md, jira-assembly-revision-<nnn>.md, review-attempt-<nnn>.md, story-quality-score-attempt-<nnn>.md, jira-export-attempt-<nnn>.md | Same |

.json4jira is valid JSON with a dedicated extension for the platform preview. It is not Markdown or a JSON-encoded string containing another request. Preserve the actual Workspace-relative JSON path byte-for-byte across scoring, preview and export; do not shorten, rename, normalize or rebuild it between calls. SPEC filenames remain safe lowercase kebab-case ending in -spec.md. Final name/path/reference checks belong to production, not a cosmetic content gate.

Keep latest deliverables at fixed paths; preserve original evidence and numbered completed records without overwriting. No historical deliverable copies. Full responses and payload/read-back evidence live in numbered records, not duplicated state bodies. There is no generated standalone STORY.md. The SPEC's Story section is a Markdown business projection from JSON, excluding routing/identity/schema control data.

Every Story edit checks SPEC/test consistency. Business changes automatically update affected local artifacts; pure routing/schema edits or already aligned content do not require artificial body changes. Local SPEC synchronization is independent of remote attachment action. Under [existing-update rules](existing-jira-update.md), a changed Story defaults related remote SPECs to replace or a first SPEC to add; only explicit remote-preservation intent uses none. Missing update-SPEC wording never waives synchronization. Resolve the delivery plan before review/score; unsupported replacement remains precise pending work, not silently omitted scope.

Batch state is .ceaia-work/batch-state.json: selectedCandidateIds in order, candidates entries with candidateId/statePath, and globalCoverage with batch-unique inScopeSRIds/coveredSRIds/pendingSRIds, supportedExclusions and candidateId/evidenceRevision bindings. Reconcile original sources before status=pass; a detached PASS label is insufficient. Send the actual batchStatePath with export handoff.

Use local resources. Another Skill's full procedure can be read through actual supported Skill-name/resource capability or supplied in full by the caller. Do not assume sibling filesystem access or invent reader tools. Generation/review carry identical local quality-standard copies.

## State and currentness

Initialize [candidate state](../templates/state-template.json) and [batch state](../templates/batch-state-template.json). contractVersion=7; unknown values are null/pending, never example success.

- Identity/progress: candidateId, mode, source, ticketKey, evidenceRevision, phase, status, nextAction and jiraExportRequested. Status is IN_PROGRESS/READY/WAITING_USER/WAITING_TOOL/STOPPED/COMPLETE. COMPLETE may describe the confirmed synchronous submission while jiraScoreSync remains pending; report that distinction explicitly.
- Paths: storyJsonPath, testCasePath, specPath, planningPath, scoreRecordPath and historyRoot. review.recordPath is the sole review pointer.
- Versions: artifactRevisions.storyJsonRevision/testCaseRevision and each specArtifacts entry's bodyRevision/fileRevision. The retained uploaded flag means intended upload only; actual remote success is in writeResults. attachmentReplacementMapping records approved existing IDs/output files, never guessed API encoding.
- Update attachmentAction is add for first SPEC delivery, replace for existing related SPECs, or none for explicit remote preservation; none never disables local SPEC/test synchronization. Record the actual related IDs/output mapping or explicit preservation decision in the existing update manifest/plan.
- Review: result, reviewAttempt, recordPath, evidenceRevision/storyJsonRevision/testCaseRevision, all SPEC-body bindings, contentReadBack and specHeaderSync. No score/readiness placeholders in the reviewer report.
- Score: actual status, ok/finalScore when returned, scoreAttempt, submitted storyJsonPath/revision, exact SPEC status/path/fileRevision and review attempt, responseReadBack. Preserve actual IDs/version/checksum only as returned, in the full record. No specScoreDisplay state.
- Assembly: exportContext retains actual target/baseline/current facts. exportContext.assembly has status (pending/verified/blocked), recordPath, profileKey and storyJsonRevision. The record contains exact schema/mappings/confirmed values/attachment intent needed for assembly and later successful profile persistence.
- Preview: exportPreparation.previewConfirmation has status (pending/confirmed/stale), storyJsonPath, storyJsonRevision, specFileRevisions entries {path,fileRevision}, and recordPath for actual preview/user confirmation evidence. Destination settings confirmation before scoring is not final content/upload confirmation.
- Repair/pending: one active and cumulative content counter, per-cause mechanical corrections, exact pending questions and bounded technical recovery. writeResults preserves each operation's actual issue, attachment, databaseLink, scoreSyncQueue and jiraScoreSync states/evidence separately. These are normalized local facts, not invented backend response keys.

Parent allocates the numbered review path and increments review.reviewAttempt; copy report.reviewResult, reviewedBindings and reviewedSpecArtifacts into the matching state fields. Validate identity, paths, complete SPEC list and observed revisions against current reads before accepting PASS. The report uses storyJsonChangeRequired to limit repairs.

Before any affected edit set readiness false and confirmation stale. Advance observed revisions only after a real write/read-back. Local counters are not hashes or remote locks. Reuse verified complete reads when no write intervened. Read actual files/latest records on resume before reusing an assessment.

| Change | Required return |
| --- | --- |
| Any JSON bytes, including routing/custom fields/attachments | Update affected projections/tests/SPECs and review if business/mapping meaning changed; revalidate/rescore current package, global coverage as affected, then obtain a fresh final confirmation. |
| Scored SPEC content, name/path or selected scoring attachments | Align affected references, independently recheck changed business content, synchronize header before a new score, then confirm again. Never assume metadata-only caching. |
| Test Case only | Repair embedded SPEC table; affected independent review and new package scoring/confirmation because SPEC changed. |
| Review Result/Notes | Complete header changes before scoring. A later header edit changes the scored file and requires a current assessment. |
| Preview edit | Return to generation, align changed JSON/SPEC/tests, re-review affected content and score before a fresh confirmation. |
| Known external ticket change | Reconcile baseline and affected preservation before any update. |

## Bounded repair and persistence

Default two content-repair batches per candidate/evidenceRevision, shared across review and score findings. Fix all known supported defects in one batch, increment round/cumulativeRounds once and independently verify affected content. Stop early when the same material blocker remains without progress. Do not change scope or cosmetically mutate content to seek a better score.

Pure mechanical corrections have at most one correction/check per real cause, tracked by causeId/artifactPath/attempts/outcome in technicalRecovery.mechanicalCorrections. They do not consume a content batch, but changed scoring inputs still invalidate score/confirmation. Backend JSON validation errors identify exact corrections; canonical inputs still failing are a technical issue. Missing business facts wait immediately under [clarification](user-clarification-and-resume.md). New substantive evidence resets only the affected active counter; finite extra rounds require actual recorded user direction. Resume never resets consumed state.

The local repair allowance never authorizes replaying an external create/update/upload. Preserve complete response records. A failed local save retries saving the received response, not the tool. Unknown scoring or creation outcomes require supported reconciliation rather than blind replay. The new route has no separate B call; never send old five-field arguments to recover backend linkage or queue failures.

## Handoff and completion

After current independent PASS, verified pre-score SPEC headers, real score 76–100/parser validity, exact scored input bindings and global coverage, parent sets jiraReady (and updateReady only for a fully supported update). Handoff includes statePath, batchStatePath, original source/assembly/review/score records, paths.storyJsonPath, all SPECs and update evidence where applicable. The exporter owns final content confirmation and a one-argument call; it neither queries fields nor renders/converts business content.

Backend linkage and queueing replace the old Agent B call. A ticket key alone does not prove DB write-back. Record issue, attachments, databaseLink and scoreSyncQueue outcomes from actual evidence; absent evidence remains unknown/pending. Confirmed synchronous creation/linkage/enqueue can be reported as submitted with Jira score synchronization pending. Do not claim the Jira score field has updated without actual evidence, invent a polling tool or loop waiting for an asynchronous job.

## Migration from v6

The 2026-11-03 confirmed fields/AC-ID-map format also supersedes preview.1's flat JSON. Before an unsubmitted package continues, generation rewrites it using actual metadata, aligns its dependent SPEC/tests, verifies affected review and obtains a current score and content confirmation. A schema-only edit still changes scored bytes. Preserve old records and every known/unknown remote outcome; an already submitted or uncertain operation is reconciled, never recreated to migrate its format. State contract remains 7 because the control-state shape is unchanged.

Latest developer/user confirmation further moves the AC ID to root acceptanceCriteria, removes customFields and fields.reporter, and confirms root issueIdOrKey for updates only. Use the matching create/update template before scoring. For an update, bind that exact nonempty key to the actual baseline/source/target; never infer it from an evidence ticket or profile, and never remove it to retry as create. The selector is not an extra score/export tool argument. Separate unsupported attachment operations remain specific capability gaps, not a reason to label the confirmed basic update target unknown. Migration changes scoring inputs and needs a current score/confirmation; preserve all remote-outcome evidence.

Preserve old state, original inputs, score records and every known/unknown remote outcome as migration evidence before converting local metadata. A Markdown score or old five-field association cannot authorize a new JSON upload. An unexported candidate must assemble the actual JSON, regenerate dependent views, independently review and score it through the new interface. Remove old storyPath/storyRevision/specScoreDisplay only after their evidence is preserved; new control uses storyJsonPath/storyJsonRevision. For an earlier fields-based JSON, follow [format migration](jira-text-format.md): remove root staffId and encode authored Jira rich text before rescoring/reconfirmation. Recheck affected semantic consistency; presentation-only changes do not justify a whole business rewrite. Never migrate by replaying a known/unknown remote write.

For an already-created ticket with an old pending B or unknown attachment result, do not automatically rescore, recreate or send old B arguments to the path-only tool. Keep that operation pending until a genuinely supported backend recovery/association method is established. Migration never converts unknown into not-started. Main's v6 bundle remains separate from this feature branch; no automatic legacy fallback bypasses the new backend gate.
