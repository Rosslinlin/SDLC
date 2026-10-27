# Jira Write and Recovery

Use with `handoff-and-content.md`, `fields-and-rendering.md`, and `workspace-profile.md`. This reference covers reviewed Story create/update only. Do not call Jira until the upstream validated handoff, score, independent review, preview, and preflight are complete.

## 1. Profile, identity, and discovery

- Read `workspace-profile.md` and use its applicable selected Jira source (`almType`), project, and Story issue-type profile. Treat profile values as already resolved: do not query metadata or rediscover the project/type during a normal run.
- For a requested update, use the upstream Jira baseline and attachment manifest. Do not call `getJiraInfos` or re-read the live attachment list as routine preparation. If the upstream baseline/manifest is missing or stale, stop and return to the upstream workflow.
- Use the real current `staffId`, the current conversation ID, and lowercase `almType`. Do not infer real identity values or fabricate a conversation ID.
- Complete the applicable SDLC defaults/settings confirmation before preparing the final preview. This is the workflow's routing/defaults confirmation, not an additional final-write approval prompt.
- If upstream authorizes a Story relationship, carry its reviewed Jira link fields at the export's top level only. Do not invoke the separate association flow, create another issue type, or invent relationship keys.

## 2. Create and update payloads

`exportJiraByDynamicFields` uses direct named arguments. Use only the live tool contract; relevant exposed arguments for this flow are:

| Argument | Use |
| --- | --- |
| `staffId`, `almType` | Real current staff ID and selected lowercase Jira source; required. |
| `projectKey`, `issueType`, `summary`, `description` | Create values; include only as applicable to the operation. `issueTypeId` is not an exposed export argument. |
| `issueIdOrKey` | Update target; use the reviewed existing ticket key. |
| `dynamicFieldsJson` | Once-serialized JSON string for metadata-confirmed fields, including Acceptance Criteria when creating/changing it. |
| `attachmentsJson` | Once-serialized authorized attachment plan; omit when there are no operations. |
| `issuesJson` | Once-serialized non-empty array for the supported integrated batch mode. |
| `conversationId` | Real current conversation ID when needed for Workspace attachment transport/binding. |
| `epicLink`, `parentLink` | Dedicated direct relationship fields. Create uses a confirmed applicable value; omit Epic for an explicit no-Epic decision. Update sends a relationship only when its change is explicitly authorised. Never put these fields in `dynamicFieldsJson`. |
| `linkedIssueKeys`, `linkType` | Only for a relationship explicitly authorized by upstream review; pass as top-level link fields, not in `dynamicFieldsJson`. |

Omit unused optional arguments. Never send `requestJson`, an undocumented `issues` argument, or `testDetailsJson` from this Story-only skill.

- A single create uses direct named `exportJiraByDynamicFields` arguments. A batch of new Stories uses one `issuesJson` argument containing a once-serialized non-empty JSON array string. Do not use an `issues` argument.
- Ordinary multiple updates are executed one ticket at a time in the exact preview order. Stop at the first failed, partial, or unknown result; do not proceed to later tickets. A mixed create/update batch may use one integrated `issuesJson` call only when the user explicitly requested a mixed batch. Otherwise, prepare separate operation previews. Keep each item's fields, target, attachment plan, and Story-to-score path with that item.
- Use only the actual fields supported by the live tool contract and the reviewed field assembly. `dynamicFieldsJson` is a once-serialized JSON string. This Story-only skill never uses `testDetailsJson`.
- The first Jira write includes the reviewed Story content and authorized attachment plan, but **never includes `markdownReviewRelativePath`**. Do not wrap arguments in `requestJson` or invent tool parameters.
- Before writing, preserve the exact direct payload and numbered attempt record. Re-read every value and upload file immediately before the call. If anything changed, replace the current `jira-preview.md`, retain the previous write preview in its audit record, allocate the next numbered attempt, and repeat preflight. Never overwrite completed attempt records.
- Write directly after exact preview and read-back. The user's export intent and upstream reviewed handoff govern this route; do not add another final approval prompt.
- The first response is authoritative for attachment outcomes. If an attachment is confirmed successful there, do not upload it again in any later step. A Jira issue success and an attachment failure/partial result remain distinct facts.

## 3. Separate Story-to-review binding

After an item's Jira issue write **and all requested attachments** are confirmed successful:

1. Read that item's actual returned `ticketKey`. For an update, it must equal the intended key. Missing, numeric-only, ambiguous, or mismatched keys block binding; never infer a key from a URL or item position.
2. For a batch create, match each returned resource to its own reviewed candidate. Bind each eligible successful item separately; never use a top-level Story path for another item.
3. Replace the single current `jira-preview.md` with the exact binding preview and preserve the completed Jira-write preview/result in its numbered audit record. Allocate and read back a separate numbered binding-attempt record.
4. Call `exportJiraByDynamicFields` a second time with **exactly five non-empty direct string arguments**:
   - `staffId`: real current staff ID;
   - `almType`: applicable lowercase source;
   - `issueIdOrKey`: this write's confirmed returned `ticketKey`;
   - `conversationId`: real current conversation ID;
   - `markdownReviewRelativePath`: the exact Workspace-relative `STORY.md` input path used for this candidate's passing score.

Match the Story path to the current score record, candidate state, and reviewed Story. It is never the score-record path. Do not add any content, attachment, dynamic-field, issue-batch, or link argument. This is the five-field SDLC binding call; do not substitute the separate four-argument independent-reviewer sync.

Binding succeeds only when the returned `markdownReview` explicitly confirms the association. Record issue-write/attachment results and binding results separately. The item is complete only when both stages succeed.

## 4. Failure, uncertainty, and recovery

- Do not automatically resend any external write, attachment upload, or binding call. For partial/unknown results, reconcile state through supported read-only means before deciding the next action.
- Never replay a confirmed successful Jira write or confirmed attachment to repair only a binding failure. Preserve the real ticket key/URL and exact failed binding attempt; keep the candidate pending.
- If an attachment result is partial or unknown, reconcile that attachment specifically. Do not replay the issue write or repeat an upload already confirmed successful. For replacement, do not delete the old attachment unless the new upload is confirmed successful.
- A numeric `status=400` is a business validation failure even when the MCP envelope says `isError=false`.
- Only after a documented field-validation failure proves no external write occurred may metadata be refreshed once; then rebuild and preview the payload. For any further write, wait for the user's bounded recovery decision, then create a new attempt and perform the full read-back/preflight. Never auto-retry.
- If the binding alone failed, recovery is binding-only and requires a supported recovery decision, a fresh exact five-field preview, and a new binding attempt record. Preserve the successful Jira write and attachment outcomes.
- Preserve distinct statuses for Jira issue write, each attachment operation, and Markdown-review binding. Report exact key/URL and sanitized failure detail without claiming overall success for partial, waiting, failed, or unknown states.

After a confirmed successful Jira issue write and all its required attachment operations, persist only allowed non-secret reusable Jira defaults in the canonical SDLC user-workspace profile, according to `workspace-profile.md` (selected source, project, Story type, and explicitly confirmed Epic/Parent defaults or no-Epic decision). A binding failure leaves that eligible profile usable and records binding pending. Do not persist new configuration from a failed/partial/unknown issue or attachment result. Keep numbered attempt history for the write and each binding attempt; update only the single current `jira-preview.md`, while retaining prior previews/payloads/results in their completed audit records. Give a concise per-Story report with operation, ticket key/URL, issue-write status, each attachment status, binding status, and any sanitized error. Never report a partial or unknown item as complete.

## 5. Final preview contents

The single current `jira-preview.md` must identify each candidate and include:

- Operation and destination: create or update, project, Story type, and for updates the intended Jira key.
- Gate/profile state: applicable validated handoff and review/score status; selected profile values; and the completed SDLC defaults/settings confirmation, including the exact project/type/Epic/Parent decisions used.
- Story field mapping: full Summary, full non-AC Description, full Acceptance Criteria value or the documented reason a verified matching update field stays unchanged; identify the metadata-confirmed AC field and its ID. State provenance for every Jira-visible value (reviewed Story, metadata/profile, user-authorized change, or unchanged baseline).
- Attachments: every add/replace/delete/none operation, exact file path and filename for uploads, confirmed existing attachment ID for removal/replacement, preflight result, and serialized `attachmentsJson` where active.
- One complete direct-arguments JSON payload for the export call. This JSON contains the full values above, so do not duplicate entire Story bodies in separate prose sections. For `issuesJson`, include the complete string and ensure every item contains its own required Story fields, attachment plan, target (if update), and other item-specific values.
- Exact preview/read-back statement: the values and attachment bytes re-read immediately before the call match this payload. Never attach the source markdown, score, review, preview, attempt log, or other workflow files to Jira; only authorized reviewed SPEC files are attachments.

For a mixed batch, show one integrated `issuesJson` only when the user explicitly requested the mixed operation. The displayed payload must be the exact direct tool arguments, with no wrapper and no `handoffType`. A binding call gets its own replacement current preview showing exactly its five fields and a separate numbered binding audit record.

When checking unfamiliar JSON nesting or the binding shape, consult [call examples](call-examples.md); use actual current values, never copy example IDs or paths into an export.
