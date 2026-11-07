# Final JSON and path-only export contract

This is the shared production contract. Prepare the file before confirmation and any separately requested assessment. Saved business values, not a rendered preview or a chat summary, are the submission source.

## One issue file

Save one native JSON object in a real `.json4jira` file:

| Location | Actual meaning |
| --- | --- |
| `jiraInstance` | Confirmed lowercase source: `wpb`, `alm`, `data`, `fcr` or `go`. |
| `projectKey` | Actual selected project key. |
| `issueType` | Actual Jira type name, with its real type ID established from applicable metadata/baseline evidence. |
| `issueIdOrKey` | Exact confirmed update target, immediately after `issueType`. Omit for create; never empty/null or selected from a reference/Epic/profile. |
| `fields` | Native Jira field values keyed by real field IDs; actual types/options, no serialized inner object. |
| `attachments` | For supported ordinary uploads, only authorized, readable Workspace-relative file paths. Test Case source files omit this property. See [attachments](common-attachments.md). |

For an ordinary create, include actual `fields.project.key` and `fields.issuetype.id` when required by the selected file/metadata contract; they agree with root routing. Establish actual type identity for [individual Story versus non-Story batch handling](flow-batch-export.md). Never relabel a Story to put it in a manifest. Native Test Case shapes are owned by [their dedicated flow](flow-testcase-source-export.md), not the generic create template.

Story create/update files retain the confirmed root `acceptanceCriteria` field-ID string, with AC text in `fields[acceptanceCriteria]`. Use the [Story create](../templates/story-json-template.json) or [Story update](../templates/story-update-json-template.json) shape. Keep non-AC Description and the full source criteria in their respective fields, preserving original labels/grouping. Do not duplicate AC business text in Description. Other issue types do not acquire a fabricated Story AC mapping or mandatory SPEC.

For ordinary non-Test preparation, use the [generic issue shape](../templates/generic-issue-template.json) with actual applicable fields. Preserve supplied/approved labels and any actual supported backend marker behavior; do not copy Test Case's mandatory marker into unrelated types as a new gate. Test Case explicitly includes `CEAIA_GEN` in native labels. Never edit a scored file at export merely to add or remove a label.

Templates describe shape only. Replace every instruction/example with verified source or metadata before submission. Preserve required empty values only when actually supported; an unspecified update field is omitted, not cleared. A required routing/technical value cannot be fabricated or replaced by `"."`. Literal punctuation in source content is not a placeholder.

No root `staffId`, credential/session overrides, `conversationId`, `customFields` wrapper, independent BDD object or `fields.reporter` is generated. Do not copy old `dynamicFieldsJson`, `attachmentsJson`, `issuesJson` or write-tool argument objects into the file as a migration shortcut. Supported extensions need actual production schema evidence; do not strip an unknown business field to pass validation.

## A path manifest

A non-Story path manifest, including Test Case `multiple`, has exactly one property:

```json
{
  "story_json_workspace_paths": [
    "outputs/jira/testcases/TC-001.json4jira",
    "outputs/jira/testcases/TC-002.json4jira"
  ]
}
```

Paths are actual unique child files in approved source order. The manifest is not an inline issue array, contains no defaults/identity/attachments, and cannot nest another manifest. Each child has its own routing/fields. Example paths are not actual inputs. This format also supports ordinary non-Story types; Story children are prohibited even in projects that do not require scoring. [Batch planning](flow-batch-export.md) submits multiple Stories as individual calls and shows any mixed execution plan.

Read the manifest and every child, verifying every child is actually non-Story. Do not score a manifest. The complete selected set must pass applicable preparation and current confirmation checks before submission. Do not silently remove a blocked child. Preserve any [actual upstream assessment evidence](story-assessment-evidence.md) separately; it is not a universal export prerequisite.

## Exact production invocation

Call `java-base-mcp.exportjirabydynamicfield` with exactly one direct argument:

```json
{
  "story_json_workspace_path": "outputs/jira/testcases/export-manifest.json"
}
```

For one issue/single-mode aggregate, this value is its actual issue file path. For an approved batch, it is the manifest file path. Keep the tool's singular argument distinct from the manifest's plural property. The screenshots' `exportJiraByDynamicFieldsV2` name is a test-environment alias, not the production tool ID.

Do not add source, staffId, ticket, fields, attachments, SPEC status/path, `requestJson`, `issuesJson`, batch arrays or workflow controls beside that path. Platform request headers supply authentication/session context; do not collect or serialize them. `uploadType` stays current-request routing state, never an issue-file/manifest/tool property.

The ticket/source two-argument mode of this same tool is existing-Jira score-only synchronization owned by reviewer. It is not a second call after JSON export and cannot repair a local JSON association. No historical four/five-field binding call follows this route.

## Currentness and file use

Reuse a complete trustworthy upstream file and exact established Workspace-relative path; no duplicate export copy, late text rendering, JSON reserialization or habitual metadata discovery. Validate the selected operation/target and read actual content. A real defect or requested edit returns to preparation, then current confirmation; do not claim an old assessment covers changed input. Changes to JSON, manifest membership/order or participating files invalidate affected bindings.

Use readable Workspace-relative paths returned by the platform. Do not normalize a supplied prefix, invent a session folder, substitute a basename, traverse `..`, or pass an absolute path/URL/URI. Read actual intended uploads too. A SPEC selected only for scoring is not implicitly an upload.

Ordinary supplied additional fields remain in `fields` with their true metadata-defined types. Keep arrays/objects native; serialize only the enclosing file. In Test Case `multiple`, `testDetailsJson` is deliberately a native array despite its name. Preserve unsupported-format failures precisely instead of falling back to the old string-argument API.
