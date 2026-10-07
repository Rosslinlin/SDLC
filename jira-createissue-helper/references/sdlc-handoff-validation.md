# SDLC Handoff Validation

Reference version: 1.5.0

Used by: `flow-sdlc-validated-handoff.md`

Purpose: Validate a reviewed CEAIA SDLC artifact set before Jira metadata assembly, preview, attachment operations, or export.

## 1. Accepted Handoff Scope

Accept this flow only for a CEAIA SDLC validated handoff. The bundled end-to-end workflow produces it automatically after review PASS unless the user explicitly requests preparation-only/no-Jira output or stops. A handoff may represent:

- one new Story;
- multiple new Stories;
- one existing Jira ticket update; or
- multiple independent existing Jira ticket updates.

The handoff must state one operation per item: `create` or `update`. Never merge Stories, source evidence, attachment mappings, descriptions, or update intent across items.

## 2. Required Handoff Data

Validate the following logical fields before Jira payload assembly. The fields may be supplied in a structured object, a review report plus artifact paths, or another unambiguous equivalent representation.

| Field | Required for create | Required for update | Validation |
| --- | --- | --- | --- |
| `handoffType` | Yes | Yes | Set automatically by the bundled workflow. Internal routing metadata only; must equal `ceaia-sdlc-validated` and must be removed before every Jira MCP call. |
| `operation` | Yes | Yes | Must equal `create` or `update`. |
| `staffId` | At export | At export | Resolve in the helper from current context or, only for this SDLC route, `jira-user-info.md`; do not require generation intake to collect it. |
| `source` / `almType` | At export | Yes | Create may resolve it from validated SDLC defaults or final helper selection. Updates retain the explicitly confirmed ticket source from intake. Never infer it. |
| `projectKey` / project selection | At export | From current issue | Create resolves and validates project in the helper; update obtains project from `getJiraInfos`. |
| `issueType` | At export | From current issue | Create validates the saved/default Story type in the helper; update obtains current type and confirms metadata. |
| `epicLinkDecision` | At export | Preserve current unless authorised | First SDLC create collection requires a validated Epic key or explicit no-Epic decision. Later SDLC runs require confirmation of the revalidated saved decision before preview. A saved default never changes an update target implicitly. |
| `storyPath` | Yes | Yes | Must point to reviewed `STORY.md`. |
| `storyContent` | Yes | Yes | Must exactly equal the reviewed `STORY.md` content after line-ending normalization only. |
| `testCasePath` | Yes | Yes | Must point to `TEST_CASE.md`; audit only, never exported. |
| `specPath` | Yes | Conditional | Create: exactly one reviewed story-named SPEC. Update: primary reviewed SPEC when the manifest has an add/replace operation; optional for `attachmentAction=none`. |
| `specFileName` | Yes | Conditional | Must match `specPath`, use lowercase kebab-case unless an explicit safe filename exception was reviewed, and agree with Story/review evidence. |
| `specArtifacts` | Optional | Yes | Complete current reviewed SPEC register. Updates may contain multiple distinct replacement SPECs only when each has an explicit original-to-output mapping and independent PASS binding. |
| `reviewResult` | Yes | Yes | Must equal `PASS`. |
| `jiraReady` | Yes | Yes | Must be `true`. |
| `testCaseTitleValidation` | Yes | Yes | Must equal `manual-pass`. |
| `specHeaderSync` | Yes | Yes | Must show every intended SPEC synchronized to the latest PASS review attempt and successfully read back. |
| `ticketKey` | No | Yes | Must exactly identify the requested current Jira ticket. |
| `updateReady` | No | Yes | Must be `true`. |
| `attachmentOperations` | Optional | Optional | Must comply with Section 6. |

### 2.1 Mandatory SPEC Upload Filename Contract

This section is the authoritative filename rule for every SPEC uploaded through the validated SDLC route: new Story attachments, update `add` operations, and the new-file component of every `replace`, including each item in a batch or multi-SPEC update.

- The actual deliverable filename must be `<name>-spec.md`. `<name>` is a non-empty, reviewed Story/business-function name in lowercase kebab-case: lowercase ASCII letters and digits separated by single hyphens. The suffix is exactly lowercase `-spec.md` and cannot be omitted or replaced.
- Validate the entire filename case-sensitively against `^[a-z0-9]+(?:-[a-z0-9]+)*-spec\.md$`; do not trim or normalize a failing filename to make it pass. Regex shape alone is insufficient: `<name>` must also agree with the reviewed Story/business-function evidence.
- For example, `mobile-registration-entry-spec.md` is valid; `mobile-registration-entry.md`, `SPEC.md`, `-spec.md`, `Mobile-Registration-Entry-spec.md`, and `mobile_registration_entry-spec.md` are invalid. A prior PASS or a previously reviewed safe-filename exception does not waive this rule.
- The actual Workspace filename, `specPath`, `specFileName`, selected `specArtifacts` entry, latest review's `reviewedSpecArtifacts`, any Story/Test Case filename references, applicable update manifest output mapping, and upload plan `relativePath`/`fileName` must identify the same exact file. Show and submit that same filename in `jira-preview.md` and `attachmentsJson`.
- Multiple distinct replacement SPECs must retain distinct reviewed names and original-to-output mappings. A distinguishing business-function qualifier belongs in `<name>` before `-spec.md`; do not merge files or invent new names at export time.
- If any intended upload fails the naming or consistency checks, block before payload/preview/export. Report the offending path/name and required format. Return the affected artifact and filename references/mappings to the upstream preparation and independent review process, preserving business content. Resume only after a corrected handoff has current PASS and SPEC-header read-back evidence for the exact corrected paths, then rebuild the preview and repeat preflight. Do not silently rename/copy a reviewed file, alter only the upload `fileName`, or reuse a PASS bound to the old path.
- This rule does not rename existing Jira attachments used as source, retained attachments, or old targets of authorised `delete`/`replace` operations. Their exact original names/IDs remain in the mapping; only newly uploaded files must comply. An update with no uploads does not need a new SPEC solely for naming compliance. Ordinary non-SDLC attachments are unaffected.

## 3. Artefact And Review Gate

Before querying create metadata or constructing an update payload, validate all of the following:

1. Exactly one `STORY.md` and one `TEST_CASE.md` are present for each handoff item. A create has exactly one reviewed story-named SPEC. An update has the complete current SPEC register: zero uploads for `attachmentAction=none`, or every explicitly mapped add/replace SPEC; distinct reviewed replacement contents remain distinct files.
2. The Story, Test Case, SPEC, review report, and update manifest, when applicable, agree on the exact SPEC filename.
3. The review report contains a clean `PASS`, `jiraReady: true`, and `testCaseTitleValidation: manual-pass` for the same artefact paths.
4. For update items, the review report contains `updateReady: true`, the exact ticket key, the exact title, and the applicable attachment replacement mapping.
5. `TEST_CASE.md`, planning, scoring, review reports, and manifests are internal/audit artefacts only. They must not become Jira attachments or be inserted into Jira Description.
6. SPEC files are the only standard SDLC attachments. Multiple SPECs for one update are valid only when the approved update manifest maps each selected original attachment to a distinct reviewed output where required; unselected local SPECs are not uploaded. A new Story still has exactly one matching SPEC.
7. The Story must not contain workspace paths, source IDs, URLs, planning references, review operations, or `TEST_CASE.md` attachment/reference in Jira-visible content.
8. Every final SPEC's first two review-header fields match the latest PASS and reviewer notes, identify the latest review attempt where the active template records it, and have a successful post-synchronization read-back. A PASS report or `jiraReady` flag alone cannot waive this check.

Any failed item blocks that item. In an integrated batch, report the blocked items and do not build a write payload until all selected items pass the relevant gate.

## 4. Story, Summary And Acceptance Criteria Mapping

### 4.1 Description

Use the reviewed `STORY.md` as the sole semantic source for the two Jira text fields. Apply `sdlc-gateway.md` Section 4's single canonical partition: remove the complete `## Acceptance Criteria` section from the Description source while retaining every other section in order, including sections following AC. Render that non-AC source to Jira wiki without summarising, regenerating, paraphrasing or changing business wording. Do not modify `STORY.md`. Description must contain no extracted AC heading, declaration or Given/When/Then body.

### 4.2 Summary

For a create, use the reviewed Story title as the generated Summary unless a reviewed, evidence-supported Summary is explicitly supplied. For an update, keep Summary unchanged unless the handoff explicitly requests a reviewed, evidence-supported replacement.

### 4.3 Acceptance Criteria Field

After `queryJiraCreateMetaFields`, inspect the visible writable fields for an Acceptance Criteria field. Identify it using returned field ID, name, schema, and operations; never invent a field ID.

Use only the body of the approved Acceptance Criteria section extracted by the same partition, in exact stable `AC-###` order with unchanged Given/When/Then wording. Render its Markdown presentation syntax to Jira wiki and place it in the metadata-confirmed Jira Acceptance Criteria field. The field label represents the source heading. On an update, a Description-only change remains valid when the separately retrieved current AC field matches the reviewed AC IDs, wording and order after allowed presentation normalization; leave that field untouched and record the comparison. Otherwise include the AC field replacement. Never treat an empty, unavailable or uncertain current AC value as a match.

- Confirm that each approved AC declaration appears exactly once in that field and never in Description; preserve non-AC sections in Description without loss or reordering.
- Do not send a similarly named non-writable, hidden, or disabled field.
- If a writable Acceptance Criteria field cannot be confirmed from metadata, block the SDLC export and explain the field-mapping limitation.
- If the Story has no extractable approved Acceptance Criteria, block the handoff because it cannot satisfy the SDLC review contract.

## 5. Required-Field Decision Protocol

For each create or update item, query current Jira metadata and process required fields in this order:

1. **Direct SDLC mapping:** use validated project, type, reviewed Summary, partitioned non-AC Story Description, and extracted Acceptance Criteria or a verified matching current AC field on update.
2. **Current issue preservation:** for an update, preserve current values unless an explicitly authorised change is required.
3. **Visible metadata default:** use a visible, non-disabled default where it satisfies the field schema and does not conflict with handoff evidence.
4. **Safe evidence-based inference:** use a value only where the handoff/reviewed Story directly supports it and it is an allowed visible option.
5. **Focused user question:** if a required field still has no safe value, ask the user for that one field, its purpose, and the valid metadata options. Do not ask for Summary, Description, or Acceptance Criteria content.

For every selected value, record one of: `user-provided`, `reviewed-handoff`, `current-value`, `metadata-selected`, `inferred`, or `unavailable`. Show this provenance in `jira-preview.md`.

## 6. Attachment-Operation Validation

Attachment operations are separate from dynamic fields and run only after the associated Jira create/update succeeds.

### 6.1 Create

- Add exactly one reviewed SPEC attachment using `add`.
- Do not delete or replace attachments on a newly created issue.

### 6.2 Update

- `add` requires a relative workspace path and explicit handoff/user authorisation.
- `replace` requires the current attachment ID or an unambiguous, user-selected attachment mapping, plus the reviewed replacement SPEC path.
- `delete` requires the current attachment ID or an unambiguous, user-selected target and explicit handoff/user authorisation.
- Replace uploads the new file before deleting the mapped old file.
- Do not delete an attachment merely because it was used as source evidence.
- Reject URLs, absolute paths, `data:` / `file:` URIs, and `..` traversal.

For existing-ticket updates, attachment IDs remain internal. Present filenames to the user where a choice is necessary. Do not infer an ambiguous target from a similar filename.

### 6.3 Mandatory Upload-Path Preflight

For every `add` operation and every replacement upload, before preview and before export:

1. Inspect the proposed `relativePath` using the Workspace read capability in the applicable scope.
2. Confirm that the file is readable and the returned Workspace-relative path exactly equals the proposed path.
3. Confirm that the filename component of the returned path equals `specFileName` and the attachment plan `fileName`.
4. Confirm that the path and filename match the reviewed SPEC and, for updates, the applicable update manifest.
5. Retain the confirmed returned path in the attachment plan.
6. Block if any check fails.

Do not repair a failing path by guessing a nearby directory, searching for a similarly named file, or changing the reviewed SPEC mapping. A user-supplied correction or explicit stop decision is required.

### 6.4 Attachment Serialization Validation

When an export request uses `attachmentsJson`:

1. Build the attachment plan as an object internally.
2. Serialize that object exactly once into the `attachmentsJson` field.
3. Verify that the outer `attachmentsJson` value is a string.
4. Parse that string once and verify it recreates the authorised internal attachment plan.
5. Block if `attachmentsJson` is an object, array, null, or cannot be parsed as the validated plan.

## 7. Blocking Conditions

Block the SDLC payload and export when any of the following applies:

- source is absent, unsupported, or inferred;
- review result is not `PASS`;
- `jiraReady` is not `true`;
- `testCaseTitleValidation` is not `manual-pass`;
- any intended SPEC lacks verified synchronization to the latest PASS review attempt;
- an update item lacks `updateReady: true`;
- a required artefact is missing, inconsistent, or not reviewed;
- Story content does not match the reviewed Story artefact, or the two Jira text fields fail the Section 4 partition and source-to-payload comparison;
- any intended SPEC upload violates Section 2.1's `<name>-spec.md` contract, including an upload alias that differs from the actual file or review mapping;
- a create has zero or more than one SPEC, or an update's selected attachment mappings and complete reviewed `specArtifacts` register disagree;
- the Jira Acceptance Criteria field is absent, non-writable, hidden, disabled, or ambiguous;
- any required Jira field cannot be safely resolved and the user has not provided a valid value;
- an attachment add/delete/replace operation lacks valid path, mapping, or authorisation;
- an upload path is missing, unreadable, changed, inconsistent with the reviewed SPEC, or not confirmed in the applicable Workspace scope;
- `attachmentsJson` is not a string containing the validated serialized attachment plan; or
- Jira metadata or existing-ticket information cannot be confirmed.

Also block if the Jira-bound Description still contains raw Markdown headings outside code blocks, if a Jira-wiki conversion changes business meaning, if `handoffType` appears anywhere in Jira tool arguments or serialized Jira data, or if the current preview/export binding contains a different rendered Description.

Never downgrade a blocked SDLC handoff to generic create, update, or batch export.

## 7.1 Post-write Markdown review binding

The v5 SDLC route performs Markdown review ticket binding only after a separate, successful Jira create/update call returns that item's ticket key and all requested attachment operations succeed. It never adds `markdownReviewRelativePath` to the first Jira-write payload. Before the second call, verify that the five required non-empty values are available: real `staffId`, supported `almType`, confirmed returned ticket key as `issueIdOrKey`, actual current `conversationId`, and the Workspace-relative `STORY.md` path used by this candidate's current passing scoring invocation as `markdownReviewRelativePath`. The score-record Markdown file itself is not this path. Check the candidate/state/score/review binding and, for updates, exact key equality. A failed check blocks only the binding stage and preserves the successful Jira result. Do not add other Jira mutation fields to the second call.

## 8. SPEC Filename Regression Checks

All other review, metadata and authorisation gates still apply to cases marked as passing the filename gate.

| Scenario | Expected result |
| --- | --- |
| Create with reviewed `mobile-registration-entry-spec.md`, matching actual path, handoff, review and upload plan | Pass filename gate; upload exactly that name. |
| Create or update upload of `mobile-registration-entry.md` or `SPEC.md`, even with PASS | Block before preview/export; fixed suffix is mandatory. |
| Upload of `-spec.md`, `Mobile-Registration-Entry-spec.md`, `mobile_registration_entry-spec.md`, `mobile--registration-spec.md`, or `mobile-registration-spec.MD` | Block for empty name, uppercase or invalid kebab-case/extension. |
| Plan uses `mobile-registration-entry-spec.md` but actual file or reviewed mapping still uses `mobile-registration-entry.md` | Block; an upload-only alias is not a valid correction. |
| File renamed to a compliant name after PASS or preview, without updated review binding | Block; require corrected reviewed handoff, fresh preview and preflight. |
| Replace existing Jira `legacy-design.md` with reviewed `mobile-registration-entry-spec.md` | Pass filename gate for the new file; retain the exact old target mapping and upload before deletion. |
| Multi-SPEC update with distinct reviewed `mobile-registration-entry-spec.md` and `mobile-registration-validation-spec.md` | Validate every new file and mapping independently; never collapse the outputs. |
| Integrated batch contains one noncompliant SPEC upload | Block the integrated payload unless the user explicitly removes the blocked item. |
| Authorised delete-only update or `attachmentAction=none`, with legacy attachment names | Do not require renaming or fabricate a new upload. |
| Ordinary non-SDLC attachment named `design.md` | Do not apply the SDLC suffix rule; use the ordinary attachment checks. |
