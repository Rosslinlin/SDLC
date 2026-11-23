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
| `fields.project.key` | Required nonempty actual project key in every issue file, including updates and Test Cases; equals root `projectKey`. |
| `fields.issuetype.id` | Required nonempty actual issue type ID in every issue file, including updates and Test Cases; agrees with root `issueType` and its verified type binding. |
| `attachments` | For supported ordinary uploads, only authorized, readable Workspace-relative file paths. Test Case source files omit this property. See [attachments](common-attachments.md). |

Every issue JSON MUST retain native `fields.project: {"key": "<actual project key>"}` and `fields.issuetype: {"id": "<actual type ID>"}`. This applies to create, update (even a single-field delta), Story, non-Story, Test Case single/multiple and every manifest child. They are structural routing requirements regardless of metadata `required` flags or whether the project/type changed. Root routing alone does not replace them. On an ordinary update use the actual target's established project/type; retaining them does not authorize a project move or type change. Other business fields may be added/omitted only within the approved scope and applicable requirements. A path-only manifest has no `fields`; validate these requirements on each issue child. Establish actual type identity for [Story versus non-Story handling](flow-batch-export.md); never relabel a Story to use a manifest. Test Case's dedicated shapes still own business content, steps and tables, with these same mandatory routing objects.

For Story create/update with a verified independent AC field, retain the confirmed root `acceptanceCriteria` field-ID string and distinct root `descPath` and `acPath` references to two nonempty `.jirawiki` files in the same session Workspace. Use the [Story create](../templates/story-json-template.json) or [Story update](../templates/story-update-json-template.json) shape. Keep `fields.project`, `fields.issuetype`, `fields.summary` and other approved values, but omit `fields.description` and `fields[acceptanceCriteria]`: the Description and full source criteria live in their respective referenced files, preserving original labels/grouping.

Use the same Story template with root `descPath` as the only Wiki reference when verified field evidence confirms there is no independent AC field or the create qualifies for the capped-query fallback in [Story field assembly](common-field-assembly.md#story-specific-mapping). Save the Description and any supplied AC together in the referenced Jira Wiki file. Omit root `acceptanceCriteria`, root `acPath` and inline `fields.description`. Include both AC keys for the split form and omit both for the single-file form. Empty or null keys or a missing required Wiki file do not qualify for the single-file form.

In both forms, each Wiki path must point to a readable, nonempty `.jirawiki` file in the same session Workspace. Use Workspace-relative paths without traversal or URI syntax. Keep the two split-form paths distinct. Use the saved Wiki bodies as business prose.

An already supplied complete inline Story may remain unchanged only where the actual backend still supports that existing format; do not silently migrate a scored/confirmed file. Other issue types, including Test Cases, retain their existing inline fields and do not acquire Story Wiki paths, a fabricated AC mapping or mandatory SPEC.

For ordinary non-Test preparation, use the [generic issue shape](../templates/generic-issue-template.json) with actual applicable fields. Preserve supplied/approved labels and any actual supported backend marker behavior; do not copy Test Case's mandatory marker into unrelated types as a new gate. Test Case explicitly includes `CEAIA_GEN` in native labels. Never edit a scored file at export merely to add or remove a label.

Templates describe shape only. Replace every instruction/example with verified source or metadata before submission. Preserve supported intentional empty business values; an unspecified update business field is omitted, not cleared. This omission rule never removes `fields.project` or `fields.issuetype`. Their key/ID cannot be absent, null, empty, whitespace, placeholders or fabricated punctuation. Literal punctuation in source content is not a placeholder.

No root `staffId`, credential/session overrides, `conversationId`, `customFields` wrapper or independent BDD object is generated. `fields.reporter` is included only when a submitted Reporter value is actually required after valid update omission/baseline rules or the user explicitly requests it, and its supported native value has been resolved; otherwise omit it. Never assume a staff ID directly equals a Jira Reporter `name` or account ID. Do not copy old `dynamicFieldsJson`, `attachmentsJson`, `issuesJson` or write-tool argument objects into the file as a migration shortcut. Story Wiki paths are content references, not attachments or extra exporter arguments; preserve the existing ordinary `attachments` contract. Supported extensions need actual production schema evidence; do not strip an unknown business field to pass validation.

## A path manifest

Save every non-Story path manifest, including Test Case `multiple` and each non-Story run of a mixed plan, as a real `.json4jira` file, for example `outputs/jira/testcases/export-manifest.json4jira`. The manifest itself and all listed issue children MUST end in the exact `.json4jira` extension; `.json` is not an export-file extension. Packaged template source names such as `path-manifest-template.json` do not determine the runtime filename. The saved manifest has exactly one property:

```json
{
  "story_json_workspace_paths": [
    "outputs/jira/testcases/TC-001.json4jira",
    "outputs/jira/testcases/TC-002.json4jira"
  ]
}
```

Paths are actual unique `.json4jira` child files in approved source order. The manifest is not an inline issue array, contains no defaults/identity/attachments, and cannot nest another manifest. Each child has its own routing/fields. Example paths are not actual inputs. This format also supports ordinary non-Story types; Story children are prohibited even in projects that do not require scoring. [Batch planning](flow-batch-export.md) submits multiple Stories as individual calls and shows any mixed execution plan.

Read the manifest and every child, verifying their actual saved filenames end in `.json4jira` and every child is actually non-Story. Do not score a manifest. The complete selected set must pass applicable preparation and current confirmation checks before submission. Do not silently remove a blocked child. Preserve any [actual upstream assessment evidence](story-assessment-evidence.md) separately; it is not a universal export prerequisite.

## Exact production invocation

Call `java-base-mcp.exportJiraByDynamicFieldsV2` with exactly one direct argument:

```json
{
  "story_json_workspace_path": "outputs/jira/testcases/export-manifest.json4jira"
}
```

For one issue/single-mode aggregate, this value is its actual `.json4jira` issue file path. For an approved batch, it is the actual `.json4jira` manifest file path. Confirm that exact file exists; changing a tool argument's suffix alone does not create it. Keep the tool's singular argument distinct from the manifest's plural property. The production tool name is `exportJiraByDynamicFieldsV2`.

Do not add source, staffId, ticket, fields, attachments, SPEC status/path, `requestJson`, `issuesJson`, batch arrays or workflow controls beside that path. Platform request headers supply authentication/session context; do not collect or serialize them. `uploadType` stays current-request routing state, never an issue-file/manifest/tool property.

The jira_number-only mode of this same tool is existing-Jira score-only synchronization owned by reviewer. It is not a second call after JSON export and cannot repair a local JSON association. No historical four/five-field binding call follows this route.

## Currentness and file use

An unsubmitted upstream issue or manifest saved as `.json` needs a filename correction during preparation. Use a supported rename, or save its unchanged business/manifest content at an actual `.json4jira` path when rename is unavailable; preserve original evidence. Update affected manifest child references and preview/confirmation path bindings, verify the real saved destinations and obtain current confirmation. Any assessment claimed for changed paths must be handled under its actual binding contract. Never rename/substitute a path during submission, and never replay a successful/unknown export merely to correct a historical filename. Profile/configuration files and packaged templates remain separate from submitted files.

Reuse a complete trustworthy upstream file and exact established Workspace-relative path; no duplicate export copy, late text rendering, JSON reserialization or habitual metadata discovery. Validate the selected operation/target and both mandatory routing objects in actual `fields`; a file missing either is incomplete even if its root routing is populated or it was supplied upstream. For a path-based Story also verify the applicable one- or two-file shape above, its actual Wiki files and their current revisions. Missing, identical, unreadable or empty required references return to preparation. Recover actual missing values from verified input/baseline/profile or query only the missing binding; never guess an ID. Correct an unsubmitted file in preparation, then obtain current confirmation and any actually required reassessment. Missing or conflicting values block save-as-export-ready and submission; never silently patch an assessed/confirmed file during export. Changes to JSON, any referenced Wiki file, manifest membership/order or other participating files invalidate affected bindings.

Use readable Workspace-relative paths returned by the platform. Do not normalize a supplied prefix, invent a session folder, substitute a basename, traverse `..`, or pass an absolute path/URL/URI. Read actual intended uploads too. A SPEC selected only for scoring is not implicitly an upload.

Ordinary supplied additional fields remain in `fields` with their true metadata-defined types. Keep arrays/objects native; serialize only the enclosing file. In Test Case `multiple`, `testDetailsJson` is deliberately a native array despite its name. Preserve unsupported-format failures precisely instead of falling back to the old string-argument API.
