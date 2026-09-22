---
name: jira-createissue-helper
description: Jira issue export and write helper for create, update, batch export, Test Case export, user story generation, associations, attachments, dynamic fields, and non-Test Jira-wiki text rendering through java-base-mcp. Use when the user asks to export to Jira, create stories or issues, update a Jira ticket, submit to Jira, batch export Jira items, preview a Jira payload, or explain Jira fields. Also use automatically when internal workflow state contains a validated CEAIA SDLC handoff, even if the user did not say push or export. Preview all writes; generic routes require confirmation, while a validated CEAIA SDLC handoff exports directly after its dedicated final preflight.
---

# Jira Create Issue Helper

Version: 2.4.0-sdlc-v4

This skill separates reusable Jira helper capabilities from business flows. Read this entry file first, then load the common and flow modules required by the selected route.

## Core execution contract

1. Never write to Jira without a complete preview. Generic create, update, association, batch and Test Case routes retain their explicit-confirmation contract. A validated CEAIA SDLC handoff is the sole bundled exception: its dedicated gateway calls `exportJiraByDynamicFields` directly after the final preview, metadata validation, read-back and attachment preflight pass; no separate approval tool or confirmation popup is used.
2. Generate Jira content before export. For generic non-Test create, update and batch routes, render Markdown-authored Jira-visible rich text through `common-jira-text-rendering.md` before preview; keep workspace sources unchanged. Test Case content is explicitly excluded and remains controlled only by its dedicated flow.
3. Generate summary and description; do not collect them through pop-ups.
4. Assemble metadata fields after `queryJiraCreateMetaFields`.
5. Include `CEAIA_GEN` whenever labels are included or changed.
6. Use only one preview file: `jira-preview.md`, with a complete valid JSON payload.
7. Read existing issues through `getJiraInfos` before updates or associations.
8. Updates require metadata confirmation for the current project and issue type.
9. Associations are dedicated flows: no project or issue-type discovery.
10. Use one export call for a batch unless split explicitly by the user.
11. `jira-user-info.md` is SDLC-only state. Only the validated SDLC route may read or write it, and only after applying the SDLC confirmation/persistence rules in `common-state-and-user-info.md`. Generic create, update, association, batch and Test Case routes must neither read nor create that file.
12. Generic routes may reuse current conversation context and ordinary user-workspace context, but never `jira-user-info.md`.
13. For missing, ambiguous, stale, or user-changed allowed input values, follow the popup / selection UI contract in `common-state-and-user-info.md`; do not terminate the flow with normal chat asking for those values.
14. Preserve Test Case execution fields exactly apart from transport-safe line-break normalisation. For all Test Case export modes, keep required line breaks as newline characters in Jira-visible payload values and never submit literal HTML break tags such as `<br>`.
15. Test Case source export must use `flow-testcase-source-export.md` and its two forced payload shapes.
16. Final Jira-visible Test Case fields must be source-contained; do not substitute file references.
17. A CEAIA SDLC validated handoff has priority over generic create, update and batch flows.
18. Every SDLC export must first execute the dedicated `sdlc-gateway.md`; generic create, update, batch and Test Case rules do not override that gateway.
19. SDLC export requires `sdlc-handoff-validation.md`; do not regenerate or rewrite approved Story or SPEC business content.
20. An SDLC handoff is exportable only when its review result is `PASS`, `jiraReady` is `true`, `testCaseTitleValidation` is `manual-pass`, and every final SPEC review header has been synchronized and read back against that PASS.
21. SDLC Story Description is a Jira-wiki transport rendering of the reviewed `STORY.md` content. Preserve meaning and ordering while converting Markdown presentation syntax such as `## Heading` to Jira wiki syntax such as `h2. Heading`; never edit the workspace Story to perform this conversion.
22. Extract approved Acceptance Criteria into the metadata-confirmed writable Jira Acceptance Criteria field; do not append another AC section to Description.
23. SDLC export includes exactly one reviewed SPEC attachment per new Story unless an approved update manifest explicitly maps multiple distinct replacement SPECs. Do not export `TEST_CASE.md`, planning, scoring, review, state or manifest artefacts.
24. SDLC update exports are restricted to authorised Summary changes, reviewed Story Description replacement, Acceptance Criteria mapping, metadata-required fields, and explicitly authorised attachment add/replace/delete operations.
25. For SDLC exports, inspect current Jira metadata and resolve required fields from visible defaults, safe evidence-based inference, or a focused user question only when no safe value is available.
26. The SDLC route has no approval-tool step and must not wait for a final write-confirmation popup. After the SDLC defaults confirmation (when required), `jira-preview.md`, exact payload and attachment plan pass final read-back/preflight, call `exportJiraByDynamicFields` directly. Use `ask_user_question` only for unresolved required input, the SDLC defaults confirmation, ambiguity or recovery decisions.
27. Never place `linkedIssueKeys` or `linkType` in `dynamicFieldsJson`; pass them as dedicated top-level parameters to `exportJiraByDynamicFields` for association operations.
28. Never use `""` or other fabricated placeholders for missing parameter values. Apply the Mandatory Empty-Value Contract in `common-field-assembly.md` before preview and export: unused optional strings are `""` when passed and supported, otherwise omitted; preserve array/object types, block missing required values, and never turn an unspecified update into a field-clear operation.
29. `handoffType: ceaia-sdlc-validated` is internal routing/state metadata only. It is never an argument to `queryJiraProjectsByName`, `queryJiraIssueTypesByProject`, `queryJiraCreateMetaFields`, or `exportJiraByDynamicFields`, and it must not appear inside `dynamicFieldsJson`, `attachmentsJson`, or `issuesJson`.
30. The bundled SDLC workflow proceeds to this gateway after review PASS by default even when the opening request does not mention Jira export. Stop before Jira only for an explicit preparation-only/no-Jira request, explicit stop, or a recorded blocker.
31. On the first SDLC create run with no reusable defaults, proactively collect an Epic Link or an explicit no-Epic decision at the Jira stage. On later SDLC runs, validate the persisted defaults and obtain one focused confirmation of the displayed source/project/Story type/Epic/Parent values before preview and direct export. This confirms routing defaults, not final write approval.

## Module index

### Common modules

- [common-tools-and-inputs.md](references/common-tools-and-inputs.md)
- [common-state-and-user-info.md](references/common-state-and-user-info.md)
- [common-project-issue-type-validation.md](references/common-project-issue-type-validation.md)
- [common-field-assembly.md](references/common-field-assembly.md)
- [common-jira-text-rendering.md](references/common-jira-text-rendering.md)
- [common-preview-confirm-export.md](references/common-preview-confirm-export.md)
- [common-guardrails.md](references/common-guardrails.md)
- [common-attachments.md](references/common-attachments.md)

### Business flow modules

- [flow-create-issue.md](references/flow-create-issue.md)
- [flow-update-issue.md](references/flow-update-issue.md)
- [flow-associate-issue.md](references/flow-associate-issue.md)
- [flow-batch-export.md](references/flow-batch-export.md)
- [flow-testcase-source-export.md](references/flow-testcase-source-export.md)
- [flow-sdlc-validated-handoff.md](references/flow-sdlc-validated-handoff.md)
- [sdlc-gateway.md](references/sdlc-gateway.md)
- [sdlc-handoff-validation.md](references/sdlc-handoff-validation.md)

## Routing

Routing is semantic and ordered. Select the first matching route; do not downgrade a blocked specialised route to a generic route.

1. **SDLC validated handoff export:** When internal workflow state supplies `handoffType: ceaia-sdlc-validated`, enter this route automatically whether or not the request contains push/export wording. Also enter it when the request explicitly asks to export already reviewed CEAIA SDLC Story/SPEC artefacts. Read `sdlc-gateway.md`, `flow-sdlc-validated-handoff.md`, and `sdlc-handoff-validation.md` before any generic create, update or batch flow. The dedicated gateway is controlling for SDLC routing, rendering, final preflight and write safety; common modules supply reusable mechanics only. Never forward `handoffType` to a Jira MCP tool.
2. **Test Case source export:** When exporting Test Cases from a file, pasted table, spreadsheet-like content or another structured source, use `flow-testcase-source-export.md`. This route is independent of generic batch/create rules.
3. **Association:** For link, associate or relate requests, use `flow-associate-issue.md`.
4. **Generic update:** For a non-SDLC update request containing a Jira key or `/browse/<KEY>` link, use `flow-update-issue.md`.
5. **Generic batch:** For non-SDLC multiple creates/updates, tables, CSV-like lists, plural requests or explicit batch requests, use `flow-batch-export.md`.
6. **Generic create:** For other new issue or User Story requests, use `flow-create-issue.md`.
7. **Attachments:** Use `common-attachments.md` with the selected business flow. For SDLC, attachment operations are additionally constrained by the SDLC handoff flow and validation module.

If an SDLC or Test Case specialised-flow gate fails, explain the blocker and stop. Do not fall back to generic content generation or generic export to bypass its validation.

## Testable routing checks

| Input condition | Required route | Forbidden route |
| --- | --- | --- |
| Reviewed `STORY.md`, `TEST_CASE.md`, one SPEC and a PASS review handoff | SDLC validated handoff | Generic create, generic batch |
| Existing ticket plus reviewed SDLC update manifest | SDLC validated handoff | Generic update |
| Structured source Test Case table | Test Case source export | Generic batch |
| Multiple ordinary Jira tasks with no SDLC handoff | Generic batch | SDLC validated handoff |
| One ordinary new Jira bug | Generic create | SDLC validated handoff |

## Jira MCP tools

- `java-base-mcp.queryJiraProjectsByName`
- `java-base-mcp.queryJiraIssueTypesByProject`
- `java-base-mcp.queryJiraCreateMetaFields`
- `java-base-mcp.getJiraInfos`
- `java-base-mcp.exportJiraByDynamicFields`

## Platform interaction capabilities

- `ask_user_question`: collect allowed missing/ambiguous selections or supported recovery decisions. It is not used as an SDLC final-write gate.
