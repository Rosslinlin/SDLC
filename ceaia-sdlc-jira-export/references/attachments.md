# Reviewed Story Attachments

Use only for the reviewed SPEC attachment associated with the current Story. The upstream handoff and its reviewed update manifest are authoritative for attachment selection and Jira attachment IDs.

## Create

- A new Story receives exactly one `SPEC` attachment: the single reviewed primary SPEC named `<story-name>-spec.md`.
- The exact Workspace-relative `relativePath` and `fileName` must match the handoff, review, and current state mapping. Persistent user profiles never supply conversation attachment paths. Do not rename, substitute, or infer a nearby file.
- An explicitly reviewed many-to-one mapping may attach one reviewed SPEC to multiple Stories; require that mapping to be authorized per item and preserve each item's operation in its own payload. Never infer sharing from identical paths.

## Update

Apply only the operation explicitly authorized by the latest reviewed manifest: add, replace, delete, or none. A manifest may list multiple SPECs and their per-file operations. Preserve its order and exact mapping. Do not infer an operation from Jira's current attachments or add unlisted files.

- For add or replacement, the manifest must identify the reviewed upload path and the existing Jira attachment ID for replacement.
- For delete or replacement, use only the original attachment ID recorded in the upstream Jira attachment intake/manifest. Do not guess IDs or target attachments by filename alone.
- Existing Jira attachment filenames are not subject to the new upload `<name>-spec.md` naming rule and must not be renamed just to match it. If multiple old attachments share a filename, target only by an attachment ID already confirmed in the upstream intake; never select by filename. Reusing one old attachment for multiple new SPEC mappings (many-to-one) requires explicit authorization in the reviewed manifest.
- A replacement uploads the new SPEC first; delete the old attachment only after the new upload is confirmed successful.
- An attachment operation of `none` means omit `attachmentsJson` (or use only a live-contract accepted inactive empty-string sentinel when necessary). It must not suppress a manifest-required operation.
- Once the first issue-write call reports an upload successful, never upload it again during recovery or binding.

## Local preflight and serialization

Before preview and immediately before the export call, read every planned upload in the applicable Workspace scope and confirm:

1. The returned Workspace-relative path exactly equals the manifest `relativePath`.
2. `fileName` exactly equals the filename component of that path and the reviewed `<name>-spec.md` name.
3. The file is readable, current, and still the SPEC authorized for this candidate and operation.
4. Every replace/delete ID belongs to the intended Jira ticket and matches the upstream manifest.

Reject absolute paths, URLs, URI schemes, `..` traversal, missing or unreadable files, and any path/name mismatch. Do not normalize or substitute paths. A naming defect must return upstream for correction and review.

Build the authorized plan as an object, then serialize it exactly once into the `attachmentsJson` string. The plan may use only the supported `add`, `replace`, and `deleteAttachmentIds` operations. Parse the string once during validation and confirm it recreates the exact reviewed operations, paths, names, and IDs. Do not double-serialize or manually alter escaping. For a batch create, place each Story's attachment plan inside its own `issuesJson` item; do not rely on a top-level attachment plan being inherited.

The repository helper documents the exact `add` and `deleteAttachmentIds` shapes below. It identifies `replace` as a supported operation but does not define its inner object schema. Use the active Jira tool's documented schema for `replace`; if it is unavailable, block replacement and return for clarification rather than guessing field names or structure.

Known `add` shape:

```json
{
  "add": [
    {
      "relativePath": "outputs/ceaia/story-a/story-a-spec.md",
      "fileName": "story-a-spec.md"
    }
  ]
}
```

Known `deleteAttachmentIds` shape (ID must be the confirmed ID from the update manifest/intake):

```json
{
  "deleteAttachmentIds": ["10001"]
}
```

The complete outer export argument for an add is still a string, serialized once:

```json
{
  "attachmentsJson": "{\"add\":[{\"relativePath\":\"outputs/ceaia/story-a/story-a-spec.md\",\"fileName\":\"story-a-spec.md\"}]}"
}
```

Do not combine a separately guessed `add` and `deleteAttachmentIds` plan and call it a `replace`. Preserve the supported replacement operation only when its exact live schema is known; upload-before-delete ordering remains mandatory.

Include the exact serialized attachment string in `jira-preview.md` and in the numbered attempt record. Immediately before writing, re-read upload bytes and verify the preview binding is still current. Any changed file, path, filename, ID, operation, or serialized value invalidates that preview: regenerate it and repeat preflight.

## Result handling

Track the Jira issue result separately from each attachment result. Report the ticket key, operation, Workspace path and filename for uploads, preflight status, result, and sanitized error. If an attachment result is failed, partial, or unknown, preserve a successful issue write and reconcile only the uncertain attachment. Do not automatically retry, re-upload a confirmed successful file, or delete the old attachment after an unconfirmed replacement upload. Resume only through the bounded recovery procedure in `jira-write-and-recovery.md`.
