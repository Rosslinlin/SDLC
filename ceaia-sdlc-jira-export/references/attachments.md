# Reviewed SPEC attachments

The reviewed manifest owns attachment operations, original IDs and output mappings. Only reviewed SPECs may be uploaded; profiles never supply conversation file paths.

## Allowed plans

- Create: exactly one primary `<name>-spec.md` per Story. Explicit reviewed many-to-one sharing remains authorized separately per item; never infer sharing from identical paths.
- Bundled update: none or replace only. none omits attachmentsJson (or a live-contract accepted inactive empty string) and never suppresses a required operation. A local no-upload SPEC is not described as uploaded.
- Replace: every selected original attachment ID maps to its reviewed output path/name. Distinct contents keep distinct files. Existing names need not match new naming rules. Duplicate old names are selected by upstream-confirmed ID, never filename alone. Many-to-one replacement requires explicit reviewed authorization.
- Exporter add/delete support is available only to another explicitly supported reviewed upstream workflow, not as an extension/fallback for bundled updates. Delete uses original confirmed IDs; add uses reviewed files.

**Replacement uploads first and deletes the selected original only after confirmed upload success.** Preserve an old file after failed/unknown upload. Never re-upload an already confirmed success.

## Local preflight

Before preview and immediately before A, read planned uploads in the actual Workspace scope. Check exact manifest relativePath, matching fileName/basename, current reviewed content and operation, and original IDs belonging to the intended ticket. Reuse same-stage reads only when no edit intervened. Reject absolute paths, URLs/schemes, traversal, missing/unreadable files or mismatched names/paths. Return defects upstream; do not substitute or rename at export. No routine remote inventory reread.

Build the authorized plan as an object and serialize once into attachmentsJson. Parse once to confirm exact operations, paths, names and IDs. For batch create, each issuesJson item carries its own serialized plan; top-level attachments are not inherited. The exact string and file bindings appear in the preview/numbered attempt. A changed file/path/ID/operation/string invalidates that preview and requires affected preflight again.

Known add shape:

```json
{"add":[{"relativePath":"outputs/ceaia/story-a/story-a-spec.md","fileName":"story-a-spec.md"}]}
```

Known deletion shape (only with separately supported reviewed authorization):

```json
{"deleteAttachmentIds":["10001"]}
```

Outer direct argument remains a string:

```json
{"attachmentsJson":"{\"add\":[{\"relativePath\":\"outputs/ceaia/story-a/story-a-spec.md\",\"fileName\":\"story-a-spec.md\"}]}"}
```

The tool supports replace but this repository does not define its inner schema. Use the live documented schema; if unavailable, block replacement with that precise tool-contract gap. Never guess its fields or combine separately guessed add/delete and label it replace. Upload-before-delete remains mandatory.

## Outcomes

Track issue results separately from each attachment result, including operation, upload path/name or original ID, preflight and sanitized error. A failed/partial/unknown attachment preserves a successful issue but blocks B until reconciled and successful. Recovery follows jira-write-and-recovery.md; never replay a successful issue or confirmed upload, or delete an old attachment after unconfirmed replacement upload.
