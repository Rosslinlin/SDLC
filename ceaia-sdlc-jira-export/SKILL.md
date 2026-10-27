---
name: ceaia-sdlc-jira-export
description: Export reviewed CEAIA SDLC Stories and SPECs to Jira, reuse persistent user-workspace field profiles, and bind each successful ticket to its final Workspace score. Use for validated SDLC create/update handoffs; supports Story only.
---

# CEAIA SDLC Story Export

Version: 1.0.0 — 2026-10-27. Target runtime: GPT-6 Luna. Execution: text-only.

Own the export stage after generation, independent content review, SPEC synchronization and final Workspace scoring. Use the platform's existing file, popup and Jira tools. Do not execute scripts, generate business content, score again, or load the generic `jira-createissue-helper`.

## Scope and fixed boundaries

- Accept `handoffType: ceaia-sdlc-validated`, one or more Story `create`/`update` items. A normal bundled handoff continues automatically unless the user requested preparation-only/no-Jira or stopped. Never route a failed handoff through generic export.
- Create only the project's confirmed Story issue type; update only confirmed Story targets. Existing Epic/Parent links may be used without creating those issue types. Test Case export and standalone issue association are outside this skill.
- Reuse generation's ticket baseline and attachment inventory. Normal export does not call `getJiraInfos` or `readJiraAttachments` again. A matching complete user-workspace profile skips all three Jira query tools, including across sessions.
- Preserve the complete reviewed Story and SPEC. Only transport rendering, field mapping and serialization may change representation. Keep workflow metadata and diagnostics out of tool payloads and Jira-visible content.
- Keep both remote stages: **A: write Story and required attachments; B: separately bind each confirmed ticket to its final Workspace Story score.** A successful A never replaces B. A binding failure never authorizes replaying A.

## Execute in this order

1. **Validate handoff.** Read [handoff and content](references/handoff-and-content.md) once. Check the selected items and current local evidence. Reuse same-stage verified reads when no intervening edit occurred. Return content defects upstream; do not repair them here.
2. **Resolve saved configuration.** Read [workspace profile](references/workspace-profile.md). Load only the matching profile from the real persistent user workspace. On a complete match use it directly. Query only for a missing/inapplicable configuration, an explicit change/refresh, or a supported schema-error recovery. Never treat a new session or elapsed time alone as a cache miss.
3. **Assemble and confirm values.** Read [fields and rendering](references/fields-and-rendering.md). Use this item's reviewed content and current update baseline. Ask only for unresolved required values or requested optional choices. Through `ask_user_question`, show the exact destination, operation, each update Ticket Number, Epic/Parent decision and actual metadata field values. Require one Use/Change confirmation for the current settings, including the first run. A settings change invalidates that confirmation; collect and confirm only the affected changes.
4. **Prepare attachments when applicable.** Read [attachments](references/attachments.md) only when an add/replace/delete action exists. `none` stays a valid zero-upload update. Verify local files and upstream mappings without another remote inventory.
5. **Write A, then B.** Read [Jira write and recovery](references/jira-write-and-recovery.md) before submission. Write and read back `jira-preview.md`, save a numbered attempt, perform final payload/local attachment preflight, then call Jira directly. There is no additional final-approval popup after the settings confirmation. On each confirmed successful Story and attachment operation, make the separate five-field binding call and verify its `markdownReview` result. Batch creates still bind every successful item separately.
6. **Persist and report.** Save reusable profile facts only from confirmed successful issue-plus-required-attachment operations under the profile rules. Keep Jira, attachment, database-binding and profile-persistence results separate. Report every returned key/URL and pending operation. Mark complete only when all selected required Jira/attachment/binding operations are confirmed successful, or the user explicitly stops the remainder.

## Tools and context discipline

Normal profile-hit export needs Workspace reads/writes, `ask_user_question`, and `java-base-mcp.exportJiraByDynamicFields`. These queries remain available for profile creation or explicit invalidation:

- `java-base-mcp.queryJiraProjectsByName`
- `java-base-mcp.queryJiraIssueTypesByProject`
- `java-base-mcp.queryJiraCreateMetaFields`

`getJiraInfos` also verifies a newly selected Epic key once; a saved confirmed Epic is not queried again. Otherwise `getJiraInfos` and `readJiraAttachments` belong to upstream intake or necessary exception reconciliation, not unconditional export preflight. Follow each live callable schema; never invent filter, pagination, checksum or bulk-binding parameters.

Load mode-specific references when the corresponding step is reached. Keep compact state with profile key, current file/review/score bindings, settings confirmation and per-operation outcomes. Store complete immutable attempt evidence in files; do not reload all past reports, all profiles or raw metadata on every step. Local output paths are conversation-specific; saved profiles are user-specific and contain no Story bodies or reusable attachment paths.
