# Flow SDLC Validated Handoff Export

Flow version: 1.1.0

Applies to: reviewed CEAIA SDLC Story/SPEC handoffs

Required companion: `sdlc-handoff-validation.md`

## 1. Scope and priority

Use this flow for an explicit reviewed CEAIA SDLC handoff that creates or updates Jira Stories and manages their approved attachments. This flow supports one item or multiple independent items.

This flow has priority over generic create, update and batch flows. It does not replace the dedicated Test Case source-export flow. If the SDLC validation gate fails, report the exact blocker and stop; do not regenerate content or route the request through a generic flow.

## 2. Supported operations

| Handoff operation | Jira action | Attachment support |
| --- | --- | --- |
| One `create` item | Create one new Jira Story | Add exactly one reviewed SPEC after successful creation. |
| Multiple `create` items | Create multiple independent Jira Stories | Add each item's one reviewed SPEC after that item succeeds. |
| One `update` item | Update one existing Jira Story | Add, replace or delete attachments only as authorised. |
| Multiple `update` items | Update multiple existing Jira Stories independently | Perform each item's authorised attachment operations after its own update succeeds. |
| Mixed create/update items | Integrated SDLC batch only when explicitly requested | Apply each item's applicable rules; otherwise keep operations separate. |

## 3. Required modules

Read and apply:

- `common-tools-and-inputs.md`
- `common-state-and-user-info.md`
- `common-project-issue-type-validation.md` for creates
- `common-field-assembly.md`
- `common-preview-confirm-export.md`
- `common-guardrails.md`
- `common-attachments.md`
- `sdlc-handoff-validation.md`

## 4. End-to-end procedure

### Stage A — Identify and validate the SDLC handoff

1. Confirm explicit SDLC handoff intent and identify all selected items.
2. Classify each item as `create` or `update`; preserve item order and independent identity.
3. Inspect conversation context, user workspace context and `jira-user-info.md` before asking for reusable staff ID or source.
4. Validate each item using `sdlc-handoff-validation.md` before Jira payload assembly.
5. If any selected item fails validation, show the item-specific blocker. Do not construct a partial integrated batch payload unless the user explicitly removes the blocked item(s).

### Stage B — Resolve project, issue type and current issues

#### Create items

1. Reuse a valid project only when it is unchanged and compatible with the handoff.
2. Otherwise validate project using `queryJiraProjectsByName`; never invent a project key.
3. Query issue types using `queryJiraIssueTypesByProject` and select the Jira-returned Story type required by the handoff.
4. Retrieve create metadata using `queryJiraCreateMetaFields`.

#### Update items

1. Use the exact supplied `ticketKey` as `issueIdOrKey`.
2. Call `getJiraInfos` before generating any payload.
3. Verify the returned ticket identifier exactly matches the requested `ticketKey`.
4. Obtain project and issue type from the returned current issue. Do not perform project search or issue-type discovery for an update.
5. Retrieve current metadata using `queryJiraCreateMetaFields` and validate the proposed changed fields.

### Stage C — Build Story field mappings

For every item:

1. Map reviewed Story title to Summary under the summary rule in `sdlc-handoff-validation.md`.
2. Map the exact reviewed `STORY.md` content to Description. Do not rewrite it.
3. Extract the approved `AC-###` Given/When/Then content from Story and map it to the metadata-confirmed writable Jira Acceptance Criteria field.
4. Process all other required metadata fields using this strict order: current preserved value for updates; visible metadata default; safe evidence-based allowed option; focused user question.
5. Add optional fields only where user-provided, safely inferred, useful by default or directly relevant to the reviewed handoff.
6. Keep direct fields out of `dynamicFieldsJson`; place only other validated custom/system fields in `dynamicFieldsJson`.
7. De-duplicate labels and include `CEAIA_GEN` whenever labels are included or changed.

### Stage D — Build create, update or batch payloads

#### One create

Build a create payload with validated project key, Story issue type, reviewed Summary, reviewed Description, Acceptance Criteria field mapping, metadata-required fields and label rules.

#### One update

Build an update payload containing only:

- Summary, only when an explicit reviewed/evidence-supported replacement is authorised;
- reviewed Description replacement;
- validated Acceptance Criteria field replacement;
- metadata-required changed values; and
- label changes when applicable.

Do not update unsupported fields. Preserve current values for all other fields.

#### Multiple creates or updates

Use one `issues` or `issuesJson` list and one export call by default. Every item must retain its own project/type where required, item-specific Description, Acceptance Criteria mapping, required-field values and attachment plan.

For mixed create/update work, use one integrated payload only when the user explicitly requests a mixed batch. Otherwise produce separate operation previews to avoid accidental combined writes.

### Stage E — Attachment plan and preflight

Create the attachment plan separately from dynamic fields.

1. For each create item, plan one `add` operation for its exact reviewed SPEC path.
2. For each update item, validate each planned `add`, `replace` or `delete` operation against its update manifest, attachment mapping and user authorisation.
3. For every `add` and replacement upload, perform the mandatory Workspace path preflight in `common-attachments.md` and Section 6.3 of `sdlc-handoff-validation.md` before preview.
4. Use only the exact confirmed Workspace-relative path and matching filename returned by the preflight in the attachment plan.
5. Build the attachment plan as an object, then serialize it exactly once into `attachmentsJson`. Validate that `attachmentsJson` is a string and parses once to the same authorised plan.
6. Keep attachment operations per item in a batch.
7. Execute attachment operations only after the corresponding Jira issue create/update has succeeded.
8. On replace, upload the new SPEC before deleting the old attachment.
9. Report issue-write and attachment outcomes separately; a partial attachment failure does not conceal a successful Jira field update.

If the file preflight or serialization validation fails, do not create a preview, request confirmation, call export, retry automatically, or substitute another path. Report the exact blocker and use the approved recovery interaction.

### Stage F — Preview and explicit confirmation

Create or overwrite the sole preview file: `jira-preview.md`. It must include:

- SDLC flow version and handoff type;
- operation and item count;
- source project/issue type for every item;
- exact ticket key for update items;
- artefact validation status: Story, Test Case audit-only status, one SPEC, review PASS, `jiraReady`, title validation and update readiness where applicable;
- field provenance for every required field;
- a clear statement that reviewed Story content is used as Description and approved ACs are mapped to the Acceptance Criteria field;
- a clear statement that `TEST_CASE.md`, planning, scoring, review reports and manifests are not exported;
- attachment add/replace/delete plans with confirmed paths or filenames as appropriate;
- attachment preflight status, including confirmation that `attachmentsJson` is a serialized JSON string and parses to the displayed plan;
- full valid JSON payload; and
- for batch, the one-call export statement and per-item identifiers.

Request explicit confirmation. Ambiguous language does not authorise export. Regenerate the preview and obtain fresh confirmation after any payload-affecting change, including an attachment path, filename, operation, Workspace artefact, or serialized `attachmentsJson` value.

### Stage G — Export and report

1. After explicit confirmation only, call `exportJiraByDynamicFields` once for the planned single or batch Issue write.
2. Do not automatically retry a failed call.
3. For each successful item, repeat the path/filename consistency check if the Workspace artefact or plan changed after preview, then run its validated attachment operations after the Issue write result is known.
4. On success, update `jira-user-info.md` with allowed non-secret reusable values only.
5. Report each item separately: handoff item identifier, operation, Jira key/URL, Issue-write status, attachment operation status and sanitised errors.
6. For uploads, report the confirmed Workspace-relative path and filename used in the final plan.
7. Never compress returned Jira keys into ranges.

## 5. Required-field user interaction

Ask the user only when a required Jira field cannot be resolved by current values, a visible metadata default, or direct evidence from the reviewed SDLC handoff.

For each question:

- ask one required field at a time;
- identify the Jira field name and whether it is required;
- show valid visible non-disabled metadata options when options exist;
- explain that Summary, Description and Acceptance Criteria are derived from the reviewed handoff and are not requested as manual fields; and
- after the answer, revalidate against metadata before continuing.

For an attachment preflight or serialization blocker, state the exact failed Workspace-relative path or payload field, why it blocks export, and whether the user needs to provide a corrected file/path, approve a separately previewed retry, defer, or stop.

## 6. Result and failure conditions

Block before preview/export if the SDLC validation reference identifies a failed gate, if Jira metadata does not expose a writable Acceptance Criteria field, if a required field lacks a safe value, if an attachment operation is ambiguous or unauthorised, if an upload path is not readable in the applicable Workspace scope, or if `attachmentsJson` is not the validated serialized attachment plan.

On a Jira error, return a sanitised error. Do not create replacement content, change the approved Story/SPEC, retry automatically, or fall back to generic flows.
