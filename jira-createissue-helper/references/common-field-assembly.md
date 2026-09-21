# Common Field Assembly

## Metadata Parsing

The `queryJiraCreateMetaFields` response may wrap an inner JSON string in an outer tool response:

- `status`: success
- `result`: JSON string
- `tool_name`: `java-base-mcp.queryJiraCreateMetaFields`
- `namespace`: `java-base-mcp`

Processing steps:

1. Parse the outer response first.
2. If `result` is a string, parse the inner JSON next.
3. Treat `result.fields.values[]` as the authoritative field list.
4. Treat escaped HTML entities or escaped JSON characters as data representation details; do not ask the user to parse them manually.

If the response contains pagination fields such as `maxResults`, `startAt`, `total`, or `isLast`, mention possible incomplete metadata when appropriate.

## Field Inspection Items

Inspect these properties for each field:

- `fieldId`
- `name`
- `required`
- `schema.type`
- `schema.items`
- `schema.system`
- `schema.custom`
- `operations`
- `allowedValues`
- `defaultValue`
- `hasDefaultValue`
- `autoCompleteUrl`

## Required Fields First

Process all `required = true` fields first:

- `summary`: Generate from the user request and context; do not use a popup.
- `description`: Generate from the user request and context; do not use a popup.
- `project` / `projectKey`: Use the selected project.
- `issuetype` / `issueType`: Use the selected issue type.
- Other required fields: Generate or select automatically from context, metadata default, allowed values, and safe inference.

Except for allowed user inputs/selections such as `staffId`, `projectName`, and `issueType`, do not ask for required metadata fields through popup.

## Optional Field Handling

Add `required = false` fields to the payload only when:

- The user explicitly provides them.
- They can be safely inferred from context.
- They have a useful default.
- They are clearly relevant to the user request.

Omitted optional fields must not block creation.

## Default Label

Every create or update payload must include `CEAIA_GEN` when labels are included or changed.

Use this shape when labels are placed under Jira fields:

```json
{"fields":{"labels":["CEAIA_GEN"]}}
```

If the user provides additional labels, merge and de-duplicate them while always preserving `CEAIA_GEN`.

## Value Generation Strategy

- Prefer a reasonable `defaultValue` when one exists.
- For `allowedValues`, first match the user value; otherwise choose the metadata default; otherwise choose a visible non-disabled allowed value.
- Do not choose an allowed value with `disabled = true` unless the user explicitly insists and accepts Jira validation risk.
- If an opaque external ID is required and cannot be safely generated, do not fabricate it; mark it as unavailable from metadata/context in required-field status.
- For text/number fields without defaults, generate a reasonable value from summary, description, current date, field name, and schema.
- For user fields without a known user identifier, do not invent account IDs or hidden user keys.

## JSON Structure Mapping

Direct parameters:

- `project` / `projectKey` -> `projectKey`
- `summary` -> `summary`
- `issuetype` / `issueType` -> `issueType`
- `description` -> `description`

Put other fields into `dynamicFieldsJson`:

- Put custom fields such as `customfield_25800`, `customfield_12251`, `customfield_26615`, or `customfield_27708` into `customFields` unless the tool has a dedicated parameter.
- Put system fields such as `priority`, `labels`, `components`, `fixVersions`, `duedate`, `assignee`, or `timetracking` at the top level of `dynamicFieldsJson` or under the `fields` structure required by the Jira tool.
- Do not put `attachment` in `dynamicFieldsJson`; attachments require the attachment flow in `common-attachments.md`.
- Do not put `linkedIssueKeys` or `linkType` in `dynamicFieldsJson`; these are dedicated top-level parameters for association operations and must be passed directly to `exportJiraByDynamicFields`.
- Do not duplicate required fields in `dynamicFieldsJson` when they are already covered by direct parameters.

## Batch Item Field Assembly

For batch create/update, apply the same field assembly rules to each item, then place the items in one `issues` or `issuesJson` payload.

- Top-level `projectKey`, `issueType`, `description`, `dynamicFieldsJson`, and `conversationId` can act as defaults.
- Item-level values override top-level defaults.
- Unknown item fields such as `labels`, `components`, `fixVersions`, and `customfield_xxxxx` are treated as dynamic Jira fields for that item.
- Item-level attachments or attachment operations must not be merged into Jira dynamic fields.
- Use `testDetailsJson` per item for Test Case steps.
- Do not call `exportJiraByDynamicFields` once per item; assemble all item fields into one batch payload.

## Test Case `testDetailsJson` Assembly

When Test Case source content contains Test Steps and Expected Results, assemble `testDetailsJson` automatically.

- For source-based Test Case export with `uploadType=multiple`, item-level `testDetailsJson` must remain a JSON array value inside each parsed `issuesJson` item. Do not encode it as a JSON string inside the item.
- For source-based Test Case export with `uploadType=multiple`, use the source Test Case title as the item `description`; do not generate a long description from Requirement, Priority, Preconditions, Test Steps, Expected Results, or Comments.
- Pair Test Steps and Expected Results by corresponding item number and original order.
- Create one `testDetailsJson` row for each paired step/result item.
- Map the exact Test Steps source value to `step`.
- Map the exact Expected Results source value to `result`.
- Map the exact Test Data source value to `data`. Use an empty string only when the source row has no Test Data value and the active Jira Test Step contract requires the `data` key.
- Preserve item order and wording. If `step`, `result`, `data`, `description`, or any other Jira-visible field contains numbered list items, keep each numbered item on its own line using `\n`; do not collapse `1. ... 2. ...` into one line.
- Do not convert source-based Test Case line breaks to literal `<br>` tags in any export mode. For `uploadType=multiple`, item `description` and every `testDetailsJson[].step`, `testDetailsJson[].data`, and `testDetailsJson[].result` value must keep required line breaks as newline characters (`\n` in JSON). For `uploadType=single`, the top-level table `description` must do the same. Normalize `<br>`, `<br/>`, `<br />` and escaped equivalents back to newline characters before preview and export.
- If source step/result counts do not match, preserve all available content, mark the mismatch in the preview, and block export until the user corrects or explicitly provides a safe mapping.
- Do not reconstruct `step`, `result`, or `data` from a preview summary, serialized JSON string, or prior response.

For source-based Test Case export through `exportJiraByDynamicFields`, do not assemble the final payload from this common module. Execute `flow-testcase-source-export.md`, which owns the forced Create individual tickets and Group into a single ticket final payload shapes.

## Type Generation Rules

- single-select option: Prefer `{ "id": "..." }`; use visible value/name when no id is available.
- multi-select / array option: Use `["id", "id"]`.
- labels: Use a string array and include `CEAIA_GEN`.
- date: Use `YYYY-MM-DD`.
- number: Use a JSON number, not a quoted string.
- issue link: Use an explicit issue key; do not invent one.

## Field Explanation Template

Use this concise template when explaining metadata fields:

- **Field name:** Metadata display name.
- **Field ID:** For example `summary` or `customfield_12345`.
- **Required:** yes/no.
- **Type:** `schema.type`, plus `schema.items` for arrays.
- **Generated payload form:** text/date/option/multiple options/user reference/issue link, etc.
- **Allowed values:** Summarize visible options only and state which value was selected.
- **Default:** State whether `defaultValue` was used.
- **External lookup:** Mention `autoCompleteUrl` and avoid fabricating hidden ids.
- **Payload rule:** Explain whether it is a direct parameter or where it belongs in `dynamicFieldsJson`.

When custom field meaning is unclear, use only the visible name and schema; do not guess hidden business semantics.
