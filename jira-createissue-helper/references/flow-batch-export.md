# Flow Batch Export

## Scope

Use this module when the user asks to create, update, export, or submit multiple Jira issues or Test Cases in one request.

Also load:

- `common-tools-and-inputs.md`
- `common-state-and-user-info.md`
- `common-project-issue-type-validation.md`
- `common-field-assembly.md`
- `common-jira-text-rendering.md`, for non-Test items only
- `common-preview-confirm-export.md`
- `common-guardrails.md`
- `common-attachments.md`, when attachments are present

For Test Cases from source artifacts or structured source content, first load `flow-testcase-source-export.md`.

## Strong Batch Rule

Batch requests are a single-call flow:

- Recognize batch intent from plural wording, multiple listed items, tables, CSV-like content, multiple Test Cases, or requests such as batch export, create these issues, update those tickets, or export all.
- Assemble one `issuesJson` payload containing all items. For Test Case export with `uploadType` mode `single`, assemble one aggregate Test issue payload with top-level `summary`, clean table-only `description`, and labels inside the JSON-encoded `dynamicFieldsJson` object instead of item-list fields.
- Before preview, render Markdown-authored rich text for each eligible non-Test item through `common-jira-text-rendering.md`. Do not apply common rendering to any Test Case item or field; mixed batches preserve the existing Test Case rules item by item.
- If any Story has prior Workspace scoring evidence, read [generic Story score binding](generic-story-score-binding.md) before preview and plan binding only for eligible items.
- Generate one integrated batch preview in `jira-preview.md` before export.
- Ask for explicit confirmation once for the whole batch.
- Call `exportJiraByDynamicFields` exactly once for the approved batch issue-write payload.
- Never loop through batch items and repeat issue-write calls unless the user explicitly asks to split the batch. Successful scored Story items may receive separate post-write calls under [generic Story score binding](generic-story-score-binding.md).

## Supported Batch Scenarios

- Batch create Jira issues.
- Batch update existing Jira issues.
- Batch create or update Test Cases only when not exporting Test Cases from a user source. Source-based Test Case export must follow `flow-testcase-source-export.md`.
- Batch upload Test Details/Test Steps for existing Test issue types when not part of source-based Test Case export.
- Batch add attachments after issue creation or update.
- Batch delete or replace attachments when updating existing issues.

## Batch Mode Trigger

Batch mode uses active item data in `issuesJson`, not the mere presence of a parameter key. An explicitly supported inactive `issuesJson: ""` in a non-batch flow does not enable batch mode. A requested batch must provide a valid non-empty item list; missing, malformed, or empty batch data blocks export rather than falling back to a single create. Test Case source export with `uploadType` mode `single` is a single-call export and must not include `issuesJson`.

The current `exportJiraByDynamicFields` interface exposes `issuesJson` as a JSON-encoded non-empty array string; it does not expose an `issues` argument.

## Base Request Fields

Top-level fields act as defaults for all items:

- `staffId`: Required. Staff ID used to load the user's Jira token.
- `almType`: Required. Jira source: `wpb`, `alm`, `data`, `fcr`, or `go`.
- `uploadType`: Required only for Test Case source export as a mode selection. Valid values are `single` and `multiple`; do not include this control value in the final `exportJiraByDynamicFields` payload.
- `projectKey`: Default project key for create items.
- `issueType`: Default issue type for create items.
- `description`: Optional default description.
- `dynamicFieldsJson`: Optional default dynamic fields.
- `conversationId`: Optional default conversation id for workspace attachments.
- `attachmentsJson`: Optional top-level attachment operations for a separate single-item call; an active batch sets each item's attachment plan inside `issuesJson` because top-level attachments are not inherited.
- `issuesJson`: Optional JSON array string for batch items.

Item-level fields override top-level defaults. Map Jira fields such as `labels`, `components`, `fixVersions`, and `customfield_xxxxx` into that item's JSON-encoded `dynamicFieldsJson`; do not send them as undocumented batch-item parameters.

Validate each effective item after default inheritance using `common-field-assembly.md`'s Mandatory Empty-Value Contract. Update items must not inherit changes to unspecified fields; relocate defaults to the intended items when necessary. A requested update with a missing or empty `issueIdOrKey` must block, not silently become a create.

## Create Versus Update Items

- A batch item with `issueIdOrKey` updates an existing issue.
- A batch item without `issueIdOrKey` creates a new issue.
- Create items require `projectKey`, `summary`, and `issueType` from either item-level fields or top-level defaults.
- Update items must provide their own `issueIdOrKey`.

## Batch Create Payload

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "projectKey": "AIWPB",
  "issueType": "Story",
  "issuesJson": "[{\"summary\":\"Batch story 1\",\"description\":\"Story 1 desc\",\"dynamicFieldsJson\":\"{\\\"fields\\\":{\\\"labels\\\":[\\\"CEAIA_GEN\\\",\\\"BATCH\\\"],\\\"customfield_12345\\\":{\\\"value\\\":\\\"High\\\"}}}\"},{\"summary\":\"Batch story 2\",\"issueType\":\"Task\",\"dynamicFieldsJson\":\"{\\\"fields\\\":{\\\"labels\\\":[\\\"CEAIA_GEN\\\"],\\\"customfield_67890\\\":\\\"Business value\\\"}}\"}]"
}
```

## Batch Update Payload

Each item to update must provide its own `issueIdOrKey`.

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "issuesJson": "[{\"issueIdOrKey\":\"AIWPB-101\",\"summary\":\"Updated story summary\"},{\"issueIdOrKey\":\"AIWPB-102\",\"dynamicFieldsJson\":\"{\\\"fields\\\":{\\\"description\\\":\\\"Updated description\\\",\\\"labels\\\":[\\\"CEAIA_GEN\\\"],\\\"customfield_12345\\\":{\\\"value\\\":\\\"Medium\\\"}}}\"}]"
}
```

## Batch Test Case Payload

This module is not authoritative for source-based Test Case export.

When the user asks to export Test Cases from a source artifact, pasted table, spreadsheet-like content, or generated Test Case list into Jira, stop using this module as the primary flow and execute `flow-testcase-source-export.md`.

Do not define, preview, or submit an alternative Test Case export payload from this module. The only allowed source-based Test Case export payload shapes are the two forced shapes in `flow-testcase-source-export.md`.

## Batch Preview Requirements

Before export, write one `jira-preview.md` according to `common-preview-confirm-export.md`.

For source-based Test Case export preview requirements, follow `flow-testcase-source-export.md`.

## Batch Response Handling

Batch responses may include:

```json
{
  "resources": [
    {
      "index": 0,
      "summary": "Batch story 1",
      "status": "pass",
      "ticketKey": "AIWPB-101",
      "ticketUrl": "https://xxx/browse/AIWPB-101"
    },
    {
      "index": 1,
      "summary": "Batch story 2",
      "status": "fail",
      "error": "summary must be provided"
    }
  ],
  "successCount": 1,
  "failureCount": 1
}
```

Report batch results by item index and status. One failed batch item does not stop later items.

For each successful Story with prior Workspace scoring evidence, apply [generic Story score binding](generic-story-score-binding.md). Do not bind failed or unscored items.

## Batch Notes

- `dynamicFieldsJson`, when provided, must be a JSON-encoded string representing one object, including inside each `issuesJson` item.
- For active attachment operations, `attachmentsJson` must be a once-serialized JSON string containing the validated plan from `common-attachments.md`, not an object or array.
- `issuesJson` must be a JSON array string. For source-based Test Case export, follow `flow-testcase-source-export.md` instead of this generic batch note.
- Omit unused optional parameters; an actual empty string is allowed only where the active tool accepts it as an inactive sentinel. Apply the Mandatory Empty-Value Contract to top-level inputs and every item; inactive sentinels cannot substitute for required batch or operation data.
- Do not put attachment operations into Jira dynamic fields.
- Do not split a batch into repeated single-item tool calls unless explicitly requested.
