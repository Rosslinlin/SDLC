# SPEC synchronization and JSON attachment boundaries

Generation assembles the final JSON and authorized plan before scoring. Export verifies saved files and mappings; it does not construct attachmentsJson or invent JSON update/replace fields.

## Scoring files versus uploads

The backend uploads every file listed in the .json4jira attachments list. Inspect that list against the reviewed plan; no implicit extra files or top-level batch inheritance. A new Story lists its reviewed primary SPEC. Story business changes automatically update the local SPEC projection, affected requirements/flows and tests before scoring, whether or not the user mentions SPEC or attachments. Check every related SPEC; none never means skip local synchronization. A routing/schema-only change or already consistent business content needs a consistency check, not a fabricated document edit.

Distinct replacement contents retain their own reviewed files and original-ID mappings. Only the current reviewed SPEC uploads are in this bundled route; do not add Test Case, state, plans, baselines, profiles or score/review records.

scoreUserStory separately receives the local current SPEC through its confirmed specFilePath argument. When the user explicitly requests no remote attachment changes, none sets attachments=[] while the synchronized local SPEC still undergoes review/scoring; no extra capability-proof gate is needed merely because it is not uploaded. If the backend actually rejects this package, preserve the exact error and return a precise WAITING_TOOL; never override an explicit none, invent a score or fall back to create.

## Update targets and attachment operations

Existing-ticket targeting is confirmed: the scored JSON places root issueIdOrKey after issueType, containing the exact nonempty approved ticket number/key. It must match the saved baseline and source; create JSON omits this property. The exporter still passes only story_json_workspace_path. Do not gate basic update targeting as unknown or replace it with a create.

The SDLC update request includes synchronized SPEC delivery; do not ask whether to update SPEC or select none because the user mentioned only Story. Generation reads the attachment inventory and plans one of these internal actions before scoring:

| Internal action | Use and expected result |
| --- | --- |
| replace | Related existing Jira SPECs are present: update all relevant local contents and map each selected original ID to its current output. Ambiguous originals need only a focused selection; unrelated attachments remain untouched. |
| add | No related Jira SPEC exists: generate the current local SPEC and include its actual path as the first SPEC upload. The existing attachments path list expresses that upload; no new control/tool is needed. |
| none | The user explicitly preserves remote attachments: upload list is empty, but the current local SPEC is still synchronized, reviewed and scored. Report that remote SPECs were intentionally not changed. |

These are internal plan values, not extra tool arguments or invented JSON control fields. The confirmed issueIdOrKey establishes the ticket to update, not an attachment-replacement encoding. Show the selected SPECs and actual add/replace/none effects in final content confirmation. Default synchronization intent does not replace that final confirmation.

For replace, retain upstream-confirmed original attachment IDs and current reviewed output mappings. Do not target by filename alone, merge distinct contents, or rename legacy originals. Explicit reviewed many-to-one replacement requires preservation of every original's relevant obligations. **The supported backend operation must upload the new file and confirm success before deleting the selected old file.** If the live JSON route cannot establish that behavior, replacement is WAITING_TOOL with the exact pending SPECs; do not silently choose none, treat an additional upload as a confirmed replacement, or claim Story/SPEC synchronization complete. Do not invent a replace object, infer a delete call or execute a separate legacy upload/delete API.

## Local preflight and results

Read each actual planned upload and the primary scoring SPEC before frontend confirmation and immediately before submission. Check exact Workspace-relative path, actual basename, current reviewed content/revision and authorized operation/mapping. Reuse complete reads only when no edit intervened. Reject URLs, absolute paths, traversal, missing/unreadable files or mismatches; return upstream without substitution.

The upload list, primary scoring SPEC and their exact current revisions must match the score and frontend confirmation. A changed file/path/mapping invalidates affected confirmation/readiness and returns through the parent's review/scoring procedure. Preserve any service checksum exactly; no local hash or fabricated immutable version is required.

Record issue success separately from each required SPEC upload/replacement result. An updated local SPEC is not proof that its Jira attachment changed. A failed/partial/unknown SPEC operation stays pending and prevents reporting full requested synchronization; it never erases a confirmed issue or permits replaying it. For uncertain replacement upload, retain the old file; never re-upload confirmed success. Use the supported read-only reconciliation/recovery path in jira-write-and-recovery.md. No recovery method is implied by the old API shapes.
