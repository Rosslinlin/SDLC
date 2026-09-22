# Common State And User Info

## Conversation State

Maintain and reuse the current Jira flow state:

- `staffId`
- `almType`
- selected project key / project name
- selected issue type name / issue type id
- SDLC default Epic Link, when explicitly selected and still applicable
- SDLC default Parent Link, when explicitly selected and still applicable
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
- SDLC-only persisted `jira-user-info.md` path/content and confirmation status, only while the validated SDLC route is active

## Lookup Before Asking Basics

Before saying Jira identity, source, or project validation information is missing, perform prerequisite lookup:

1. Current conversation context.
2. User workspace context.

Only while the validated SDLC route is active, additionally inspect user-workspace `jira-user-info.md`. Generic create, update, association, batch and Test Case routes must not read, create, update, mention or depend on that file.

Reusable values include:

- `staffId`
- Jira source / `almType`
- selected project key
- selected project name
- selected issue type name
- selected issue type id
- current-request Epic Link or Parent Link only when explicitly supplied for the active generic operation; generic routes never obtain these values from `jira-user-info.md`

Do not treat `uploadType` as reusable user information. For Test Case export flows, collect the mode with a constrained selection for every export request.

If these values are found in permitted current context and remain relevant to the current request, reuse them. Do not show a popup for a value that is already available and validated, except for the mandatory later-run SDLC defaults confirmation below.

Do not ask for Jira source or project again unless the user explicitly asks to change Jira source or project, or the stored value cannot be validated for the current operation.

## CEAIA SDLC Jira Defaults

This entire section applies only to the validated SDLC route. Other routes skip it completely.

For the validated SDLC route, read the `## CEAIA SDLC Jira Defaults` section of `jira-user-info.md` before opening any Jira destination popup. Reuse current non-secret values for:

- `staffId`
- `almType`
- `projectKey` and project name
- Story issue type name and id
- `epicLink`, when the user previously selected a reusable Epic Link
- `parentLink`, when the user previously selected a reusable Parent Link

Stored values are defaults, not proof that Jira still accepts them. Validate the stored project through `queryJiraProjectsByName`, the stored Story type through `queryJiraIssueTypesByProject`, and writable fields through `queryJiraCreateMetaFields`. When an Epic Link is present, validate its exact issue key/current existence through `getJiraInfos` and confirm it is suitable as the Story's Epic Link; never infer or fabricate it.

For the first SDLC create run, when no reusable SDLC defaults section exists, proactively ask for the Epic Link after source, project and Story type are known. Accept either a real Jira Epic key or an explicit “No Epic Link for these Stories” decision. Validate a supplied key before preview. An explicit no-Epic decision means omit `epicLink` from the Jira payload; never send `"none"`, `"N/A"` or another sentinel as the field value.

For every later SDLC run whose defaults section existed before the current run, resolve any missing Epic Link/no-Epic decision, validate the stored values, then show one focused confirmation popup before preview. Display the exact `almType`, project key/name, Story type, Epic Link or explicit no-Epic decision, and Parent Link. Offer “Use these settings” and “Change settings”. Do not call `exportJiraByDynamicFields` until the user confirms. If the user chooses change, collect only the affected values through their normal focused controls, revalidate them, and show the updated confirmation once. This is destination/default confirmation, not final-write approval; after it succeeds, the SDLC route still proceeds through preview/read-back and direct export without another approval popup.

For an SDLC update, read the current ticket first and preserve its current Epic/Parent relationship unless the reviewed update scope explicitly authorises changing it. A saved create default never overwrites an existing ticket field implicitly.

Do not collect these destination values during the initial Story-generation intake merely because the user also said “push to Jira”. The generation Skill records that final intent; this helper reads or collects destination values only after the reviewed SDLC handoff reaches the Jira stage.

## User Info Persistence After Successful Writes

Only after a successful validated SDLC Jira create/export/update operation, write or update a reusable user info Markdown file in the user workspace. Generic create, update, association, batch and Test Case operations never create or update this file.

The user workspace default file path, unless the user workspace has an established alternative:

- `jira-user-info.md`

Store only reusable, non-secret workflow information:

- `staffId`
- Jira source / `almType`
- selected project key
- selected project name
- selected issue type name
- selected issue type id, when available
- SDLC default Epic Link, when explicitly supplied and successfully used, or the user's explicit no-Epic decision
- SDLC default Parent Link, when explicitly supplied and successfully used
- latest successful SDLC operation type
- latest successful SDLC ticket key or a short list of successful SDLC batch ticket keys, when available
- last successful SDLC export date

Do not store Jira tokens, passwords, raw authentication headers, sensitive payload details, or full issue descriptions.

Keep one canonical `## CEAIA SDLC Jira Defaults` section rather than appending duplicate sections. Store visible Jira values, not raw metadata responses. A suitable shape is:

```markdown
## CEAIA SDLC Jira Defaults

- Staff ID: 12345678
- Jira source: wpb
- Project key: AIWPB
- Project name: AI Wealth Personal Banking
- Issue type: Story
- Issue type ID: 10001
- Epic Link: AIWPB-1234
- Epic Link decision: use-listed-epic
- Parent Link:
- Last successful SDLC export: YYYY-MM-DD
```

For an explicit no-Epic choice, store `Epic Link:` empty and `Epic Link decision: no-epic`. An empty optional line without that decision means no reusable default and must not be converted into a Jira field-clear request.

When the file already exists, update known values instead of creating duplicate sections. Preserve useful previous values when the current operation does not provide replacements.

If persisted values conflict with the current user request, validate the current request and prefer the user's latest explicit intent.

Persist reusable top-level and validated item-level values for SDLC batches when at least one item succeeds. Do not persist new values from failed-only, partial or unknown results.

## De-Duplication Rules

- When a project has already been selected and validated during the current Jira stage, do not call `queryJiraProjectsByName` again unless the user changes source/project keyword or asks to search again. Only the validated SDLC route may load a project from `jira-user-info.md`; validate it exactly once for the new export and use its key as the `projectName` search keyword when no display name is stored.
- When `staffId`, `almType`, and `projectKey` already exist and that project is current-stage validated, skip project collection and go directly to issue type lookup. Persisted but not-yet-validated values do not qualify for this shortcut.
- For Test Case export, when `staffId`, `almType`, and `projectKey` already exist but `uploadType` is missing, skip source/project collection and collect only `uploadType` before preview.
- When issue types for the selected project have already been returned and validated during the current Jira stage, do not call `queryJiraIssueTypesByProject` again unless the user changes project or asks to refresh. Validate a persisted Story type once per new export stage.
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
- `sdlcEpicLink`, only for the validated SDLC create route; accepts a Jira Epic key or an explicit no-Epic selection
- `sdlcDefaultsConfirmation`, only for a later validated SDLC run; accepts “Use these settings” or “Change settings”

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

When the first validated SDLC create run has no reusable Epic Link decision, proactively collect `sdlcEpicLink` with a focused input plus an explicit “No Epic Link for these Stories” choice. Validate a supplied issue key before it can enter the preview. This control is not available to generic or Test Case routes.

When a later validated SDLC run has loaded and revalidated persisted defaults, collect `sdlcDefaultsConfirmation` with a two-choice confirmation popup after displaying all reusable values. This control confirms routing defaults only and cannot be treated as generic-flow confirmation or final SDLC write approval.

When Test Case export `uploadType` is missing, ambiguous, stale, or explicitly changed by the user, collect it with a constrained selection instead of free text. Use the selected value only to choose the final payload shape; do not include `uploadType` in the final `exportJiraByDynamicFields` payload.

| UI option label | Selected `uploadType` mode value | Meaning |
| --- | --- | --- |
| Group into a single ticket | `single` | Create one Jira Test issue containing all selected Test Cases. |
| Create individual tickets | `multiple` | Create separate Jira Test issue payload items for selected Test Cases. |

Show the user-facing labels first. Store only `single` or `multiple` in conversation state for routing and preview.
