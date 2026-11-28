# Preserve existing-ticket intent in the JSON route

Read [workflow](workflow-contract.md) and [assembly](jira-json-assembly.md). An explicit update never becomes a create; preserve each source+ticket identity and original obligations unless the user approves a specific change.

## Intake once per current baseline

1. Confirm each source before Jira reads. Use getJiraInfos for Description and Acceptance Criteria separately; use readJiraAttachments for the complete inventory, including zero attachments.
2. Preserve returned baseline/inventory and generated evidence copies under this candidate's historyRoot, using actual full Workspace-relative paths. Preserve original user-provided evidence at its own source path. Keep actual attachment IDs/names/relevance/skip reasons. Read every relevant readable attachment once; material skipped/unreadable evidence needs a supported handling decision.
3. Save baseline paths/statuses and actual target project/type/relationships/fields in exportContext. Reuse completed intake; do not repeat remote reads merely because another Skill/stage is invoked. Missing unsupported remote fields remain unavailable, not fabricated.
4. A usable Description with empty/unavailable separate AC can proceed after inspecting embedded criteria. Unusable Description or incomplete relevant inventory blocks that target. Known external changes require reconciliation before overwriting.

Before field discovery, read the real persistent user-Workspace profiles under [assembly](jira-json-assembly.md). Show the current-run settings decision even if only a create-capability profile exists; reuse same-user/source/project/type stable components while keeping create-only defaults out of the update values. Use actual baseline project/type facts as additional evidence. Switching to update is not a reason to repeat the three discovery queries. Profiles never replace reading this ticket's current Description/AC and relevant attachments, nor prove edit permission.

## Approved delta and transport support

Keep the Plan section of <historyRoot>/preparation.md and <historyRoot>/update-manifest.md: target/source, baseline pointers, original obligation → preserved/explicitly changed/clarification, approved delta and Summary decision, approved SPEC upload paths and filenames, and material questions. Point the manifest to shared preparation facts instead of duplicating their whole tables. Reuse unchanged records on resume; a real decision updates the affected version-bound section, not another numbered plan file. Score/audit data stay in actual invocation records under historyRoot.

Preserve Summary, priority/assignee/labels/custom fields/status/links and Epic/Parent unless explicitly authorized within the reviewed delta. Settings/default adoption cannot broaden scope. Broad inherited sizing alone never authorizes new tickets or removal of requirements.

## Identify relevant SPEC evidence

Bind each relevant original to the confirmed source+ticket and its actual inventory ID, name and imported path when available. Read its complete content to establish its specification purpose and affected scope. Filename, extension, MIME, order or apparent recency alone cannot establish relevance. Record related SPECs and their obligations in the existing update manifest, along with other evidence or unresolved attachments. All originals remain attached. Resolve an unread or ambiguous original when its content is needed to preserve business obligations. A missing old attachment ID alone does not block adding a reviewed SPEC if its material baseline is established.

## Story and SPEC change together

An SDLC Story update includes automatic consistency of its local SPEC and tests; the user does not need to say update SPEC. For changes to Summary, Description, AC, scope, behavior or other business fields, update every affected Story projection, SPEC section, FR/SC/flow and test/embedded table from the approved delta, preserving original obligations. Reuse the current local SPEC when available; otherwise derive it from the actual baseline, relevant original SPECs and approved change. No original SPEC does not waive producing a current one. Read back all related SPECs before review/score. If a field change has no SPEC meaning or the content already agrees, record the check and keep that content unchanged rather than manufacturing edits.

Remote attachment intent is separate from local synchronization:

| Actual request | Internal action and plan |
| --- | --- |
| Ordinary business Story update | add: upload every affected, reviewed current SPEC as a new attachment via `attachmentsJson.add`, including when related SPECs already exist on Jira. Keep all existing attachments. |
| User explicitly requests no remote attachment changes | none: use `attachments=[]` and omit `attachmentsJson`. Keep the local SPECs and tests synchronized, reviewed and scored. |

Present the exact new attachments and retained originals in final content confirmation. Retain distinct output SPECs when their source obligations differ. If the user explicitly requests attachment replacement or deletion, explain that SDLC currently supports adding attachments and ask whether to add the new files or leave attachments unchanged.

Use [confirmed attachment operations](jira-json-assembly.md#4-confirmed-attachment-operations): every root `attachmentsJson.add` entry contains the reviewed file's actual `relativePath` and `fileName`. The update template defaults to add. Existing attachment IDs are baseline evidence and never enter the upload entries.

Use the [update JSON template](../templates/story-update-json-template.json): root issueIdOrKey, placed after jiraInstance/projectKey/issueType, is the exact confirmed existing ticket key (uppercase I in Id). This selector is user-confirmed backend support; a basic update no longer waits for an unknown target-property name. Verify it against the actual baseline and exportContext.target.ticketKey before review/scoring/export. A cited evidence ticket, key prefix or saved profile cannot select the update target. Creates omit this property; a missing/empty/mismatched update value blocks the update and never becomes a create.

Both modes use root acceptanceCriteria as the real AC field-ID string and two distinct root descPath/acPath files for Description and AC Jira wiki content, with no inline fields.description or fields[acceptanceCriteria], customFields wrapper or root staffId. Include fields.reporter only when actual update evidence requires it or the user explicitly requests a supported change, with the native value resolved; omission preserves the remote Reporter. Keep the scoreable supported business view and preserve unchanged baseline values; include only approved changes and fields allowed by the actual update contract. Do not copy create defaults or treat create metadata as edit permission. Omission must preserve remote values where supported, not silently clear them.

Assemble the approved upload plan before scoring. Use root `attachmentsJson.add` and omit the legacy attachment path array. Explicit none uses `attachments=[]` and omits `attachmentsJson`, while the synchronized local SPEC still supplies `specFilePath`. Resolve material baseline content gaps and target ambiguity. Keep any actual backend rejection and the confirmed update target. `issueIdOrKey` and `attachmentsJson` stay in the scored JSON and are not separate tool arguments.

## Generate, review and score

Assemble the confirmed update JSON, its two referenced Jira wiki files and exact attachment operations during generation; automatically synchronize tests/SPEC business views before review or scoring. Independently review every current SPEC using the Story package and tests as alignment references, synchronize Result/Notes, call scoreUserStory with JSON path + status + primary SPEC path, then freeze inputs including both wiki files. No Story score is written into SPEC. Hand the exact package, baseline and approved new SPEC upload plan to the path-only exporter for final content confirmation and submission. No routine intake repeats at export, and no automatic retry after an unknown remote result.
