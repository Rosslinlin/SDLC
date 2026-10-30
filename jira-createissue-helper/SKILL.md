---
name: jira-createissue-helper
description: Jira issue export and write helper for generic create, update, batch export, Test Case export, user story generation, associations, attachments, dynamic fields, and non-Test Jira-wiki rendering through java-base-mcp. Use for ordinary Jira export/update requests, payload previews, or Jira fields. Current bundled CEAIA SDLC handoffs use ceaia-sdlc-jira-export; this helper retains an explicitly selected legacy SDLC gateway. Preview all writes; generic routes require confirmation, while the selected legacy gateway follows its dedicated preflight.
---

# Jira Create Issue Helper

Version: 2.6.0-sdlc-v5

This skill separates reusable Jira helper capabilities from business flows. Read this entry file first, then load the common and flow modules required by the selected route.

## SDLC exporter ownership

The current bundled SDLC workflow uses `ceaia-sdlc-jira-export` in both Task mode and normal conversation mode. If a current SDLC handoff or request reaches this helper, transfer its unchanged reviewed content, state and evidence pointers to that exporter before loading common modules or the legacy gateway. Do not perform Jira writes here for that handoff. If the current exporter is unavailable or blocked, report the blocker rather than falling back to this helper.

The helper-based SDLC gateway remains available only when the user or caller explicitly selects the **legacy helper-based SDLC workflow**. A validated `handoffType: ceaia-sdlc-validated` alone is not a legacy selection. All SDLC-specific helper rules and references below apply only to that explicitly selected legacy route. Ordinary non-SDLC Story scoring/export, generic create/update/batch, associations and Test Case routes retain their existing rules.

## Core execution contract

1. Never write to Jira without a complete preview. Generic create, update, association, batch and Test Case routes retain their explicit-confirmation contract. An explicitly selected legacy helper-based CEAIA SDLC handoff is the exception in this helper: its dedicated gateway calls `exportJiraByDynamicFields` directly after the final preview, metadata validation, read-back and attachment preflight pass; no separate approval tool or confirmation popup is used.
2. Generate Jira content before export. For generic non-Test create, update and batch routes, render Markdown-authored Jira-visible rich text through `common-jira-text-rendering.md` before preview; keep workspace sources unchanged. Test Case content is explicitly excluded and remains controlled only by its dedicated flow.
3. Generate summary and description; do not collect them through pop-ups.
4. Assemble metadata fields after `queryJiraCreateMetaFields`.
5. The Jira backend adds `CEAIA_GEN` on export. Never put it in a tool payload; send only other requested or required labels.
6. Use only one preview file: `jira-preview.md`, with a complete valid JSON payload.
7. Read existing issues through `getJiraInfos` before updates or associations.
8. Updates require metadata confirmation for the current project and issue type.
9. Associations are dedicated flows: no project or issue-type discovery.
10. Generic batches use one Jira issue-write export call unless split explicitly by the user; after its response, bind every successful Story with verified Workspace scoring evidence separately before marking that item complete. SDLC multiple creates may use one issuesJson call; bundled Task updates execute one ticket at a time under the SDLC gateway.
11. Persist reusable, non-secret user information only after success.
12. Check conversation context and ordinary user-workspace context before asking reusable basics. Read `jira-user-info.md` only for the validated SDLC route.
13. For missing, ambiguous, stale, or user-changed allowed input values, follow the popup / selection UI contract in `common-state-and-user-info.md`; do not terminate the flow with normal chat asking for those values.
14. Preserve Test Case execution fields exactly apart from transport-safe line-break normalisation. For all Test Case export modes, keep required line breaks as newline characters in Jira-visible payload values and never submit literal HTML break tags such as `<br>`.
15. Test Case source export must use the text-only `flow-testcase-source-export.md` and its two required payload shapes.
16. Final Jira-visible Test Case fields must be source-contained; do not substitute file references.
17. Dispatch a current bundled SDLC handoff to `ceaia-sdlc-jira-export` before any helper route. An explicitly selected legacy SDLC handoff has priority over generic create, update and batch flows.
18. Only the explicitly selected legacy helper-based SDLC route executes `sdlc-gateway.md`; generic create, update, batch and Test Case rules do not override that gateway.
19. SDLC export requires `sdlc-handoff-validation.md`; do not regenerate or rewrite approved Story or SPEC business content.
20. An SDLC handoff is exportable only when independent content review is `PASS`, every final SPEC review header has been synchronized and read back, the final Workspace Story score is current and passing with verified primary SPEC status/path, `jiraReady` is `true`, and `testCaseTitleValidation` is `manual-pass`. The reviewer report itself keeps jiraReady=false before scoring; use current parent state for final readiness.
21. For SDLC only, split the exact reviewed `STORY.md` at its canonical `## Acceptance Criteria` section. Jira Description is a Jira-wiki transport rendering of every other Story section in its original order; the Acceptance Criteria section and its criteria must not appear there. Keep the Workspace Story unchanged.
22. Render the extracted criteria once into the metadata-confirmed writable Jira Acceptance Criteria field, preserving AC IDs and Given/When/Then wording. On an update, leave that field untouched only after verifying that its retrieved current value already matches. Apply the same split to SDLC create, update and batch items, whether reached from Task mode or the normal conversation route; block a proposed field mapping that duplicates or omits criteria across the two effective Jira fields.
23. SDLC export includes exactly one reviewed SPEC attachment per new Story unless an approved update manifest explicitly maps multiple distinct replacement SPECs. Every SPEC uploaded by SDLC create or update (`add` or replacement upload) must be named `<name>-spec.md`, where `<name>` is the reviewed lowercase kebab-case Story/business-function name and `-spec.md` is a mandatory fixed suffix, with no filename exceptions. Enforce `sdlc-handoff-validation.md` Section 2.1 across the actual file, handoff, review, manifest and upload plan; block noncompliant names rather than changing only the upload `fileName`. Do not export `TEST_CASE.md`, planning, scoring, review, state or manifest artefacts.
24. SDLC update exports are restricted to authorised Summary changes, reviewed non-AC Story Description replacement, Acceptance Criteria mapping when its current value differs, metadata-required fields, and explicitly authorised attachment add/replace/delete operations.
25. For SDLC exports, inspect current Jira metadata and resolve required fields from visible defaults, safe evidence-based inference, or a focused user question only when no safe value is available.
26. For a validated SDLC handoff, use the gateway's direct-export rule after its final preview/read-back/preflight. The final score always comes from Workspace `STORY.md` for both create and update, with the primary SPEC Result/path saved in that scoring invocation. After each confirmed successful Jira create/update, call `exportJiraByDynamicFields` a second time with exactly the five real values `staffId`, `almType`, returned `issueIdOrKey` ticket key, `conversationId`, and that final passing score's `STORY.md` path as `markdownReviewRelativePath`. Do not add this path to the first Jira-write call. The SDLC gateway controls separate preview, outcome tracking, and recovery for this mandatory binding call; generic routes retain their confirmation contract.
27. Never place `linkedIssueKeys` or `linkType` in `dynamicFieldsJson`; pass them as dedicated top-level parameters to `exportJiraByDynamicFields` for association operations.
28. Never use `"."` or other fabricated placeholders for missing parameter values. Apply the Mandatory Empty-Value Contract in `common-field-assembly.md` before preview and export: unused optional strings are `""` when passed and supported, otherwise omitted; preserve array/object types, block missing required values, and never turn an unspecified update into a field-clear operation.
29. For export-ready Jira-visible content, do not let the LLM rewrite business meaning. Apart from required Jira transport formatting, field mapping, JSON escaping, and safe line-break normalization, preserve supplied source wording, order, identifiers, table cells, steps, expected results, acceptance criteria, Story/SPEC content, and user-approved replacement text exactly. If content must be generated from a raw business request, mark it as generated and require preview confirmation before export.
30. For a non-SDLC generic Story create/update/batch item with prior Workspace scoring evidence in context or a known/supplied result file, read [generic Story score binding](references/generic-story-score-binding.md) to verify it and bind after that item's Jira write succeeds. Never score as part of export or bind an unscored Story. This conditional step does not apply to other issue types, Test Case, association, or SDLC routes.

## Module index

### Common modules

- [common-tools-and-inputs.md](references/common-tools-and-inputs.md)
- [common-state-and-user-info.md](references/common-state-and-user-info.md)
- [common-project-issue-type-validation.md](references/common-project-issue-type-validation.md)
- [common-field-assembly.md](references/common-field-assembly.md)
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

1. **SDLC dispatch:** For `handoffType: ceaia-sdlc-validated` or a request to export reviewed CEAIA SDLC Story/SPEC artifacts, use `ceaia-sdlc-jira-export` for the current bundle, with or without Task orchestration and whether or not the request contains push/export wording. Stop this helper's processing after transfer. Only an explicit legacy helper-based workflow selection enters this helper's old SDLC route: then read `sdlc-gateway.md`, `flow-sdlc-validated-handoff.md`, and `sdlc-handoff-validation.md` before any common mechanics. An incomplete/failed handoff stays blocked or returns upstream; never bypass it through generic export. Never forward `handoffType` to a Jira MCP tool.
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
| Current bundled reviewed Story/Test Case/SPEC handoff | Transfer to `ceaia-sdlc-jira-export` | Legacy helper gateway, generic create/batch |
| Current bundled existing-ticket SDLC update manifest | Transfer to `ceaia-sdlc-jira-export` | Legacy helper gateway, generic update |
| Explicit legacy helper-based SDLC selection with a validated handoff | Legacy SDLC gateway | Generic create/update/batch |
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
