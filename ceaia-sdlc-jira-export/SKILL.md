---
name: ceaia-sdlc-jira-export
description: Export a reviewed and scored CEAIA story.json4jira through its unchanged Workspace path after frontend content confirmation. Verify ticket, attachment, database association and score-sync outcomes without rebuilding the JSON or issuing a separate binding call.
---

# CEAIA JSON Story Export

Version: 6.1.0-preview.3 — 2026-11-03. State contract: 7. Runtime: GPT-5.6 Luna or GPT-6 Luna. Execution: text-only.

Generation owns project/type/field discovery, configuration, JSON assembly, content repair, review and scoreUserStory. This skill consumes their current evidence, obtains the scored file's frontend upload confirmation and invokes the exporter with one path. Use existing platform capabilities; no scripts, new tools, business generation or scoring here.

## Execute in order

1. Read [current handoff](references/handoff-and-content.md). Read the actual saved story.json4jira, current SPECs and referenced review/score/global-coverage evidence. Reuse verified unchanged reads; return missing, stale or inconsistent evidence upstream.
2. Read [attachment boundaries](references/attachments.md). Verify every planned upload against the scored JSON and reviewed files. Unsupported update/replace or score-only SPEC semantics are WAITING_TOOL, never permission to create another ticket or add an attachment.
3. Read [preview, submission and recovery](references/jira-write-and-recovery.md). Show the final scored .json4jira through the platform frontend and obtain actual content/upload confirmation bound to the current JSON and SPEC files. Earlier settings confirmation is insufficient. Preserve the exact preview and numbered attempt, then submit the same unchanged path.
4. Inspect and retain the complete actual response. Record ticket, each attachment, database association, queue acceptance and eventual score synchronization separately; missing evidence stays pending. Do not repeat a successful or unknown operation to repair another stage.
5. Read [profile persistence](references/workspace-profile.md) when saving reusable configuration. Persist only the eligible upstream assembly snapshot after confirmed issue/required-attachment success. Report all confirmed results and pending work; a local record or generic tool success is not database proof.

## Fixed tool boundary

The only export invocation is java-base-mcp.exportJiraByDynamicFields with exactly one argument: story_json_workspace_path, copied from paths.storyJsonPath and the actual passing score input. Do not pass business fields, identity values, attachment controls, batch arguments or a separate association payload. Backend association/queue work is required and must be evidenced, but there is no second five-field binding call in this JSON route.

Do not query projects/types/metadata, repeat normal Jira intake, render/partition Story text or rebuild the JSON during export. A defect or requested change returns to generation's assembly/review/scoring procedure. Test Case export, other issue types, generic helper/legacy routes and automatic fallback are outside this skill. Default multi-item execution is one confirmed file and one export call per candidate, stopping on failed/partial/unknown required outcomes.

The bundled workflow continues to this stage unless preparation-only/no-Jira or stopped. It must still obtain the current frontend content confirmation before writing. See [call examples](references/call-examples.md) for the one-argument shape; examples are not real paths or result schemas.
