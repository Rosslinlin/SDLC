---
name: ceaia-sdlc-only-jira-push-content
description: Final Jira write gate for reviewed CEAIA Story/SPEC creation and updates, with exact payload and attachment approval; also routes dedicated UAT testcase and generic work-item callers to their original export rules.
---

# CEAIA Jira Push

Bundle revision: 3.0.1 text-only — 2026-09-21.

Own Jira fields, preview, content-bound final approval, writes and per-operation results. Do not generate/review CEAIA content or replace independent review with export checks.

## Load and route

Select exactly one mode; load only that mode's references:
- `ceaia_story_spec_export`: [Story export](references/ceaia-story-spec-export.md).
- `ceaia_story_spec_update`: [Story update](references/ceaia-story-spec-update.md).
- `generic_work_item_export`: original [common write gate](references/common-jira-write-gate.md), [generic export](references/generic-work-item-export.md), [work-item field mapping](references/work-item-field-mapping.md) and shared payload rules in Story export; CEAIA artifact gates do not apply to generic work.
- `uat_testcase_export`: original [common write gate](references/common-jira-write-gate.md) and [UAT testcase export](references/uat-testcase-export.md). Preserve the dedicated caller's existing Test Case flow; do not route it through CEAIA generation, review or Story export.

For CEAIA modes read [CEAIA write gate](references/ceaia-jira-write-gate.md) and [shared workflow contract](../ceaia-sdlc-story-spec-generation/references/workflow-contract.md), not the legacy common write gate. All remaining sections below apply only to CEAIA modes. Other callers follow their original common and mode-specific references. Links are Skill-relative; artifact paths are workspace-relative.

For explicitly requested optional creation fields or new Epic-plus-Stories, also read [work-item field mapping](references/work-item-field-mapping.md). This preserves the combined creation branch; it is never triggered automatically.

## CEAIA entry gate

Read actual current state, review report, Story and SPEC. Require all selected candidates' current SCORE and review PASS, current file bindings, manual-pass titles and global source coverage. For updates require supported action and preservation mapping, including valid none.

If content/mapping changed since review, return the exact repair scope to generation. New Story bytes require new scoring; a SPEC-only defect does not. Never rewrite content during export to make a payload pass.

Reuse confirmed per-target source and completed stage state. If a source is missing, ask_user_question collects independent WPB/ALM/DATA/FCR/GO selectors before Jira access. Do not infer source.

## Sequence

1. Validate handoff and destination; collect missing mode-specific fields.
2. Construct actual complete payload, show preview and attachment content/change summary.
3. Bind exact serialized requestJson and every uploaded file's final bytes to request_user_approval.
4. When approved, verify bindings again and execute the exact approved request.
5. Record actual issue/attachment outcomes; reconcile partial or unknown results before any retry.

Clean handoff automatically starts preparation without asking the user to type continue. Final authorization remains mandatory. Await asynchronous approvals by yielding; if the approval tool returns a completed decision synchronously, use that actual decision. Never infer approval from ordinary chat or an information popup.

Create uses java-base-mcp.pushJiraContent; update uses java-base-mcp.updateJiraTicket. Each has exactly one argument: `requestJson`, a serialized JSON object string. Never switch to jira-createissue-helper automatically.

WAITING_USER/WAITING_TOOL is a resumable return, not a loop or success. Complete only after all selected operations are confirmed or user explicitly stops.
