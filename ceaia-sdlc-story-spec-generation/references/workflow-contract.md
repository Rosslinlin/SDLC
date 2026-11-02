# CEAIA workflow contract v6

Owns orchestration, local state, invalidation and repair. Task and ordinary-chat entry use this one sequence. Content criteria belong to [quality-contract](quality-contract.md); external tool inputs and persistence belong to [scoring-tool-contract](scoring-tool-contract.md) and the exporter. Execution is text-only on GPT-5.6 Luna or GPT-6 Luna; no scripts, invented tools or unconfirmed Task model parameters.

## Fixed sequence

| Phase | Action | Output / next |
| --- | --- | --- |
| intake | Classify new/update, confirm each Jira source, read supplied evidence; complete update baseline and attachment inventory. | Fixed selected list, readable evidence and per-candidate blockers. Intake stage can close with exact pending targets recorded. |
| generate | For each ready candidate read Q1–Q7; map sources, write Story → tests → SPEC, then one production precheck. | Current files; `independent-review`. |
| review | Fresh read-only `ceaia-spec-review` reads sources and current artifacts. Parent saves its report. | PASS → score; supported defect → affected repair and re-review; missing fact → clarification. |
| score | Synchronize every SPEC Result/Notes and verify unchanged body; invoke real Workspace scoring and save the complete response. Follow score display and final metadata procedure. | Current passing score and final SPEC metadata, or exact score/technical blocker. |
| global-coverage | Reconcile original source obligations with all selected candidate outputs and approved exclusions. | Zero pending obligations before export. Preparation-only ends here with artifacts, not Jira success. |
| export | Exporter confirms settings, renders fields, previews/preflights, performs Jira A. | Preserve actual issue and attachment outcomes. |
| bind | After each A item and all required attachments succeed, exporter performs separate five-field B and verifies `markdownReview`. | Confirmed bindings, or binding-pending recovery; never repeat successful A to repair B. |

Default `jiraExportRequested=true` even without export wording. Only explicit preparation-only/no-Jira, scope change or stop changes that intent. Defer destination/project/type/Epic questions until export. Explicit stage-only calls stop at their boundary without launching later stages or cancelling whole-workflow export intent. A direct full-workflow call follows the same table; do not maintain a second sequence in a Skill.

Prepare candidates serially through generate/review/score so a blocked candidate does not stop unrelated preparation. Keep every selected candidate pending or ready; no silent removal. All selected candidates and global coverage must pass before the integrated Jira stage unless the user explicitly changes the selected scope.

## Paths and resources

| Artifact | New Story | Existing-ticket update |
| --- | --- | --- |
| outputRoot | `outputs/ceaia/<slug>` | `outputs/ceaia/updates/<SOURCE>/<TICKET-KEY>` |
| internalRoot | `.ceaia-work/stories/<slug>` | `.ceaia-work/updates/<SOURCE>/<TICKET-KEY>` |
| Current deliverables | `STORY.md`, `TEST_CASE.md`, `<name>-spec.md` | Same; multiple distinct replacement SPECs when needed |
| Current state | `<internalRoot>/state.json` | Same, plus `update-manifest.md` |
| Numbered evidence | `plan-revision-<nnn>.md`, `review-attempt-<nnn>.md`, `story-quality-score-attempt-<nnn>.md`, `jira-export-attempt-<nnn>.md` | Same |

Artifact paths are real Workspace-relative paths, not Skill resources, absolute paths, URLs or traversal. Identity for updates is source + ticketKey; new slugs are unique lowercase kebab-case. Produce safe `<name>-spec.md` basenames matching `^[a-z0-9]+(?:-[a-z0-9]+)*-spec\.md$`. Decide names before writing and keep actual paths, SPEC File Name, Story upload names, Test Case link and attachment mappings consistent. Naming is a production/transport check, not an independent business-quality criterion.

Keep latest deliverables at fixed paths and numbered completed evidence immutable. Preserve original user/Jira sources separately. State contains pointers and control facts, not copies of documents or raw responses. Do not create historical deliverable copies. `jira-preview.md` is the single current preview; each numbered export attempt retains its exact payload and result.

The batch control file is `.ceaia-work/batch-state.json`. Each candidates entry identifies candidateId and its actual statePath; selectedCandidateIds preserves the complete scope/order. globalCoverage records batch-unique inScopeSRIds, coveredSRIds, pendingSRIds, approved supportedExclusions and evidenceRevisionBindings entries containing candidateId/evidenceRevision for every selected candidate. Set status=pass only after reconciling the originals; preserve a waiting candidate rather than removing it to make counts match.

Use local resources first. For another Skill's procedure, use actual platform Skill-name + resource reading when supported; otherwise the caller supplies the full relevant procedure/template text. Do not assume sibling filesystem access, invent a resource tool, or continue from a summary when the actual standard is unavailable. Generation and review carry identical local Q1–Q7 copies; no fourth Skill is required.

## State and currentness

Initialize [candidate state](../templates/state-template.json) and [batch state](../templates/batch-state-template.json). `contractVersion=6`. Use actual booleans/numbers and null for unknowns. Detailed findings and complete tool JSON remain in numbered records.

- Identity / progress: candidateId, mode, source, ticketKey, evidenceRevision, phase, status, nextAction, jiraExportRequested. Status: IN_PROGRESS / READY / WAITING_USER / WAITING_TOOL / STOPPED / COMPLETE.
- Paths / artifacts: current evidence/planning/review/score pointers, artifactRevisions and all specArtifacts; each SPEC has path, fileName, uploaded, bodyRevision and fileRevision. **uploaded means planned upload**, not confirmed remote success; actual outcomes live only in writeResults. Include attachmentAction and explicit replacement mapping.
- Review: result, reviewAttempt, recordPath, observed Story/Test Case revisions, reviewed SPEC-body bindings, contentReadBack and specHeaderSync. No scoreGate compatibility object, title-pass flag or reviewer-controlled readiness.
- Score: actual status, call count, current Story/primary SPEC/review binding and exact returned ok/finalScore; full inputs/response are in scoreRecordPath. specScoreDisplay tracks derived display and final metadata completion under its procedure. Do not claim a local revision was returned/stored by the remote service.
- Control: one repair counter, pending userRecovery, technicalRecovery, parent-owned jiraReady/updateReady, exportContext baseline/profile/settings pointers, exportPreparation and writeResults. Batch state stores selected IDs/state paths and global coverage, not duplicated write outcomes.

When saving a new report, parent allocates the numbered review path and increments review.reviewAttempt. Copy report.reviewResult to review.result, report.reviewedBindings to the corresponding evidence/Story/Test Case revisions and contentReadBack, and report.reviewedSpecArtifacts to review.specArtifacts. review.recordPath is the single review pointer. Before accepting PASS, compare the report's identity, actual paths, complete SPEC list and body bindings with current files; advisories may remain, blockers may not. A v6 report uses qualityContractVersion=6.0.0 and contains no scoring/readiness/repair placeholders.

Set readiness false before affected edits. After successful write/read-back advance Story/Test Case revision, or SPEC bodyRevision + fileRevision for body edits; header-only edits advance fileRevision. Revisions track observed edits, not hashes. Read actual files when establishing/reusing PASS; matching counters alone are insufficient. Reuse a complete read when no intervening write occurred. On resume read current state/files/latest applicable records; invalidate only affected uncertain bindings.

| Change | Return point |
| --- | --- |
| Story content | Align tests/SPEC, affected independent review, synchronize SPEC, final score and display/metadata; invalidate Jira preview/readiness. |
| Test Case or SPEC body | Repair dependent embedding/references, affected independent review, synchronize SPEC and refresh final score metadata using normal cache when eligible. |
| Filename/path/reference | Production correction and affected link/mapping verification; review only affected content consistency. If Story bytes change, its score is stale. Refresh SPEC metadata/preview as needed. |
| Review header or derived score display only | Header read-back and required metadata procedure; unchanged content review remains valid. |
| Source/scope/attachment selection | Replan affected obligations, review preservation/content, refresh eligible score metadata and preview. |
| Routing-only fields | Export settings/preview; content review only if business scope or mapping changes. |
| Known external ticket change | Reconcile baseline and affected preservation before overwriting. |

## Bounded local repair and waiting

Default **two content-repair batches per candidate/evidenceRevision**, shared across review and final-score findings. One batch fixes all currently known supported defects, then independently verifies affected content before eligible scoring. Increment repair.round/cumulativeRounds once before edits. Stop early if the same material blocker remains without progress; never reword unchanged content merely to seek a higher score.

Pure mechanical corrections preserving business meaning (canonical heading, filename/reference or table syntax) have at most one correction/check per cause. Record causeId, artifactPath, attempts and outcome in technicalRecovery.mechanicalCorrections; identify the cause by the actual item/problem, not a new generated revision or label. They do not consume a content batch. This does not waive downstream invalidation: changed Story bytes need a current score; changed business meaning needs content repair and review. Repeated recognition failure with canonical content is a service issue, not permission to invent requirements.

Missing/conflicting business facts wait immediately. After the content limit or no-progress stop, use [clarification and resume](user-clarification-and-resume.md) for a precise fact or a finite extra-repair decision. A new session or ordinary retry never resets counters. Actual substantive new evidence/decision starts a new evidenceRevision for affected candidates and resets active round only; preserve cumulative count and all records. Explicit finite extensions are recorded before editing.

Header synchronization, score persistence and external writes keep their own bounded recovery rules. **The local two-batch allowance never authorizes automatic Jira/attachment/binding retries.** Unknown external outcomes require reconciliation; a successful operation is never replayed to fix another step.

## Ready handoff and completion

After all selected current content reviews PASS, all SPEC headers/display and final scoring metadata are verified, every score passes 76–100/parser checks and global coverage has no pending obligations, parent sets jiraReady=true and updateReady=true for valid updates. Send statePath, batchStatePath, identity, actual artifact/source/planning paths, review/score records, exportContext and update manifest to the exporter. batchStatePath resolves to the actual canonical batch file above, not a copied coverage label. Exporter checks selected scope, coverage evidence revisions and current bindings against it, without repeating business review or routine Jira intake.

Bind each current export attempt to its final scoreRecordPath/scoreAttempt and reviewed artifacts. A new assessment intended for a new content operation has a new pending binding even if Story path and Ticket Key are unchanged. Preserve old successful A/B outcomes as history; they do not complete a different assessment/operation. For the unchanged same operation, reuse confirmed outcomes and resume only its missing B without rescoring or replaying A.

Final attachment/request read-back and exact preview are exporter-owned. Any intervening content change invalidates affected readiness. These checks do not provide a remote lock or cryptographic immutability. Report actual saved SPEC names and separate Jira/attachment/binding/profile outcomes. WAITING states yield with one next action; STOPPED needs explicit user stop and is not PASS. Whole-workflow success requires all intended operations confirmed.

## Resume older state safely

Read existing v5 state and its referenced records before a one-time local v6 migration. Preserve source/baseline paths, all artifact revisions, final metadata bindings, counters, pending questions, exact payloads and **every known/unknown remote outcome**. Archive the original state as migration evidence; do not repeat calls to migrate.

Use nested review.reviewAttempt (or its recorded report) as the sole review counter and review.recordPath as the pointer; migrate paths.reviewPath there before removing that alias. Remove top-level reviewAttempt/aiSelfRepairCount aliases and obsolete title/scoreGate/diagnostic/stagnant fields. Keep consumed repair.round/cumulativeRounds; v6 default limit is 2, with any explicit scoped extension retained. Convert batch writeResults into pointers to the candidate's authoritative results only after preserving their complete evidence. Unclear associations stay WAITING_TOOL until reconciled; never translate unknown into not-started. A current old content PASS is reusable only when its actual evidence establishes the applicable Q1–Q7 checks; otherwise re-review affected content without recreating obsolete fields. A v5 score is reusable only with current actual inputs, parser validity and completed required SPEC metadata. If A/attachments already succeeded and only B is pending, preserve the existing scored-content association and recover B only; migration itself never authorizes re-scoring or replaying A.
