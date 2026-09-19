# SDLC Handoff Validation

Reference version: 1.1.0

Used by: `flow-sdlc-validated-handoff.md`

Purpose: Validate a reviewed CEAIA SDLC artifact set before Jira metadata assembly, preview, attachment operations, or export.

## 1. Accepted Handoff Scope

Accept this flow only for an explicit CEAIA SDLC validated handoff. A handoff may represent:

- one new Story;
- multiple new Stories;
- one existing Jira ticket update; or
- multiple independent existing Jira ticket updates.

The handoff must state one operation per item: `create` or `update`. Never merge Stories, source evidence, attachment mappings, descriptions, or update intent across items.

## 2. Required Handoff Data

Validate the following logical fields before Jira payload assembly. The fields may be supplied in a structured object, a review report plus artifact paths, or another unambiguous equivalent representation.

| Field | Required for create | Required for update | Validation |
| --- | --- | --- | --- |
| `handoffType` | Yes | Yes | Must equal `ceaia-sdlc-validated`. |
| `operation` | Yes | Yes | Must equal `create` or `update`. |
| `staffId` | Yes | Yes | Reuse from valid state only where permitted by common state rules. |
| `source` / `almType` | Yes | Yes | Must be explicitly supplied as `WPB`, `ALM`, `DATA`, `FCR` or `GO`; never infer it. |
| `projectKey` / project selection | Yes | From current issue | Create must validate project; update obtains project from `getJiraInfos`. |
| `issueType` | Yes | From current issue | Create must validate issue type; update obtains current type and confirms metadata. |
| `storyPath` | Yes | Yes | Must point to reviewed `STORY.md`. |
| `storyContent` | Yes | Yes | Must exactly equal the reviewed `STORY.md` content after line-ending normalization only. |
| `testCasePath` | Yes | Yes | Must point to `TEST_CASE.md`; audit only, never exported. |
| `specPath` | Yes | Yes | Must point to exactly one reviewed story-named SPEC. |
| `specFileName` | Yes | Yes | Must match `specPath`, use lowercase kebab-case, and be consistent with Story/review evidence. |
| `reviewResult` | Yes | Yes | Must equal `PASS`. |
| `jiraReady` | Yes | Yes | Must be `true`. |
| `testCaseTitleValidation` | Yes | Yes | Must equal `manual-pass`. |
| `ticketKey` | No | Yes | Must exactly identify the requested current Jira ticket. |
| `updateReady` | No | Yes | Must be `true`. |
| `attachmentOperations` | Optional | Optional | Must comply with Section 6. |

## 3. Artefact And Review Gate

Before querying create metadata or constructing an update payload, validate all of the following:

1. Exactly one `STORY.md`, one `TEST_CASE.md`, and one reviewed story-named SPEC are present for each handoff item.
2. The Story, Test Case, SPEC, review report, and update manifest, when applicable, agree on the exact SPEC filename.
3. The review report contains a clean `PASS`, `jiraReady: true`, and `testCaseTitleValidation: manual-pass` for the same artefact paths.
4. For update items, the review report contains `updateReady: true`, the exact ticket key, the exact title, and the applicable attachment replacement mapping.
5. `TEST_CASE.md`, planning, scoring, review reports, and manifests are internal/audit artefacts only. They must not become Jira attachments or be inserted into Jira Description.
6. The SPEC is the only standard SDLC attachment. A handoff with multiple proposed SPEC files for one Story is invalid.
7. The Story must not contain workspace paths, source IDs, URLs, planning references, review operations, or `TEST_CASE.md` attachment/reference in Jira-visible content.

Any failed item blocks that item. In an integrated batch, report the blocked items and do not build a write payload until all selected items pass the relevant gate.

## 4. Story, Summary And Acceptance Criteria Mapping

### 4.1 Description

Use the reviewed `STORY.md` content as Jira Description. Do not summarise, regenerate, paraphrase, or append a second Acceptance Criteria section. Only transport-safe line-ending normalisation is permitted.

### 4.2 Summary

For a create, use the reviewed Story title as the generated Summary unless a reviewed, evidence-supported Summary is explicitly supplied. For an update, keep Summary unchanged unless the handoff explicitly requests a reviewed, evidence-supported replacement.

### 4.3 Acceptance Criteria Field

After `queryJiraCreateMetaFields`, inspect the visible writable fields for an Acceptance Criteria field. Identify it using returned field ID, name, schema, and operations; never invent a field ID.

Extract the approved Acceptance Criteria from the reviewed Story in their exact stable `AC-###` order, retaining their Given/When/Then wording. Place this content in the metadata-confirmed Jira Acceptance Criteria field.

- Do not place extracted Acceptance Criteria in Description as a duplicate export representation.
- Do not send a similarly named non-writable, hidden, or disabled field.
- If a writable Acceptance Criteria field cannot be confirmed from metadata, block the SDLC export and explain the field-mapping limitation.
- If the Story has no extractable approved Acceptance Criteria, block the handoff because it cannot satisfy the SDLC review contract.

## 5. Required-Field Decision Protocol

For each create or update item, query current Jira metadata and process required fields in this order:

1. **Direct SDLC mapping:** use validated project, type, reviewed Summary, reviewed Story Description, and extracted Acceptance Criteria.
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
- an update item lacks `updateReady: true`;
- a required artefact is missing, inconsistent, or not reviewed;
- Story content does not match the reviewed Story artefact;
- there is zero or more than one SPEC for a Story;
- the Jira Acceptance Criteria field is absent, non-writable, hidden, disabled, or ambiguous;
- any required Jira field cannot be safely resolved and the user has not provided a valid value;
- an attachment add/delete/replace operation lacks valid path, mapping, or authorisation;
- an upload path is missing, unreadable, changed, inconsistent with the reviewed SPEC, or not confirmed in the applicable Workspace scope;
- `attachmentsJson` is not a string containing the validated serialized attachment plan; or
- Jira metadata or existing-ticket information cannot be confirmed.

Never downgrade a blocked SDLC handoff to generic create, update, or batch export.
