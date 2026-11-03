# Preview, path-only submission and backend recovery

Use a [current validated handoff](handoff-and-content.md), verified assembly and authorized [attachments](attachments.md). Tool: java-base-mcp.exportJiraByDynamicFields. The user-confirmed interface has exactly one argument, story_json_workspace_path. There is no separate five-field B call. See [examples](call-examples.md).

## 1. Preview the actual scored package and obtain confirmation

1. Read the exact saved .json4jira and every scored/intended-upload SPEC. Verify review/score/header/currentness and selected global coverage under the handoff. Do not serialize a replacement request or query metadata here.
2. Present that actual .json4jira using the platform's native frontend preview. Show create/update operation, source/project/Story type, the update's exact root issueIdOrKey, full business content, synchronized local SPECs and exact planned SPEC add/replace or explicitly requested none. Story-only wording does not exclude SPEC synchronization. Create files omit issueIdOrKey. The platform may render HTML; this is not an instruction to generate another HTML upload file or invent a preview tool. If native preview/current-content confirmation is unavailable, retain the prepared files and return the precise WAITING_TOOL.
3. Obtain an explicit user content/upload confirmation for this current package through the supported platform confirmation capability. Initial SDLC intent, earlier destination settings, a previous revision's confirmation or a model-written label does not count. Reuse an existing actual confirmation only when it binds these same current files and revisions. The user may confirm several clearly identified current packages together.
4. Save the current jira-preview.md with exact one-argument invocation, JSON path/revision, SPEC paths/file revisions, target/attachment summary and actual preview/confirmation evidence. Record exportPreparation.previewConfirmation as status=confirmed with storyJsonPath, storyJsonRevision, specFileRevisions entries {path,fileRevision}, and recordPath. Do not duplicate/re-author the business Story to create the preview.

An edit in the frontend changes the submission input. Return it to generation, align affected tests/SPEC/projection, independently verify changed business content, synchronize headers and obtain a new score and current-content confirmation. Destination/attachment/schema-only JSON edits still invalidate score and confirmation. Never modify, rename, normalize or reserialize JSON/SPEC between passing score and submission.

## 2. Save the attempt and submit the unchanged path

Allocate the next numbered jira-export-attempt-<nnn>.md under the candidate's historyRoot. Bind current scoreRecordPath/scoreAttempt, actual score input, assembly/review/global-coverage records, exact preview/confirmation and read-back observations. Preserve the exact one-argument request and complete actual response; keep completed attempts immutable.

Immediately before submission verify the actual files still match those bindings and the confirmation. An update's root issueIdOrKey must be nonempty and equal the confirmed saved target; never remove it to pass create validation or redirect to another issue. Copy story_json_workspace_path unchanged from the passing score input and paths.storyJsonPath. Do not pass issueIdOrKey or other direct content/routing/identity/attachment fields, SPEC parameters, requestJson/issuesJson or internal handoff/state metadata as tool arguments. Platform identity/session headers are not caller-generated arguments.

Call the exporter once for this candidate. The backend must validate the stored score/current checksum/threshold, execute the supported issue/attachment operation, link the score record to the Jira ticket in the database and enqueue asynchronous Jira score synchronization. Agent-side PASS or a saved local report does not replace those backend effects. Do not issue B to perform them again.

Multiple candidates use one path per sequential call, in confirmed selection order. Each has its own score and current-content confirmation. Stop further writes at the first failed/partial/unknown required synchronous outcome, preserving every remaining selected candidate. Confirmed queue acceptance with asynchronous sync still pending is not itself a submission failure.

## 3. Read the complete response and record each effect

Preserve the full raw result before interpreting it, including unknown fields/errors and returned service IDs. For an already decoded object serialize the whole object and label that mode; do not claim byte-verbatim text. Inspect nested business failures even when an MCP envelope has isError=false; status=400 or a checksum/threshold rejection is not success.

Use the actual live response schema/guarantees. These are local observation categories, not invented backend field names:

| Outcome | Evidence required |
| --- | --- |
| issue | Actual supported create/update success and unambiguous returned ticket identity/URL when exposed. An update's returned key must match its reviewed target. Do not infer keys from positions or ambiguous numbers. |
| attachments | Each requested upload/removal result, or a documented atomic success guarantee covering the exact planned operations. Zero uploads is N/A only when the authorized plan is empty. |
| databaseLink | Explicit association evidence or a documented response guarantee proving score-record-to-ticket linkage. A ticket key alone does not prove it. |
| scoreSyncQueue | Actual accepted/enqueued result or a documented guarantee. Keep the real queue/score record ID when returned; do not manufacture one. |
| jiraScoreSync | Actual completed/failed/pending evidence. Queue PENDING/accepted proves scheduling, not a Jira UI score-field update. Without completion evidence report pending/unknown. |

An authoritative response may prove several effects when its documented semantics guarantee them; record that basis. A generic success with no such guarantee does not prove every effect. Missing optional IDs alone are not failure, but required effects without evidence remain unknown/pending.

Save each fact/evidence separately in state.writeResults and the attempt record. Report confirmed keys/URLs, local SPEC synchronization, each required Jira SPEC upload/replacement outcome, linkage, queue, async state and exact pending work. Synchronous submission is complete only with confirmed issue, required attachments, linkage and enqueue; it may finish with score synchronization pending. A current local SPEC does not prove the remote SPEC changed, and a pending required SPEC operation prevents claiming full Story/SPEC synchronization. Explicit none reports remote attachments intentionally unchanged. Never state the Jira UI score is synchronized merely because the call returned or a queue row exists.

## 4. Partial, failed or unknown outcomes

- A failed local response save retries that save, not the external operation. Preserve a received response even if later interpretation fails.
- Known issue success with attachment, linkage or queue failure keeps the ticket and all confirmed uploads. Do not recreate/update it again or resend confirmed files to repair another effect. There is no automatic legacy B, generic-helper fallback or standalone upload/delete bypass.
- Unknown timeout/connection loss is unknown, not not-started. Use only actually available supported read-only reconciliation. A Jira read may establish ticket/attachment state; it does not establish database/queue state. Without an unambiguous outcome or supported recovery interface, return WAITING_TOOL with exact pending effects; a vague continue does not establish idempotency.
- A validation/checksum/threshold rejection requires actual evidence of whether any effect occurred. Return stale/invalid inputs upstream for bounded correction, affected review/scoring and fresh confirmation. Do not drop fields or alter a scored file at the submission step. Only documented failure proving no external effect permits a new attempt after the required correction/confirmation; no blind automatic retry.
- Replacement deletion requires confirmed new upload under the supported backend operation. Unknown upload retains the old attachment; never target old files by name or replay a confirmed upload.
- Asynchronous queue processing is backend-owned. Do not invent polling/recovery tools, loop waiting for completion or re-export to nudge a job. Report actual pending/failed sync and a supported next action if one exists.

Local records/counters are audit evidence, not remote locks or database proof. Missing schema/result/recovery capability is a precise tool gap, not permission to fabricate success. Preserve all known/unknown outcomes on resume. After independently eligible issue/required-attachment success, save only the upstream profile snapshot under [profile persistence](workspace-profile.md); its save failure is separate from the remote operation.
