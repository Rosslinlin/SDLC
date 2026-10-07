# Flow SDLC Validated Handoff Export

Flow version: 1.5.0

Applies to: reviewed CEAIA SDLC Story/SPEC handoffs

Required controlling gateway: `sdlc-gateway.md`

Required companion: `sdlc-handoff-validation.md`

## 1. Scope and priority

Use this flow for a reviewed CEAIA SDLC handoff that creates or updates Jira Stories and manages their approved attachments. In the bundled SDLC workflow the handoff is automatic after review PASS even when the opening user input did not mention Jira export. This flow supports one item or multiple independent items.

This flow has priority over generic create, update and batch flows. It does not replace the dedicated Test Case source-export flow. If the SDLC validation gate fails, report the exact blocker and stop; do not regenerate content or route the request through a generic flow.

Execute `sdlc-gateway.md` before this procedure. The gateway controls SDLC eligibility, Jira-wiki Description rendering, final export binding and write recovery. Common modules remain reusable mechanics and do not override the gateway.

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

- `sdlc-gateway.md`
- `common-tools-and-inputs.md`
- `common-state-and-user-info.md`
- `common-project-issue-type-validation.md` for creates
- `common-field-assembly.md`
- `common-jira-text-rendering.md`
- `common-preview-confirm-export.md`
- `common-guardrails.md`
- `common-attachments.md`
- `sdlc-handoff-validation.md`

## 4. End-to-end procedure

### Stage A — Identify and validate the SDLC handoff

1. Confirm the internal validated SDLC handoff and identify all selected items. Do not require export wording or a second continue request; an explicit preparation-only/no-Jira instruction is the only normal opt-out.
2. Classify each item as `create` or `update`; preserve item order and independent identity.
3. Inspect conversation context and user workspace context. Because this is the validated SDLC route, also inspect `jira-user-info.md` before asking for reusable staff ID, source, project, Story issue type, Epic Link or Parent Link. No other helper route may read this file.
4. Validate artifact/review gates using `sdlc-handoff-validation.md`. Destination fields marked “At export” are resolved in Stage B from user-workspace defaults or focused final-stage selection; their absence must not send the workflow back to initial generation intake.
5. If any selected item fails validation, show the item-specific blocker. Do not construct a partial integrated batch payload unless the user explicitly removes the blocked item(s).

### Stage B — Resolve project, issue type and current issues

#### Create items

1. Reuse a valid project only when it is unchanged and compatible with the handoff.
2. Otherwise validate project using `queryJiraProjectsByName` with direct `staffId`, `almType`, and `projectName` arguments; never invent a project key.
3. Query issue types using `queryJiraIssueTypesByProject` with direct `staffId`, `almType`, and `projectKey` arguments, then select or validate the Jira-returned Story type required by the handoff.
4. Retrieve create metadata using `queryJiraCreateMetaFields` with direct `staffId`, `almType`, `projectKey`, plus `issueType` or `issueTypeId`.
5. If no SDLC defaults section existed before this run, proactively collect and validate an Epic Link or the explicit no-Epic decision. If a defaults section existed, revalidate its current values and obtain the one SDLC defaults confirmation defined in `common-state-and-user-info.md`. Do this before payload preview, not during generation intake.

#### Update items

1. Use the exact supplied `ticketKey` as `issueIdOrKey`.
2. Call `getJiraInfos` before generating any payload. Retrieve the current Description and Acceptance Criteria separately; the AC value is needed to decide whether a reviewed AC replacement is required.
3. Verify the returned ticket identifier exactly matches the requested `ticketKey`.
4. Obtain project and issue type from the returned current issue. Do not perform project search or issue-type discovery for an update.
5. Retrieve current metadata using `queryJiraCreateMetaFields` and validate the proposed changed fields.
6. If an SDLC defaults section existed before this run, revalidate and confirm the routing values relevant to this update. Preserve the ticket's current Epic/Parent fields unless the reviewed update explicitly authorises changing them; never apply the saved create default implicitly.

### Stage C — Build Story field mappings

For every item:

1. Map reviewed Story title to Summary under the summary rule in `sdlc-handoff-validation.md`.
2. Apply `sdlc-gateway.md` Section 4's single partition to the exact reviewed `STORY.md`. Render every non-AC section in its original order to Jira wiki and map only that result to Description. Do not edit the Workspace Story or include its `## Acceptance Criteria` section in Description.
3. Render the extracted AC section body separately and map it to the metadata-confirmed writable Jira Acceptance Criteria field. Preserve each `AC-###` declaration and its Given/When/Then wording exactly once; verify that neither an AC declaration nor its body was also sent in Description. For an update, omit an AC replacement only after the retrieved current field is verified to match the reviewed value.
4. Process all other required metadata fields using this strict order: current preserved value for updates; visible metadata default; safe evidence-based allowed option; focused user question.
5. Add optional fields only where user-provided, safely inferred, useful by default or directly relevant to the reviewed handoff.
6. Keep direct fields out of `dynamicFieldsJson`; place only other validated custom/system fields in `dynamicFieldsJson`.
7. De-duplicate labels and include `CEAIA_GEN` whenever labels are included or changed.

### Stage D — Build create, update or batch payloads

#### One create

Build a create payload with validated project key, Story issue type, reviewed Summary, partitioned non-AC Description, separate Acceptance Criteria field mapping, metadata-required fields and label rules.

#### One update

Build an update payload containing only:

- Summary, only when an explicit reviewed/evidence-supported replacement is authorised;
- reviewed non-AC Description replacement;
- validated Acceptance Criteria field replacement from the same reviewed Story when its current value differs; leave a verified matching current field untouched;
- metadata-required changed values; and
- label changes when applicable.

Do not update unsupported fields. Preserve current values for all other fields.

#### Multiple creates or updates

Use one `issuesJson` JSON-array string and one export call by default. Partition each item's own reviewed Story independently. Every item must retain its own project/type where required, non-AC Description, separately mapped Acceptance Criteria in that item's `dynamicFieldsJson` when creating or changing AC, required-field values and attachment plan. A verified matching current AC field may remain untouched on an update. Do not inherit another item's AC through top-level defaults. Do not send an `issues` argument because it is not exposed by the active export tool.

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

If the file preflight or serialization validation fails, do not create a preview, call export, retry automatically, or substitute another path. Report the exact blocker and use the supported recovery interaction.

### Stage F — Final preview and direct-export preflight

Create or overwrite the sole preview file: `jira-preview.md`. It must include:

- SDLC flow version and internal handoff type, clearly marked “routing only; omitted from Jira tool arguments”;
- operation and item count;
- source project/issue type for every item;
- exact ticket key for update items;
- artefact validation status: Story, Test Case audit-only status, one SPEC, review PASS, `jiraReady`, title validation and update readiness where applicable;
- field provenance for every required field;
- SDLC defaults status: first-run Epic Link/no-Epic decision or later-run validated user confirmation, including the exact project/type/Epic/Parent values used;
- a clear statement that the reviewed Story was partitioned once, with every non-AC section mapped to Description and only the approved AC section body mapped to the Acceptance Criteria field;
- the full Jira-wiki rendered Description and AC field, their source-to-field coverage check, and conversion validation confirming no raw Markdown heading markers remain outside code blocks;
- a clear statement that `TEST_CASE.md`, planning, scoring, review reports and manifests are not exported;
- attachment add/replace/delete plans with confirmed paths or filenames as appropriate;
- each upload's exact `<name>-spec.md` filename and passing filename-contract status, including consistency with the actual Workspace file, handoff, latest review and applicable update manifest;
- attachment preflight status, including confirmation that `attachmentsJson` is a serialized JSON string and parses to the displayed plan;
- full valid JSON payload; and
- for batch, the one-call export statement and per-item identifiers.

The JSON block must represent the direct named arguments to `exportJiraByDynamicFields`; never wrap them in `requestJson`. Remove `handoffType` before producing that block and verify it is absent from the outer arguments, `dynamicFieldsJson`, `attachmentsJson`, and `issuesJson`. Check the decoded effective payload for each item: direct `description` contains only non-AC Story sections, and the metadata-confirmed AC field occurs in that item's `dynamicFieldsJson` with the complete extracted criteria for creates or changed ACs. An update may omit that field only with a documented comparison to the matching current Jira AC. Do not treat a prose preview statement as proof that the serialized arguments obey the partition.

No approval tool or final confirmation popup exists in this route. After the complete preview is written, allocate the next `.ceaia-work/.../jira-export-attempt-<nnn>.md`, re-read the exact tool arguments and every upload file, and compare them with the preview. Any payload-affecting change requires a regenerated preview and repeated preflight before direct export.

Example single-create argument shape:

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "projectKey": "AIWPB",
  "summary": "Reviewed Story title",
  "issueType": "Story",
  "description": "h1. Reviewed Story title\n\nh2. User Story\nAs a member, I want to view my status, so that I can track progress.",
  "dynamicFieldsJson": "{\"fields\":{\"labels\":[\"CEAIA_GEN\"],\"customfield_12345\":\"AC-001: Given a saved status, when I open the status page, then I see that status.\"}}",
  "attachmentsJson": "{\"add\":[{\"relativePath\":\"outputs/ceaia/example/example-spec.md\",\"fileName\":\"example-spec.md\"}]}"
}
```

Example batch argument shape:

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "issuesJson": "[{\"projectKey\":\"AIWPB\",\"summary\":\"Story 1\",\"issueType\":\"Story\",\"description\":\"h1. Story 1\",\"dynamicFieldsJson\":\"{\\\"fields\\\":{\\\"labels\\\":[\\\"CEAIA_GEN\\\"],\\\"customfield_12345\\\":\\\"AC-001: Given a saved status, when I open the status page, then I see that status.\\\"}}\",\"attachmentsJson\":\"{\\\"add\\\":[{\\\"relativePath\\\":\\\"outputs/ceaia/story-1/story-1-spec.md\\\",\\\"fileName\\\":\\\"story-1-spec.md\\\"}]}\"}]"
}
```

These are structural examples only. Use actual validated metadata field IDs and current artifact paths; never copy example IDs or paths into a real payload.

### Stage G — Export and report

1. After the final preview/read-back/preflight passes, call `exportJiraByDynamicFields` immediately with exactly the displayed direct Jira create/update arguments, omitting `markdownReviewRelativePath` on this first call.
2. Do not automatically retry a failed call.
3. For each successful item, repeat the path/filename consistency check if the Workspace artefact or plan changed after preview, then run its validated attachment operations after the Issue write result is known.
4. After an item's issue write and requested attachments are confirmed successful, read that item's returned `ticketKey`; for an update verify it matches the intended key. Then follow `sdlc-gateway.md` Section 7: preview and read back a separate call containing exactly `staffId`, `almType`, `issueIdOrKey`, `conversationId`, and `markdownReviewRelativePath`, and call `exportJiraByDynamicFields` again. Use the candidate's current scored `STORY.md` path, not the internal score-record Markdown path. For a batch, make this second call once per successful item with that item's returned key and path.
5. Record the Jira write/attachment outcome and the returned `markdownReview` binding outcome separately. If binding fails or is unknown, keep the successful Ticket Key and do not recreate/update the Jira content merely to retry the association. Do not mark the whole item complete until both required stages pass.
6. On a confirmed successful Jira write, update the canonical SDLC-only `## CEAIA SDLC Jira Defaults` section in `jira-user-info.md` with allowed non-secret reusable values, including validated source, project, Story issue type and explicitly selected Epic/Parent Link defaults or the explicit no-Epic decision. No generic or Test Case route may perform this persistence.
7. Report each item separately: handoff item identifier, operation, Jira key/URL, issue-write status, attachment operation status, Markdown review binding status and sanitised errors. When describing content parity, compare `STORY.md` with Jira Description plus the separate AC field; raw Markdown and Jira-wiki syntax may differ, but business wording and order within each field must match the reviewed source partition.
8. For uploads, report the confirmed Workspace-relative path and filename used in the final plan. Never compress returned Jira keys into ranges.

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
