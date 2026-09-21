# Common Tools And Inputs

## Scope

Use this module for Jira MCP tool responsibilities, required base inputs, and Jira source mapping. Business flows must load this module before calling Jira tools.

## Supported Jira Tasks

- Create Jira issues, including Story, Task, Bug, Epic, Sub-task, and similar types.
- Create or update Jira issues and Test Cases in batch.
- Export Test Cases from a user-selected source artifact, pasted table, spreadsheet-like content, or other structured testcase source.
- Generate Jira user stories from business requests.
- Generate `dynamicFieldsJson` from dynamic Jira metadata.
- Fuzzy-search Jira projects and let the user select a returned project.
- Query available issue types for a selected project and select from the returned list.
- Query `queryJiraCreateMetaFields`, explain required/optional fields, and automatically assemble payloads.
- Update an existing Jira issue.
- Add an association between existing Jira issues, for example `Relates`.
- Add, delete, or replace attachments after create/update when supported by the export tool.

Do not start the full creation flow only to explain static Jira fields unless the user explicitly wants to create, export, update, or generate a payload afterward.

## Base Inputs

All Jira write operations require at least:

- `staffId`: Used to read the user's saved Jira token.
- `almType`: Jira source. Backend values are limited to `wpb`, `alm`, `fcr`, `go`, and `data`.
- `CEAIA_GEN`: The default label automatically added to every create or update payload when labels are included or changed.

## Source Mapping

- `WPB` -> `wpb`
- `ALM` -> `alm`
- `FCR` -> `fcr`
- `GO` -> `go`
- `DATA` -> `data`

Do not allow arbitrary unsupported source names.

Required base inputs must contain real validated values, not empties or fabricated `"."` placeholders. For unused optional inputs, follow the Mandatory Empty-Value Contract in `common-field-assembly.md`: use accepted typed empty values or omit the input, without bypassing required operation data or changing unspecified update fields.

## New Issue Inputs

New issue creation also requires:

- `projectName`: Fuzzy search keyword for `queryJiraProjectsByName`.
- `projectKey`: Must come from a project query result unless the user explicitly provides a known exact key and accepts Jira validation risk.
- `issueType` or `issueTypeId`: Must come from `queryJiraIssueTypesByProject` unless the user explicitly provides a known type and accepts Jira validation risk.
- Generated `summary`, generated `description`, and metadata-derived required fields.

## Existing Issue Update Inputs

Existing issue updates also require:

- `issueIdOrKey`: Extracted from a Jira key or a `/browse/<KEY>` link.
- Current Jira information: Retrieved through `java-base-mcp.getJiraInfos`.
- Current project key and issue type: Used to call `java-base-mcp.queryJiraCreateMetaFields` and confirm the field rules for the requested update.
- Confirmed update metadata: Every field in the proposed update must conform to the returned field id, schema, operations, allowed values, and required/value rules.
- Explicit user confirmation for the current-information summary and proposed changes.

## Batch Inputs

Batch create/update also requires:

- `issues` or `issuesJson`: A single batch item list used for one `exportJiraByDynamicFields` call.
- Per-item `summary`, `issueIdOrKey`, `testDetailsJson`, dynamic fields, or attachments as required by the operation.
- One integrated batch preview before the entire batch. Generic batch routes also require confirmation; the validated SDLC route follows its gateway's direct-export rule.

For Test Case source export with `uploadType` mode `single`, do not use `issues` or `issuesJson`; assemble one aggregate Test issue with top-level `summary`, clean table-only `description`, and `dynamicFieldsJson.labels` containing `CEAIA_GEN`.

## Test Case Source Inputs

Test Case source export also requires:

- A user-selected source artifact or structured testcase source for the current export.
- `uploadType`: User-selected Test Case export behavior used to choose the final payload shape. Valid mode values are `single` and `multiple`; do not include this control value in the final `exportJiraByDynamicFields` payload.
- A parsed candidate Test Case list.
- A confirmed selected Test Case list when exporting a subset or when IDs are ambiguous.
- Source-to-payload integrity checks for execution fields before preview and before export.

## Tool Responsibilities

- `queryJiraProjectsByName`: Pass exactly the required `staffId`, `almType`, and `projectName` search keyword.
- `queryJiraIssueTypesByProject`: Pass exactly the required `staffId`, `almType`, and selected `projectKey`.
- `queryJiraCreateMetaFields`: Pass `staffId`, `almType`, `projectKey`, plus at least one of `issueType` or `issueTypeId`; retrieve create-field metadata and drive automatic field assembly. For Jira updates, confirm the target project's field rules before export.
- `getJiraInfos`: Read existing Jira information, especially for update and association flows.
- `exportJiraByDynamicFields`: Pass tool arguments directly, without a `requestJson` wrapper. Generic routes call it after their existing confirmation step. A validated SDLC route calls it directly after the gateway's final preview/read-back/preflight, with no approval-tool call.

`handoffType` is workflow metadata, not a Jira MCP parameter. Never send it to any tool or place it in a serialized Jira field payload.

Association-specific parameters such as `linkedIssueKeys` and `linkType` must be passed directly to `exportJiraByDynamicFields`; they are not dynamic Jira fields and must never be nested in `dynamicFieldsJson`.
