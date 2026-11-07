# Update an existing Jira issue

Use for a confirmed existing-ticket content change. Keep the exact target in root `issueIdOrKey` under [the file contract](common-json4jira-contract.md); a reference ticket, Epic or parent mentioned in prose is not an update target.

## Establish the target and baseline

Read [durable user configuration](common-state-and-user-info.md) on entry, including when a complete upstream update JSON is supplied. Reuse the actual confirmed source/target mapping and trustworthy current target evidence. Source must come from confirmed context or an established Jira-host mapping, never from a ticket prefix. A profile cannot select the target or overwrite unrequested fields.

Read `java-base-mcp.getJiraInfos` when the actual target's project/type, relevant current fields, relationships or preservation baseline are unavailable. Read the fields needed for the requested change; broad replacement needs a complete relevant baseline. Existing verified baseline evidence can be reused when still applicable. A prepared file with complete target/assembly evidence does not require repeating intake or field-discovery calls merely to export it.

For requested attachment changes, read the actual target's inventory as described in [attachments](common-attachments.md). Do not substitute cached settings for live target content or original attachment IDs.

## Reuse or prepare the update file

1. If the selected final JSON already matches the confirmed target, current approved change and file contract, retain its exact path/content. Do not query or rebuild it as a routine export step.
2. Otherwise read [configuration](common-state-and-user-info.md) first and query only missing applicable facts under [discovery](common-project-issue-type-validation.md). Stable project/type/field facts can be reused across create/update. Create metadata does not prove edit permission.
3. Use [field preparation](common-field-assembly.md) to include both mandatory `fields.project.key` and `fields.issuetype.id` from verified target/routing facts on every update, even when changing only one business field. They agree with root project/type and are never removed as unchanged or absent from metadata-required lists. Keep other fields limited to approved changes and required baseline values; omit optional unrequested business fields, never clear them through omission/template empties. Saved create defaults cannot overwrite target values.
4. Render only newly prepared non-Test rich-text replacements under [text rendering](common-jira-text-rendering.md). Preserve untouched fields. Finish the supported attachment/relationship plan before preview and confirmation.
5. Before saving as export-ready and on read-back, verify native `fields.project`/`fields.issuetype` with actual nonempty key/ID and consistent root/baseline binding. A root-only or sparse-delta upstream file missing either needs preparation correction and renewed confirmation, never direct export. Root `issueIdOrKey` remains the exact nonempty target; missing/conflicting routing or target blocks this update and never becomes create.

## Assessment evidence and submission

Use [assessment evidence](story-assessment-evidence.md) when supplied. A ticket/source assessment of the online ticket is not the local revision's assessment; an input edit invalidates any claim that an older JSON result is current. Report stale evidence accurately, but do not impose a helper scoring prerequisite or automatically invoke scorer/reviewer. The actual project/backend decides whether a score is required. No generated SPEC or local minimum score is required here.

Follow [preview and submission](common-preview-confirm-export.md): show the target, known baseline, approved differences and actual file; obtain/reuse current-content confirmation; submit the unchanged path through `java-base-mcp.exportjirabydynamicfield` with only `story_json_workspace_path`. A Story always uses its individual issue-file path, including in [a multi-item plan](flow-batch-export.md); it never enters a manifest even for a scoring-exempt project.

Preserve issue success separately from attachment, association, database and queue outcomes. A backend score-required rejection remains pending; do not silently rescore or repeat export. Refer the user to the reviewer when assessment is requested. Do not repeat a confirmed update to repair another outcome, issue a separate legacy binding, or call ticket/source score synchronization after this local JSON export.

After confirmed issue update success, complete the real user-Workspace merge/write/read-back under [configuration](common-state-and-user-info.md). Save verified stable facts and only actually confirmed update-scoped preferences, preserving create defaults and all other project/type entries. Do not wait for separate attachment/linkage/queue success or infer edit permissions from a successful create. Report profile persistence independently; its failure resumes only that save, never this update.
