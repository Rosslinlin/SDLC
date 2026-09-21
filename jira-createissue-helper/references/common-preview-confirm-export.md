# Common Preview Confirm Export

## Preview Before Confirmation

After metadata is retrieved and fields are assembled, or after an update/association payload draft is completed, generate the preview before showing a generic-flow confirmation popup or submitting through the validated SDLC route.

The preview stage must include:

- conversational Jira summary
- workspace Markdown preview file: `jira-preview.md`
- the complete payload as a valid JSON code block
- required-field status
- optional fields
- labels, including `CEAIA_GEN` when labels are included or changed

Overwrite the same `jira-preview.md` whenever the preview changes. Do not create scattered preview Markdown files. Do not replace the payload JSON block with a separate payload file.

## Empty-Value Preflight

Before preview and again immediately before `exportJiraByDynamicFields`:

1. Apply `common-field-assembly.md`'s Mandatory Empty-Value Contract to the actual tool arguments, including direct inputs, dynamic fields, parsed active JSON payloads, and effective batch items after inheritance.
2. Verify that unused included optional strings use `""` where supported, empty arrays/objects retain their declared types, and no generated `"."` or other missing-value placeholder remains. Omit unused fields excluded by a specialised payload shape; preserve literal periods in evidence.
3. Distinguish an unused optional JSON-string parameter with an accepted `""` sentinel from active operation data. Do not parse the inactive sentinel as JSON or activate an operation merely because its key exists. Active JSON payloads must parse and satisfy the selected flow's schema; required data cannot use an empty sentinel or collection.
4. Validate required values and ensure unspecified update inputs have not become changes or field clears through empty values or inherited defaults.
5. Show intentional empty values, omitted unused parameters, and explicitly requested field clears in the preview. Submit exactly the normalized values that were approved; do not insert placeholders afterward.

Block submission on any failed check. In a generic route, if a correction changes a confirmed payload, regenerate `jira-preview.md` and obtain fresh explicit confirmation using that route's confirmation mechanism. For SDLC, `sdlc-gateway.md` controls the stricter preview and direct-export preflight; regenerate the preview and repeat final read-back instead of invoking an approval tool.

## Attachment Payload Preflight in the Preview Gate

Apply this section when attachment operations are planned or required, or when the final export payload contains a non-empty `attachmentsJson`. An accepted `attachmentsJson: ""` with no planned or required operations is an inactive sentinel, not a plan to parse. It must never bypass a required attachment.

Before showing a confirmation popup, complete and show all of the following checks:

1. State every planned `add`, `replace`, and `delete` operation separately.
2. For every upload (`add` and the upload component of `replace`), show the exact Workspace-relative `relativePath` and `fileName`.
3. Confirm that every upload file was successfully read from the applicable Workspace scope immediately before payload assembly, and that the returned Workspace-relative path exactly equals the planned `relativePath`.
4. Confirm that each returned filename equals the planned `fileName` and the approved/reviewed attachment mapping.
5. Confirm that the complete outer export payload is valid JSON.
6. Build the attachment plan as an object internally, then serialize it exactly once into `attachmentsJson`.
7. Confirm that the outer `attachmentsJson` value is a JSON string, not a JSON object, array, or null.
8. Parse the `attachmentsJson` string once and confirm that it recreates the same authorised operations, paths, and filenames displayed in the preview.
9. Confirm that no attachment payload-affecting change occurs after the preview binding. In generic routes, any such change requires fresh confirmation. In the validated SDLC route, it requires a regenerated preview and a repeated final read-back/preflight before direct export.

If any attachment preflight check fails, do not request confirmation and do not call `exportJiraByDynamicFields`. Report the exact failed Workspace-relative path or payload field, preserve the returned transport error where safe to display, and request correction, a separately previewed retry, deferment, or an explicit stop decision. Do not guess a nearby path, substitute a similarly named file, or automatically retry.

## `jira-preview.md` Content Requirements

For new Jira issues, include:

- project key / project name
- issue type
- generated `summary`
- generated `description`
- acceptance criteria, when applicable
- required-field status
- optional fields
- labels
- attachment preflight status, when `attachmentsJson` is used
- complete `exportJiraByDynamicFields` payload JSON block

For Jira updates, include:

- current Jira information summary
- proposed changes
- fields to update
- labels
- attachment preflight status, when `attachmentsJson` is used
- complete update payload JSON block

For association updates, include:

- source Jira
- target Jira
- normalized link type
- source metadata validation summary
- complete association payload JSON block

For batch exports, include:

- batch operation type
- top-level defaults
- item count
- per-item summary, target project/type or `issueIdOrKey`, required-field status, labels, Test Details summary, and attachment operation summary
- per-item attachment preflight status when attachments are used
- complete batch `exportJiraByDynamicFields` payload with `issues` or `issuesJson` as a valid JSON code block
- a clear note that the export tool will be called once for the whole batch after confirmation

For Test Case source exports, also include:

- source artifact or structured source identifier
- selected Test Case count and selected Test Case IDs
- omitted Test Case IDs and technical reasons, if any
- selected export behavior
- selected `uploadType` mode: `single` or `multiple`, plus the user-facing label selected by the user; this is previewed as routing state, not as a final tool payload field
- source-to-payload integrity status for execution fields
- line-break validation status confirming Jira-visible Test Case fields keep required breaks as newline characters and contain no literal or escaped HTML break tags
- for `multiple`, Test Case ID to payload item mapping and confirmation that item `description` and `testDetailsJson` values preserve required line breaks
- for `single`, Test Case ID to aggregate table row mapping and confirmation that `description` contains only the clean Test Case table with no Markdown separator row, extra text, or literal line-break tags

## Generic-flow confirmation

For generic create, update, association, batch and Test Case routes, call `exportJiraByDynamicFields` only after explicit user confirmation.

For a `ceaia-sdlc-validated` handoff, `sdlc-gateway.md` is controlling. No approval tool or final confirmation popup exists in this route. Once the complete preview, payload read-back and attachment preflight pass, call `exportJiraByDynamicFields` directly.

User phrases such as `create`, `export`, or `go ahead` only allow preview generation unless they are clearly responding to an already shown confirmation request. They do not bypass confirmation.

If confirmation is unclear, do not call `exportJiraByDynamicFields`.

The generated-content confirmation popup for new Jira issues must show:

- generated `summary`
- generated `description`
- required-field status list
- key optional fields
- payload summary or a reference to the `jira-preview.md` path
- required confirmation control asking whether the content meets the user's expectation

Recommended confirmation text for a new Jira issue:

> Please confirm whether the generated Jira content meets your expectation. I will call exportJiraByDynamicFields to create the Jira issue only after you confirm.

Recommended confirmation text for a Jira update:

> Please confirm whether the current Jira information summary and proposed changes are correct. I will call exportJiraByDynamicFields to update this Jira issue only after you confirm.

Bind generic-route confirmation to the complete preview payload. Regenerate `jira-preview.md` and obtain fresh explicit confirmation after any generic payload-affecting change. For SDLC, the same kinds of changes invalidate the preview binding and require a new preview plus final read-back/preflight, without a confirmation tool.

## Submission Parameters

The lists below describe active parameters. Unused optional inputs follow the Mandatory Empty-Value Contract: omit them or pass accepted typed empty values, never `"."`. An accepted `attachmentsJson: ""` represents no attachment operation only; it cannot substitute for a validated required plan.

New issue parameters may include:

- `staffId`
- `almType`
- `projectKey`
- `summary`
- `issueType`
- `description`
- `projectName`
- `epicName`
- `epicLink`
- `parentLink`
- `dynamicFieldsJson`
- `attachmentsJson`, only as a validated serialized JSON string when attachments are authorised

Update issue parameters may include:

- `staffId`
- `almType`
- `issueIdOrKey`
- `summary`
- `description`
- `dynamicFieldsJson`
- `testDetailsJson`
- `epicLink`
- `parentLink`
- `attachmentsJson`, only as a validated serialized JSON string when attachments are authorised

Association update parameters may include:

- `staffId`
- `almType`
- `issueIdOrKey`
- `linkedIssueKeys`
- `linkType`
- `dynamicFieldsJson`, only for other validated fields such as labels

`linkedIssueKeys` and `linkType` must remain top-level export parameters and must not be nested inside `dynamicFieldsJson`.

Batch parameters may include:

- `staffId`
- `almType`
- top-level defaults such as `projectKey`, `issueType`, `description`, `dynamicFieldsJson`, and `conversationId`
- `issues`
- `issuesJson`
- `attachmentsJson`, only as a validated serialized JSON string when attachments are authorised

For Test Case export, `uploadType` must be collected before preview, but it is not submitted as a final `exportJiraByDynamicFields` parameter. Use it only to choose between the `multiple` payload shape with `issuesJson` and the `single` payload shape with top-level `summary`, `description`, and `dynamicFieldsJson`. In `single` mode, the submitted `dynamicFieldsJson` object must contain `labels` with `CEAIA_GEN`.

For Test Case export, the submitted payload must be self-contained. Preview text may identify the source artifact, but the final payload JSON block must not place source-file references, preview-file references, attachment references, generated-document references, or other file pointers inside Jira-visible fields instead of the complete Test Case content.

## Result Handling

On success, show:

- `ticketKey`
- `ticketUrl`

For batch responses, show per-item `index`, `status`, `ticketKey`, `ticketUrl`, and sanitized `error` when present, plus `successCount` and `failureCount` if returned.

For Test Case batch responses, preserve the selected Test Case to Jira issue mapping. For `uploadType: "multiple"`, show one result line per selected Test Case when a returned item has a Test Case ID and a Jira key or URL. For `uploadType: "single"`, show the selected Test Case ID list or aggregate identifier with the single returned Jira issue key or URL. Do not collapse sequential Jira keys into ranges or shorthand.

After a successful create/export/update response, write or update the user info Markdown file described in `common-state-and-user-info.md`. The persistence must happen after the Jira write response succeeds, not before generic confirmation or before SDLC submission.

For attachment operations, report the Jira issue result and attachment result independently. Include the confirmed Workspace-relative path and filename used for each upload. Preserve the exact returned attachment transport error where safe to display.

On failure:

- Show the sanitized Jira/tool error.
- Ask whether the user wants to revise inputs or fields.
- Do not resubmit the same payload unless the user explicitly requests a retry.
- Do not update the user info Markdown file for a failed Jira write unless the values were already validated and the user explicitly asks to save them.
