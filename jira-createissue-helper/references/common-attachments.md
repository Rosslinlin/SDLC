# Attachment preparation and recovery

Read only when the user selects attachment add/delete/replace. Ordinary issues do not generate a SPEC or replace attachments by default. The [Test Case flow](flow-testcase-source-export.md) omits attachment controls from its two fixed file shapes.

## Supported input and intent

[The JSON contract](common-json4jira-contract.md) expresses ordinary new uploads as an `attachments` list of actual Workspace-relative file paths. Every listed file is an intended upload. An empty/no-upload list does not request deletion of remote files. A new issue supports add only.

Complete the authorized upload list before final confirmation. Reuse a valid prepared file unchanged. A changed JSON list, participating file or supported operation mapping invalidates affected prior confirmation and any claim that an upstream [assessment](story-assessment-evidence.md) is still current. Obtain updated content confirmation; do not impose rescoring or invoke scoring automatically. The project/backend decides whether the changed package requires an assessment.

For delete/replace, establish the exact existing ticket/source and original attachment IDs from the actual target's inventory, using `getJiraInfos` or an actually available supported attachment-read capability. Reuse complete applicable inventory evidence. Names alone do not identify deletion targets; a profile cannot supply an attachment inventory or permission to replace a file.

## Local preflight

Read each actual planned upload through the platform Workspace capability. Confirm its exact returned Workspace-relative path, current readable content and actual basename. Keep established paths unchanged; reject URLs, absolute paths, traversal, missing files and substituted examples. Reuse complete reads only when no file change intervened.

If an operation schema has a separate filename, it must match the actual confirmed file. Do not rename ordinary attachments to a SPEC convention or infer a conversation ID from their paths. Authentication/session identity remains platform-owned.

## Delete and replace boundaries

Use delete/replace only when the actual production JSON contract establishes their field positions/types and supported behavior. The path list alone expresses an upload, not which original attachment to replace. An old serialized plan or the root issue target does not establish the new replacement encoding.

Retain the selected original-ID-to-new-file mapping in existing preview/context. Do not invent a replace object, infer a delete API, add legacy extra tool parameters or call a separate legacy upload/delete operation. If the runtime provides the supported encoding, prepare it before preview and confirmation; otherwise report the precise pending operation and missing representation rather than silently choosing no attachment change.

Replacement must confirm successful new upload before deleting the selected old attachment. Keep old files on failed or unknown upload. Preserve unrelated attachments and distinct replacement contents; do not target by filename or merge mappings for convenience. A requested deletion requires the actual selected ID and its current target membership.

## Confirmation and outcomes

Use [the common preview/submission process](common-preview-confirm-export.md). Show selected adds/deletes/replacements and their real file/ID mappings. Submit the final file path only; never send attachment arguments beside it. A scope change to defer an attachment operation must be explicit, reflected in the current file, accurate assessment-evidence status and confirmed content.

Record issue success separately from each attachment effect. Preserve confirmed uploads/removals and old IDs after partial failure. Do not recreate/update a successful issue, re-upload a confirmed file or delete old content to repair an unknown effect. A local result-save failure retries only the save. Use supported read-only reconciliation and user-directed recovery for actual failed/unknown operations; do not replay the whole export.
