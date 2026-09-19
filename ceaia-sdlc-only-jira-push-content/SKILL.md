---
name: ceaia-sdlc-only-jira-push-content
description: Final Jira write gate for the bundled CEAIA SDLC workflow and supported Jira export modes. Prepare exact previews and serialized requestJson payloads, obtain explicit request_user_approval, create reviewed Stories with SPEC attachments, or update reviewed existing Jira descriptions and selected attachments.
---

# Jira Push Content

## Responsibility

Own mode selection, Jira field collection, preview, exact payload binding, final approval, write-tool invocation, and result reporting.

Never generate or review CEAIA artifacts in this skill. Never call a Jira write tool before applicable artifact set has an independent PASS.

If any Jira target lacks an explicitly user-supplied source, immediate next action is an `ask_user_question` popup with one independent `WPB | ALM | DATA | FCR | GO` selector per unresolved target. Do not infer a source, request a Jira URL instead, combine unrelated questions, or continue Jira preparation.

## Progressive reference loading

1. Read `references/common-jira-write-gate.md`.
2. Select exactly one mode.
3. Read only reference required by that mode:
   - `uat_testcase_export`: `references/uat-testcase-export.md`
   - `ceaia_story_spec_export`: `references/ceaia-story-spec-export.md`
   - `generic_work_item_export`: `references/generic-work-item-export.md` and `references/ceaia-story-spec-export.md` for its shared work-item rules
   - `ceaia_story_spec_update`: `references/ceaia-story-spec-update.md`

Do not preload every mode reference.

The bundled SDLC workflow task uses only `ceaia_story_spec_export` and `ceaia_story_spec_update`. Preserve UAT and generic modes for their dedicated callers, but do not load them during CEAIA SDLC task.

## Resource manifest

- Always read `references/common-jira-write-gate.md`.
- Read `references/uat-testcase-export.md` only for `uat_testcase_export`.
- Read `references/ceaia-story-spec-export.md` only for `ceaia_story_spec_export`, or with `references/generic-work-item-export.md` for their documented shared rules.
- Read `references/generic-work-item-export.md` only for `generic_work_item_export`.
- Read `references/ceaia-story-spec-update.md` only for `ceaia_story_spec_update`.

This skill has no template directory. Do not infer a missing Jira template.

## Core write contract

Use `java-base-mcp.pushJiraContent` only for creation/export modes and `java-base-mcp.updateJiraTicket` only for existing-ticket updates.

Every final write call has exactly one argument:

```json
{
  "requestJson": "<serialized approved Jira payload JSON object>"
}
```

Do not pass naked Jira fields as tool arguments.

## Jira source confirmation

Require an explicit user-supplied or user-confirmed `source` of `WPB`, `ALM`, `DATA`, `FCR`, or `GO` before every ticket's first Jira read or write preparation.

Never infer or default Jira source from ticket prefix, project key, previous tool behavior, organization context, or another ticket. When several tickets lack sources, invoke one `ask_user_question` popup with one independent constrained source selector per ticket and retain per-ticket mapping. If Jira tool returns not-found or another source-related error, display exact error verbatim in popup and ask user to correct or reconfirm ticket key and source; do not request URL or try other sources automatically.

When source is missing, source selection is next and only interaction before any Jira action. Do not combine it with unrelated Jira field collection. When source-related error occurs, invoke `ask_user_question` immediately and include exact error, ticket key, confirmed source, and correction/retry/stop choices.

Before writing:

- identify mode;
- collect all required fields;
- build complete payload object;
- show mode-specific preview or manifest;
- resolve every blocker and ambiguity;
- serialize exact payload;
- bind approval to that exact serialized payload through `request_user_approval`.

If any payload-affecting value changes, rebuild preview and request approval again.

## Automatic post-review behavior

A clean PASS from `ceaia-spec-review` is automatic handoff trigger for this skill.

Do not pause after review and ask user to type continue, confirm that Jira preparation should start, or repeat original export request. Immediately:

1. select `ceaia_story_spec_export` for new reviewed Stories or `ceaia_story_spec_update` for reviewed existing tickets;
2. collect missing Jira fields or resolve attachment ambiguity;
3. show required preview or manifest;
4. call `request_user_approval`;
5. after approval, execute exact approved write payload.

Automatic continuation does not bypass preview or final approval.

## Existing Jira Acceptance Criteria compatibility

For `ceaia_story_spec_update`, verify description and Acceptance Criteria were requested separately during intake.

An empty, null, unavailable, not-found, or field-level error result for separate Acceptance Criteria field is acceptable when description was retrieved successfully. Description is then authoritative baseline whether or not separately identifiable Acceptance Criteria are present.

Do not block Jira intake, preview, or update solely because separate Acceptance Criteria field is empty. Require manifest to record `empty-or-unavailable`. Block Jira field intake only when description is also unavailable; later content-quality gaps follow normal score or review gates.

## Non-negotiable safety rules

- Do not invent Jira fields, ticket keys, Epic keys, attachment IDs, or mappings.
- Do not include unresolved blockers in a Jira payload.
- Use workspace-relative attachment paths without URL schemes, absolute roots, or `..`.
- Never export standalone `TEST_CASE.md`, planning, scoring, or review artifacts.
- Do not replace ambiguous attachments.
- Do not update unsupported fields in `ceaia_story_spec_update`.
- Process update batches one ticket at a time and pause before remaining tickets after first failed or partial result. Use `ask_user_question` to request correction, retry, continuation with newly approved remaining payload, or stop.
- Do not write when `request_user_approval` is unavailable or rejected.

## Popup recovery and result

Use `ask_user_question` for every missing field, ambiguity, attachment choice, preview correction, rejected approval follow-up, unavailable approval tool, invalid tool response, Jira failure, partial result, retry decision, or stop decision. Explain problem and exact information or choice needed. Use `request_user_approval` only for final authorization of exact payload.

Report success, partial, failed, rejected, or blocked accurately, but treat every non-success state as recoverable and non-terminal. List every returned Jira key or URL individually and include attachment outcomes. Never claim issue was created or updated when write tool did not confirm it.

Complete bundled SDLC workflow only when every intended Jira operation succeeds or user explicitly chooses stop or cancel. Do not end merely because workflow is awaiting input, approval was rejected, a tool is unavailable, or Jira returned partial, failed, or blocked.
