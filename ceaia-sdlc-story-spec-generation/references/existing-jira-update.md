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

The screenshot sample does not define the new JSON's update-target or replacement fields. Before setting updateReady, actual backend/schema evidence must establish exact target encoding, omission semantics and none/replace handling, including validation of any score-only SPEC. If unavailable, return WAITING_TOOL for that target with prepared local evidence. Never invoke a create-shaped JSON for an update, invent properties or fallback to the old field-based tool call.

## Generate, review and score

Assemble the supported update JSON during generation; derive tests/SPEC business views while preserving source-specific details. Read/review every current SPEC, synchronize Result/Notes before scoring, call scoreUserStory with JSON path + status + primary SPEC path, then freeze inputs. No Story score is written into SPEC. Hand the exact package and baseline/mapping/schema evidence to the path-only exporter for final content confirmation and submission. No routine intake repeats at export, and no automatic retry after an unknown remote result.
