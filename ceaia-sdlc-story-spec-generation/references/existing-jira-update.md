# Preserve existing-ticket intent in the JSON route

Read [workflow](workflow-contract.md) and [assembly](jira-json-assembly.md). An explicit update never becomes a create; preserve each source+ticket identity and original obligations unless the user approves a specific change.

## Intake once per current baseline

1. Confirm each source before Jira reads. Use getJiraInfos for Description and Acceptance Criteria separately; use readJiraAttachments for the complete inventory, including zero attachments.
2. Preserve the returned original files and actual attachment IDs/names/relevance/skip reasons. Read every relevant readable attachment; material skipped/unreadable evidence needs a supported handling decision.
3. Save baseline paths/statuses and actual target project/type/relationships/fields in exportContext. Reuse completed intake; do not repeat remote reads merely because another Skill/stage is invoked. Missing unsupported remote fields remain unavailable, not fabricated.
4. A usable Description with empty/unavailable separate AC can proceed after inspecting embedded criteria. Unusable Description or incomplete relevant inventory blocks that target. Known external changes require reconciliation before overwriting.

## Approved delta and transport support

Create a numbered plan plus update-manifest.md: target/source, baseline pointers, original obligation → preserved/explicitly changed/clarification, approved delta and Summary decision, actual attachment IDs → output mappings, and material questions. Score/audit data stay in separate records.

Preserve Summary, priority/assignee/labels/custom fields/status/links and Epic/Parent unless explicitly authorized within the reviewed delta. Settings/default adoption cannot broaden scope. Broad inherited sizing alone never authorizes new tickets or removal of requirements.

Bundled attachment intent remains none or replace:
- none: no upload operations, no new attachment claim. Keep a local reviewed SPEC and pass its path to scoring separately; do not put it in JSON uploads merely for scoring.
- replace: preserve each selected original's actual ID, output path/name and obligations; distinct replacement contents stay distinct. Require active schema encoding and confirmed upload-before-delete behavior. Do not guess targets from duplicate names, merge originals, or substitute add/delete.

Use the [update JSON template](../templates/story-update-json-template.json): root issueIdOrKey, placed after jiraInstance/projectKey/issueType, is the exact confirmed existing ticket key (uppercase I in Id). This selector is user-confirmed backend support; a basic update no longer waits for an unknown target-property name. Verify it against the actual baseline and exportContext.target.ticketKey before review/scoring/export. A cited evidence ticket, key prefix or cached profile cannot select the update target. Creates omit this property; a missing/empty/mismatched update value blocks the update and never becomes a create.

Both modes use root acceptanceCriteria as the real AC field-ID string and Jira wiki content at fields[acceptanceCriteria], with no customFields wrapper, fields.reporter or root staffId. Keep the scoreable supported business view and preserve unchanged baseline values; include only approved changes and fields allowed by the actual update contract. Do not copy create defaults or treat create metadata as edit permission. Omission must preserve remote values where supported, not silently clear them.

The update target confirmation does not define attachment replacement controls. A normal none update uses attachments=[] to preserve remote files and supplies its local reviewed SPEC through the confirmed specFilePath scoring input, then follows normal confirmation/export. Do not block it merely because an extra score-only capability proof was not supplied; an actual backend rejection is recorded as a precise tool gap with prepared evidence retained. Replace still requires supported replacement encoding and upload-before-delete behavior. Do not invent operation/replace/deletion properties, convert none to add, or fallback to an old field-based call. The confirmed issueIdOrKey belongs inside the scored JSON, never beside the path as an extra score/export argument.

## Generate, review and score

Assemble the supported update JSON during generation; derive tests/SPEC business views while preserving source-specific details. Read/review every current SPEC, synchronize Result/Notes before scoring, call scoreUserStory with JSON path + status + primary SPEC path, then freeze inputs. No Story score is written into SPEC. Hand the exact package and baseline/mapping/schema evidence to the path-only exporter for final content confirmation and submission. No routine intake repeats at export, and no automatic retry after an unknown remote result.
