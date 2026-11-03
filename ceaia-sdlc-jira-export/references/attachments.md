# JSON attachment boundaries

Generation assembles the final JSON and authorized plan before scoring. Export verifies saved files and mappings; it does not construct attachmentsJson or invent JSON update/replace fields.

## Scoring files versus uploads

The backend uploads every file listed in the .json4jira attachments list. Inspect that actual list against the reviewed plan; no implicit extra files or top-level batch inheritance. A normal new Story lists its reviewed primary SPEC. Distinct replacement contents retain their own reviewed files and original-ID mappings. Only selected reviewed SPEC uploads are in this bundled route; do not add Test Case, state, plans, baselines, profiles or score/review records.

scoreUserStory separately receives the local reviewed SPEC through its confirmed specFilePath argument. A normal none update sets attachments=[] and uses that local SPEC for review/scoring while preserving remote attachments; it does not need an extra capability-proof gate merely because the SPEC is not uploaded. If the backend actually rejects this package, preserve the exact error and return a precise WAITING_TOOL; never turn none into add, invent a score or fall back to create to make scoring work.

## Update targets and attachment operations

Existing-ticket targeting is confirmed: the scored JSON places root issueIdOrKey after issueType, containing the exact nonempty approved ticket number/key. It must match the saved baseline and source; create JSON omits this property. The exporter still passes only story_json_workspace_path. Do not gate basic update targeting as unknown or replace it with a create.

The parent may authorize attachmentAction none or replace. The confirmed issueIdOrKey establishes the ticket to update, not an attachment-replacement encoding. For none, keep attachments empty, preserve existing remote attachments and use the local SPEC through specFilePath. For replace, require actual schema/documentation for that requested attachment operation. No inferred add/delete combination, invented replace object or extra legacy API call is allowed.

For replace, retain upstream-confirmed original attachment IDs and current reviewed output mappings. Do not target by filename alone, merge distinct contents, or rename legacy originals. Explicit reviewed many-to-one replacement requires preservation of every original's relevant obligations. **The supported backend operation must upload the new file and confirm success before deleting the selected old file.** If the live JSON route cannot establish that behavior, replacement is WAITING_TOOL. Do not execute a separate legacy upload/delete API to bypass it.

## Local preflight and results

Read each actual planned upload and the primary scoring SPEC before frontend confirmation and immediately before submission. Check exact Workspace-relative path, actual basename, current reviewed content/revision and authorized operation/mapping. Reuse complete reads only when no edit intervened. Reject URLs, absolute paths, traversal, missing/unreadable files or mismatches; return upstream without substitution.

The upload list, primary scoring SPEC and their exact current revisions must match the score and frontend confirmation. A changed file/path/mapping invalidates affected confirmation/readiness and returns through the parent's review/scoring procedure. Preserve any service checksum exactly; no local hash or fabricated immutable version is required.

Record issue success separately from each upload/removal result. A failed/partial/unknown attachment never erases a confirmed issue and never permits replaying it. For uncertain replacement upload, retain the old file; never re-upload confirmed success. Use the supported read-only reconciliation/recovery path in jira-write-and-recovery.md. No recovery method is implied by the old API shapes.
