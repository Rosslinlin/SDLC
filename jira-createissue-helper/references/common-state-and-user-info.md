# Common State And User Info

## Conversation State

Maintain and reuse the current Jira flow state:

- `staffId`
- `almType`
- selected project key / project name
- selected issue type name / issue type id
- `issueIdOrKey`, for existing ticket updates
- association source / target issue key / `linkedIssueKeys` / `linkType`
- latest `queryJiraCreateMetaFields` response
- generated required field values
- generated optional field values
- generated `dynamicFieldsJson`
- generated Jira issue summary text
- generated `jira-preview.md` path and content
- selected Test Case source artifact or structured source identifier
- selected Test Case IDs and source row order
- selected Test Case upload type: `single` or `multiple`
- Test Case ID to batch item index mapping
- latest Test Case source-to-payload integrity status
- persisted user info Markdown file path and content

## Lookup Before Asking Basics

Before saying Jira identity, source, or project validation information is missing, perform prerequisite lookup:

1. Current conversation context.
2. User workspace context.
3. User workspace `jira-user-info.md`, when it exists.

Reusable values include:

- `staffId`
- Jira source / `almType`
- selected project key
- selected project name
- selected issue type name
- selected issue type id
- selected Test Case upload type, only for Test Case export flows

If these values are found and remain relevant to the current request, reuse them. Do not show a popup for a value that is already available and validated.

Do not ask for Jira source or project again unless the user explicitly asks to change Jira source or project, or the stored value cannot be validated for the current operation.

## User Info Persistence After Successful Writes

After a successful Jira create/export/update operation, write or update a reusable user info Markdown file in the user workspace.

The user workspace default file path, unless the user workspace has an established alternative:

- `jira-user-info.md`

Store only reusable, non-secret workflow information:

- `staffId`
- Jira source / `almType`
- selected project key
- selected project name
- selected issue type name
- selected issue type id, when available
- latest successful operation type, such as create, update, batch create, batch update, or association update
- latest successful ticket key or a short list of successful batch ticket keys, when available
- last updated date

Do not store Jira tokens, passwords, raw authentication headers, sensitive payload details, or full issue descriptions.

When the file already exists, update known values instead of creating duplicate sections. Preserve useful previous values when the current operation does not provide replacements.

If persisted values conflict with the current user request, validate the current request and prefer the user's latest explicit intent.

Persist reusable top-level and validated item-level values for batch operations when at least one item succeeds. Do not persist values from failed-only batch results unless they were already validated earlier and the user explicitly asks to save them.

## De-Duplication Rules

- When a project has already been selected, do not call `queryJiraProjectsByName` again unless the user changes source/project keyword or asks to search again.
- When `staffId`, `almType`, and `projectKey` already exist, skip project collection and go directly to issue type lookup.
- For Test Case export, when `staffId`, `almType`, and `projectKey` already exist but `uploadType` is missing, skip source/project collection and collect only `uploadType` before preview.
- When issue types for the selected project already exist, do not call `queryJiraIssueTypesByProject` again unless the user changes project or asks to refresh.
- When metadata for the same project + issue type already exists, do not call `queryJiraCreateMetaFields` again unless the user changes project/issue type or asks to refresh.
- When metadata has already been explained, do not repeat the full explanation; assemble values directly and generate the preview.
- Do not reopen popups for already collected or generated field values unless the user asks to modify them.

## Popup Boundary

This section is the controlling popup / selection UI contract for Jira input collection.

For values listed under Allowed popup/selection inputs, if the value is missing, ambiguous, stale, or explicitly changed by the user, present a popup / selection UI, not normal chat text. Until the user provides the value or makes a selection, do not continue to downstream Jira lookup, metadata retrieval, preview generation, or export.

Do not use a popup for values outside the allowed list. Handle blockers for non-popup fields in normal chat according to the relevant flow.

Allowed popup/selection inputs:

- `staffId`
- Jira source
- `projectName` search keyword
- `projectKey` selected from `queryJiraProjectsByName` results
- `issueType` selected from `queryJiraIssueTypesByProject` results
- `uploadType`, only for Test Case export flows

Normal chat must not be used as a substitute for popup collection of the allowed inputs above. If the runtime cannot render a required popup / selection UI, state that the required input UI is unavailable and stop before continuing the Jira flow.

Do not collect these through popup:

- `summary`
- `description`
- metadata-derived required fields
- metadata-derived optional fields
- labels
- assignee
- priority
- components
- custom fields

If a field popup is used, it must satisfy:

- one tab per field
- one independent control per field
- one control must not mix multiple Jira fields
- no textarea, JSON box, CSV box, or natural-language prompt to collect multiple fields at once

When `staffId` or Jira source is missing, use a fixed popup with independent controls:

- `staffId`: required staff id input.
- source: use available source choices; final customer input shows Other supported sources: `FCR`, `GO`, `DATA`.

Do not dynamically add or reorder source options.

When `projectName` search keyword is missing, stale, or explicitly changed by the user, collect it with a focused popup input before calling `queryJiraProjectsByName`.

When `projectKey` selection is needed from `queryJiraProjectsByName` results, collect it with a dynamic project popup generated from the actual returned results. Each option must preserve at least the returned project key and project name.

When `issueType` selection is needed from `queryJiraIssueTypesByProject` results, collect it with a dynamic issue-type popup generated from the actual returned results. Each option must preserve the returned issue type name and id when available.

When Test Case export `uploadType` is missing, ambiguous, stale, or explicitly changed by the user, collect it with a constrained selection instead of free text. Use the selected value only to choose the final payload shape; do not include `uploadType` in the final `exportJiraByDynamicFields` payload.

| UI option label | Selected `uploadType` mode value | Meaning |
| --- | --- | --- |
| Group into a single ticket | `single` | Create one Jira Test issue containing all selected Test Cases. |
| Create individual tickets | `multiple` | Create separate Jira Test issue payload items for selected Test Cases. |

Show the user-facing labels first. Store only `single` or `multiple` in conversation state for routing and preview.
