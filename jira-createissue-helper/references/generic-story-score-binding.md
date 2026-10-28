# Generic Story score binding

Use only for non-SDLC generic Story create/update/batch and recovery of their missing Workspace-score bindings. Other issue types, Test Cases, associations and SDLC handoffs retain their own flows. Jira-mode scores use the reviewer's four-field route; they have no Workspace scoring input to bind.

## Identify the current assessment

Before preview, establish whether the exported Story has a completed Workspace scorer call: `ok === true`, finite numeric `finalScore`, and its actual `relativePath`. Use actual conversation calls or a known/supplied result record; a local result file is not mandatory. Input content must still represent the exported Story apart from Jira transport formatting. The score-result file is never the binding path. Scores including 0 qualify; no SDLC threshold applies.

A genuinely unscored revision exports normally without a binding call, including a new revision whose only assessment belongs to older content. Historical scoring alone adds no new scoring gate. When this export carries a completed current Workspace assessment or a pending binding for this scored revision, missing/ambiguous current path, result or mapping must be resolved before sending the scored export; do not silently reclassify that current assessment as unscored. If its write already succeeded, retain it and keep binding pending. Do not bind an older assessment to changed content. Resolve a mismatch with the caller; this helper never scores during export.

Keep a compact per-assessment record in the existing score result/preview/context: assessment version or actual invocation reference when available, input path, Ticket/source, content-write result and binding status (pending/success/failed/unknown). Each new Workspace assessment intended for export starts a new pending binding, including a new invocation with cached content score. Earlier success for the same Ticket/path does not complete the new assessment. Do not add a new state file or invent invocation IDs.

## Prepare the existing preview

Include each eligible item's current assessment, input path and intended five-field binding in `jira-preview.md` and the normal generic confirmation. Keep `markdownReviewRelativePath` out of the first content-write call. The existing confirmation covers the write and its binding; add no second confirmation popup. Preserve normal single/batch write payloads and per-item mappings.

## Complete the post-write binding

After the current item's confirmed successful Jira content create/update, match its actual returned Ticket Key to that assessment. For a batch, preserve the one issue-write call and match each returned resource to its previewed item; process every successful eligible Story separately. Missing/ambiguous keys or uncertain/failed content writes do not qualify.

For each current pending eligible assessment, call `exportJiraByDynamicFields` once with exactly five nonempty direct strings: `staffId`, `almType`, `issueIdOrKey` (that returned Key), `conversationId` (the verified scoring Workspace identity), and `markdownReviewRelativePath` (this assessment's actual `relativePath`). Do not send content, attachments, links or batch fields. Confirm real IDs/path through existing evidence or focused input questions; never substitute a score-result/SPEC path.

Do not finish a scored Story's flow at the content-write response. Record content-write and binding outcomes separately; only a successful returned `markdownReview` confirms association. If binding remains missing, failed or unknown, preserve the written Ticket and the assessment's pending/problem status. Do not claim association complete from a Ticket Key, generic success, or an older assessment's binding. Association confirmation does not establish final Jira score-field writes.

## Binding-only recovery

When the user asks to finish writeback, select the assessment referred to by the current request/context. Verified current Workspace scoring plus a successful matching content write means five-field recovery, even though the Ticket already exists. If that binding was never invoked, record its exact arguments in the existing record/preview and perform only the five-field call. The user's explicit recovery request authorizes this missing association; do not request another sync confirmation.

Do not replay successful creation, content updates or attachment operations, or rescore to repair a missing binding. If content still needs writing, resume the normal helper update flow first. If this assessment's association already succeeded, retain it instead of repeating it. A failed, partial or uncertain prior binding needs reconciliation and a supported user-directed recovery; no automatic retries.

Before closing a batch, account for each item as bound, genuinely unscored/already Jira-associated, write-failed or binding-pending with its reason. Guard repeated result handling by this assessment's recorded outcome so the single/batch hooks never duplicate a confirmed binding.
