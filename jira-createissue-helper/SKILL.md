---
name: jira-createissue-helper
description: Prepare or export ordinary Jira issues and source Test Cases through final Workspace JSON files. Reuse upstream JSON and user profiles, preview and confirm actual content, and preserve partial write outcomes. Use for generic create, update, batch, association, attachment or export requests; CEAIA SDLC uses its dedicated exporter.
---

# Jira Create Issue Helper

Version: 3.0.0-json-preview.2 — 2026-11-07. Runtime: GPT-5.6 Luna or GPT-6 Luna, platform text/file tools only; no scripts.

The submission source is the saved final `.json4jira`, or an approved path manifest. Production content export calls `java-base-mcp.exportjirabydynamicfield` with only `story_json_workspace_path`. Read [the JSON contract](references/common-json4jira-contract.md) and the selected flow; load preparation references only when facts/files are missing. CEAIA SDLC handoffs belong to their dedicated exporter; this helper has no legacy SDLC execution route.

## Select one flow

| Actual request/input | Read next |
| --- | --- |
| Resume/check a prior write or failed downstream effect | [Results and recovery](references/common-preview-confirm-export.md); preserve prior successes before any new action. |
| Export Test Cases from a selected source/table or their canonical files | [Test Case source export](references/flow-testcase-source-export.md), including its current single/multiple selection. It owns its shapes and overrides generic batch/rendering rules. |
| Link/associate existing issues | [Association](references/flow-associate-issue.md); use only supported relationship encoding. |
| Several ordinary issue creates/updates | [Batch](references/flow-batch-export.md). Plan individual Story calls and non-Story manifests in approved selection order. |
| Modify an explicitly selected existing issue | [Update](references/flow-update-issue.md). A selected update file's exact root target is authoritative; keys in prose/Epic/Parent are references. |
| Prepare/create another ordinary issue | [Create](references/flow-create-issue.md). |
| Requested attachments | Also read [attachments](references/common-attachments.md); Test Case source shapes omit them. |

A complete trustworthy upstream JSON keeps its exact path/content and needs no discovery or text conversion just because another skill authored it. It still enters the durable profile lookup and post-write save/read-back lifecycle below. Resolve actual operation, source/type and target from the selected file and confirmed request. A missing specialized capability stays precisely pending; do not downgrade it to create or an older tool shape.

## Execute in order

1. Read [durable profiles and state](references/common-state-and-user-info.md), actual selected inputs and existing results on every route. Match the selected source/project/type without replacing other profiles. Resolve missing source then project/type; offer supported optional fields, and confirm applicable saved settings before authoring a create. A current matching explicit instruction or upstream settings agreement can answer that check. Reuse an established usable JSON unchanged; otherwise prepare one through [missing metadata](references/common-project-issue-type-validation.md) and [field assembly](references/common-field-assembly.md). Query only missing applicable facts. Complete non-Test rich-text rendering before confirmation and any separately requested assessment.
2. For updates or attachment/relationship changes, use actual relevant target/baseline evidence. Read Jira when needed; reusable profiles are not current ticket/attachment data and never select a target or overwrite its fields.
3. There is no global score prerequisite or automatic scoring in this helper. Preserve [actual upstream assessment evidence](references/story-assessment-evidence.md) when present; the backend owns project-specific scoring requirements. Story files submit individually, while non-Story items may use a path manifest under the batch flow. No mandatory SPEC or locally invented score threshold.
4. Follow [preview, confirmation and submission](references/common-preview-confirm-export.md). Show actual saved files, selected operations/targets, fields, attachments and per-item effects. Obtain/reuse real explicit confirmation for this unchanged content. An edit returns to preparation and renewed confirmation; earlier assessment of changed input remains historical.
5. Read back the exact approved file/manifest and children, then make one path-only content call per approved submission unit. Preserve the complete actual response and report each effect independently. No second score binding or ticket/source synchronization call follows JSON export.
6. After each confirmed issue create/update, including successful manifest children, complete the [profile merge/write/read-back](references/common-state-and-user-info.md) in the real persistent user Workspace. This is required even for upstream files and when attachments/linkage/queue fail separately. Save meaningful verified routing/schema facts and explicitly remembered operation-scoped preferences; Story uses canonical issueType plus its matching storyType alias, other types use issueType. Report profilePersistence separately as saved/pending/failed/unavailable/not_eligible with actual scope/read-back or the precise cause. A profile-only failure never replays discovery, scoring or a remote write.

## Tools to expose

- `java-base-mcp.exportjirabydynamicfield`: required production content export, exactly one path argument here. The test alias `exportJiraByDynamicFieldsV2` is not used.
- `java-base-mcp.queryJiraProjectsByName`, `java-base-mcp.queryJiraIssueTypesByProject`, `java-base-mcp.queryJiraCreateMetaFields`: prepare genuinely missing routing/field facts after profile lookup. Use their actual exposed production schemas under [tools](references/common-tools-and-inputs.md).
- `java-base-mcp.getJiraInfos`: current target facts for update/association/attachment work when not already established. Use a supported attachment-inventory reader if the issue response does not expose needed real IDs.
- Platform Workspace search/read/write, persistent user-Workspace read/write, native file preview and `ask_user_question` or supported confirmation/selection UI. No scorer, reviewer delegation or sibling filesystem access is required for ordinary export.

Request authentication/session context is platform-owned. Do not ask users for tokens/Conversation-ID, parse JWTs or add identity fields to business JSON/score/export calls. A production tool requiring unavailable injected identity is an actual integration blocker, not permission to invent it.

## Continuity and boundaries

Keep source/preparation facts, score bindings, approved paths and actual write outcomes in existing context/results. Use the final JSON as business truth and at most one derived `jira-preview.md` per operation; do not rebuild content from that view. The required batch manifest is transport input, not a duplicate state file. Preserve prior score/write results and partial successes on resume.

Preserve supplied/approved wording, IDs, order, code, links, mapped Test Case cells and extra supported fields. Generate content only when the user actually requests it from a business brief; mark generation and show it before confirmation. No required-field invention, arbitrary option selection, automatic business repair, fabricated success, credentials in profiles, or export/rescore retry loops. See [guardrails](references/common-guardrails.md) only for an unresolved cross-cutting boundary.
