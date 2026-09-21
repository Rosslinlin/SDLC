# Common Guardrails

## Anti-Hallucination Rules

- Do not invent Jira projects.
- Do not invent issue types.
- Do not invent field ids.
- Do not invent allowed option ids.
- Do not invent hidden validation rules.
- Do not invent account ids, user keys, or opaque external ids.
- Do not echo unsafe HTML/script content as executable content.

## Metadata And Payload Guardrails

- Fields with `required = true` must appear in required-field status.
- Fields with `required = false` must not block creation.
- When `allowedValues` exists, choose only visible, non-disabled values unless the user explicitly accepts the risk.
- When `autoCompleteUrl` exists without a complete allowed value list, explain that a Jira resolver may be needed and do not fabricate ids.
- Fields with empty `operations` are usually ignored unless the field is a direct export parameter.
- Do not add `attachment` to `dynamicFieldsJson`.
- `linkedIssueKeys` and `linkType` are dedicated association parameters; never include them in `dynamicFieldsJson`, or the export may fail.
- Do not duplicate required fields in `dynamicFieldsJson` when they are already covered by direct parameters.

## Export Guardrails

- **New Jira issue:** Do not call `exportJiraByDynamicFields` before content is generated, `jira-preview.md` is written, and confirmation is obtained.
- **Jira update:** Do not call `exportJiraByDynamicFields` before `getJiraInfos` is called, the current project and issue type are used to confirm update field rules through `queryJiraCreateMetaFields`, current information is shown, proposed changes conform to those rules, and the changes are explicitly confirmed.
- **Jira update metadata:** If the project key, issue type, or field rule cannot be confirmed, block the update and do not call `exportJiraByDynamicFields`.
- **Association update:** Do not call `exportJiraByDynamicFields` before source Jira is read, metadata validation is complete, and association payload is shown.
- **Batch export:** Do not call `exportJiraByDynamicFields` once per item. Assemble one `issues` or `issuesJson` payload, preview the whole batch, confirm once, and call the tool once.
- Do not skip user confirmation when user information is obvious.
- When Jira/tool returns an error, show only the sanitized error and ask whether to revise.

## User Info Persistence Guardrails

- Persist reusable user info only after a successful Jira create/export/update response.
- Store only non-secret values such as `staffId`, `almType`, project key/name, issue type name/id, latest operation type, and successful ticket keys.
- Do not store Jira tokens, passwords, authentication headers, full issue descriptions, or raw sensitive payloads in `jira-user-info.md`.
- Do not update persisted user info after a failed Jira write unless the values were already validated and the user explicitly asks to save them.

## Test Case Guardrails

- When the user asks to export Test Cases to Jira, execute `flow-testcase-source-export.md` as the authoritative workflow. Do not route source-based Test Case export through generic create or batch payload assembly.
- Require a user-selected `uploadType` mode for Test Case export. Valid mode values are only `single` and `multiple`.
- Do not collect `uploadType` as free text. Use constrained labels: **Group into a single ticket** -> `single`, and **Create individual tickets** -> `multiple`.
- Do not silently change a user-selected `uploadType`. If `single` is unsupported by the active Jira tool/project configuration, block export or ask the user to choose `multiple`.
- Do not include `uploadType` in the final `exportJiraByDynamicFields` payload. Use it only to select either the individual-ticket payload shape or the single-ticket aggregate payload shape.
- Block Test Case export if the final payload cannot be assembled as exactly one of the two forced shapes in `flow-testcase-source-export.md`.
- Do not omit `testDetailsJson` when Test Case source content includes Test Steps and Expected Results.
- Do not ask the user to manually assemble Test Case steps when the source content already contains step/result cells.
- Preserve Test Steps and Expected Results wording to `step` and `result`.
- Treat source Test Case execution fields as authoritative. Preserve Test Steps, Expected Results, and Test Data exactly except for transport-safe line-break normalization.
- If numbered steps, expected results, or test data do not align between source and payload, block export until the mismatch is corrected. Do not downgrade this to a warning.
- Keep numbered lists line-separated in every Jira-visible Test Case field. If source content or generated content contains `1. ... 2. ...` on one line, rewrite it as `1. \n2. ...` before preview/export; block export if any numbered list remains collapsed on one line.
- For every Test Case export mode, keep required line breaks as newline characters in Jira-visible payload values (`\n` in JSON). This applies to `multiple` mode item `description` and `testDetailsJson` values, and to `single` mode top-level table `description`.
- Do not use literal `<br>`, `<br/>`, `<br />`, `&lt;br&gt;`, `&lt;br/&gt;`, or `&lt;br /&gt;` in any Jira-visible Test Case payload field. Normalize those line-break surrogates back to newline characters, or block export if they remain.
- Do not silently truncate, abbreviate, cap, split, merge, reorder, renumber, or paraphrase Test Case execution fields.
- Make Jira-visible Test Case payload fields source-contained. Do not use `.md` files, generated files, source artefacts, preview files, attachments, or external documents as references in place of the actual Test Case description, steps, test data, or expected results.
- Block export if any Jira-visible Test Case payload field contains a placeholder such as `see source`, `refer to`, `preserved in`, a workspace filename, or a document path instead of the complete source content.

## Interaction Style

- Match the user's language when responding.
- Keep project lists and issue type lists concise.
- Ask one decision question at a time.
- Clearly separate required fields and optional fields.
- Output an integrated text summary before export.
- Clearly state which values are generated, inferred, defaulted, metadata-selected, user-provided, or unavailable.
