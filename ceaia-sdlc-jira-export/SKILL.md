---
name: ceaia-sdlc-jira-export
description: Export validated CEAIA SDLC Stories and reviewed SPECs to Jira, reuse user-workspace field profiles, and separately bind every successful ticket to its final Workspace score. Story create/update only.
---

# CEAIA SDLC Story Export

Version: 6.0.0 — 2026-10-31. Runtime: GPT-5.6 Luna or GPT-6 Luna. Execution: text-only.

Own transport and Jira operations after generation, content review, SPEC synchronization and final Workspace scoring. Use existing platform Workspace, popup and Jira tools; no scripts, new tools, content generation, scoring or generic helper workflow.

## Boundaries

- Accept one or more validated Story create/update items. Continue an authorized bundled handoff unless preparation-only/no-Jira or stopped; never bypass a failed gate through generic export.
- Generation owns business quality and parent readiness. Check current evidence and mappings without repeating quality review. Preserve business content; only field partition, Jira rendering and serialization change its representation.
- Reuse upstream update baselines/inventories and matching complete user-workspace profiles. New stages/sessions do not require routine remote rereads or rediscovery.
- **A writes the Story and required attachments. B is a separate five-field call binding each successful ticket to its final Workspace score. Both are required. A or attachment success never substitutes for B.**
- Story only. Existing Epic/Parent relationships are supported; Test Case export, other issue types and standalone association are outside this skill.

## Execute in order

1. Read [handoff and content](references/handoff-and-content.md). Validate selected items against current parent state and saved evidence; return stale/missing content evidence upstream.
2. Read [workspace profile](references/workspace-profile.md). Reuse matching components; query only missing/inapplicable facts, explicit changes/refresh, or supported schema-error recovery.
3. Read [fields and rendering](references/fields-and-rendering.md). Resolve allowed values and obtain one Use/Change settings confirmation for this export, including first use. Changes invalidate affected confirmation/preview bindings.
4. For attachment operations, read [attachments](references/attachments.md). Apply only the reviewed plan. none is a valid zero-upload update.
5. Read [Jira write and recovery](references/jira-write-and-recovery.md). Preserve/read back the exact A preview and numbered attempt, preflight, call A, then separately preview/call B for each eligible returned ticket. No additional final approval popup follows settings confirmation.
6. Persist eligible profile facts and report issue, attachment, binding and profile results separately. Complete only after every selected required A/attachment/B operation succeeds; explicitly stopped or pending work remains reported as such.

## Tools and context

Normal profile-hit export uses Workspace reads/writes, ask_user_question, and java-base-mcp.exportJiraByDynamicFields. Discovery/recovery also uses java-base-mcp.queryJiraProjectsByName, java-base-mcp.queryJiraIssueTypesByProject and java-base-mcp.queryJiraCreateMetaFields. java-base-mcp.getJiraInfos verifies a newly selected Epic once or reconciles necessary exceptions. readJiraAttachments belongs to upstream intake or necessary exception reconciliation. Follow live schemas; do not invent pagination, edit-metadata, checksum or bulk-binding capabilities.

Load each reference when needed. Reuse verified unchanged reads; keep current pointers/bindings in state and complete numbered attempts in files. Conversation artifacts and persistent user profiles have different scopes. Never put workflow metadata, diagnostics or internal paths into Jira-visible business fields.
