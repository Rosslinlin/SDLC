# Preserve existing-ticket intent in the JSON route

Read [workflow](workflow-contract.md) and [assembly](jira-json-assembly.md). An explicit update never becomes a create; preserve each source+ticket identity and original obligations unless the user approves a specific change.

## Intake once per current baseline

1. Confirm each source before Jira reads. Use getJiraInfos for Description and Acceptance Criteria separately; use readJiraAttachments for the complete inventory, including zero attachments.
2. Preserve returned baseline/inventory and generated evidence copies under this candidate's historyRoot, using actual full Workspace-relative paths. Preserve original user-provided evidence at its own source path. Keep actual attachment IDs/names/relevance/skip reasons. Read every relevant readable attachment once; material skipped/unreadable evidence needs a supported handling decision.
3. Save baseline paths/statuses and actual target project/type/relationships/fields in exportContext. Reuse completed intake; do not repeat remote reads merely because another Skill/stage is invoked. Missing unsupported remote fields remain unavailable, not fabricated.
4. A usable Description with empty/unavailable separate AC can proceed after inspecting embedded criteria. Unusable Description or incomplete relevant inventory blocks that target. Known external changes require reconciliation before overwriting.

Before field discovery, read the real user-workspace profiles under [assembly](jira-json-assembly.md). Reuse same-user/source/project/type stable components even when their successful profile was recorded for create. Use actual baseline project/type facts as additional evidence. Switching to update is not a reason to repeat the three discovery queries. Profiles never replace reading this ticket's current Description/AC and relevant attachments, nor prove edit permission.

## Approved delta and transport support

Keep the Plan section of <historyRoot>/preparation.md and <historyRoot>/update-manifest.md: target/source, baseline pointers, original obligation → preserved/explicitly changed/clarification, approved delta and Summary decision, actual attachment IDs → output mappings, and material questions. Point the manifest to shared preparation facts instead of duplicating their whole tables. Reuse unchanged records on resume; a real decision updates the affected version-bound section, not another numbered plan file. Score/audit data stay in actual invocation records under historyRoot.

Preserve Summary, priority/assignee/labels/custom fields/status/links and Epic/Parent unless explicitly authorized within the reviewed delta. Settings/default adoption cannot broaden scope. Broad inherited sizing alone never authorizes new tickets or removal of requirements.

## Story and SPEC change together

An SDLC Story update includes automatic consistency of its local SPEC and tests; the user does not need to say update SPEC. For changes to Summary, Description, AC, scope, behavior or other business fields, update every affected Story projection, SPEC section, FR/SC/flow and test/embedded table from the approved delta, preserving original obligations. Reuse the current local SPEC when available; otherwise derive it from the actual baseline, relevant original SPECs and approved change. No original SPEC does not waive producing a current one. Read back all related SPECs before review/score. If a field change has no SPEC meaning or the content already agrees, record the check and keep that content unchanged rather than manufacturing edits.

Remote attachment intent is a separate decision with the following defaults for a business Story change:

| Actual inventory / explicit request | Internal action and plan |
| --- | --- |
| Related existing SPEC(s) | replace: automatically include the affected originals and their updated output mappings. User wording need not mention SPEC. Retain source-specific details and unrelated attachments. |
| No related SPEC | add: generate the first SPEC and include its actual Workspace path in attachments for upload to the existing ticket. This does not create another Story. |
| User explicitly asks to preserve remote attachments / no remote SPEC change | none: attachments=[], while the local SPEC and tests still follow the changed Story and are reviewed/scored. Report that the remote SPEC was intentionally left unchanged. |

Do not use none merely because the request mentions only the Story, Description or AC. Present the derived SPEC upload/replacement plan in the final current-content confirmation rather than asking whether ordinary local synchronization is wanted. If several originals could represent the same related SPEC, ask only which original to use; do not guess from duplicate filenames or merge distinct obligations. No new business fact may be invented to fill a SPEC.

For replace, preserve each selected original's actual ID, output path/name and obligations. Require the actual backend's supported replacement encoding and confirmed upload-before-delete behavior; the attachments path list alone does not prove deletion or replacement. Do not invent replace controls or use a separate legacy API. A missing replacement capability is a precise WAITING_TOOL with the local package and intended mapping preserved, never permission to silently leave a stale remote SPEC and announce full synchronization. Confirmed Story success with a failed/unknown SPEC operation stays partial; do not replay the successful Story write.

Use the [update JSON template](../templates/story-update-json-template.json): root issueIdOrKey, placed after jiraInstance/projectKey/issueType, is the exact confirmed existing ticket key (uppercase I in Id). This selector is user-confirmed backend support; a basic update no longer waits for an unknown target-property name. Verify it against the actual baseline and exportContext.target.ticketKey before review/scoring/export. A cited evidence ticket, key prefix or cached profile cannot select the update target. Creates omit this property; a missing/empty/mismatched update value blocks the update and never becomes a create.

Both modes use root acceptanceCriteria as the real AC field-ID string and Jira wiki content at fields[acceptanceCriteria], with no customFields wrapper, fields.reporter or root staffId. Keep the scoreable supported business view and preserve unchanged baseline values; include only approved changes and fields allowed by the actual update contract. Do not copy create defaults or treat create metadata as edit permission. Omission must preserve remote values where supported, not silently clear them.

The update target confirmation does not define attachment replacement controls. Explicit none uses attachments=[] and sends the synchronized local SPEC through confirmed specFilePath for scoring, with no extra score-only capability proof. The normal add/replace plan uses only actual authorized upload paths; replace additionally requires the supported original-ID operation above. An actual backend rejection is recorded as a precise tool gap. Do not invent operation/replace/deletion properties, override an explicit none, or fall back to an old field-based call. issueIdOrKey stays inside the scored JSON, never beside its path as an extra score/export argument.

## Generate, review and score

Assemble the supported update JSON during generation; automatically synchronize tests/SPEC business views and resolve the remote SPEC plan above before review or scoring. Read/review every current SPEC, synchronize Result/Notes, call scoreUserStory with JSON path + status + primary SPEC path, then freeze inputs. No Story score is written into SPEC. Hand the exact package, baseline and actual attachment mapping/support evidence to the path-only exporter for final content confirmation and submission. No routine intake repeats at export, and no automatic retry after an unknown remote result.
