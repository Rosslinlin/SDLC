# Common Attachments

Use for add, delete and replace operations. Preview and confirmation are mandatory.

- Process attachments only after successful issue create/update.
- Never merge attachment instructions into dynamic fields.
- New issues support `add` only.
- Delete/replace require `issueIdOrKey` and validation that the attachment belongs to that issue.
- Replace uploads the new file before deleting the old one.

## 1. Workspace path and file preflight

Every attachment `add` or replacement upload must identify a Workspace-relative `relativePath` and a matching `fileName`.

1. Reject URLs, absolute paths, `data:` / `file:` URIs, and `..` traversal.
2. Before generating `jira-preview.md`, inspect the proposed file using the Workspace read capability in the applicable Workspace scope.
3. Confirm that the returned artifact is readable and that its exact returned Workspace-relative path equals the proposed `relativePath`.
4. Confirm that `fileName` equals the filename component of the confirmed `relativePath`.
5. For an SDLC handoff, also confirm that the path and filename exactly match the single reviewed SPEC and any applicable update manifest.
6. Repeat the path/filename consistency check immediately before export if the attachment Workspace artifact changed since preview.

Do not infer, normalize, relocate, or substitute an attachment path. A missing, unreadable, changed, inconsistent, or unconfirmed path blocks the attachment operation. Report the exact Workspace-relative path and the available failure detail, then request correction, a newly supplied path, or an explicit stop decision.

Workspace readability is a mandatory local preflight. It does not guarantee that a later external Jira transport cannot fail, but it prevents submission of an attachment path that was not confirmed available in the Workspace.

## 2. Attachment-plan serialization contract

When no attachment operation is planned or required, omit `attachmentsJson`, or pass `""` only if the active tool accepts it as an inactive sentinel. Never use `"."`. Do not parse this unused empty sentinel as an attachment plan. The serialization and validation requirements below apply to active attachment operations; required operations, upload paths, and filenames must not be replaced by empty values or placeholders.

Build the attachment plan as an object internally. The export parameter `attachmentsJson` must then be a string containing that plan serialized exactly once.

### Required internal plan shape

```json
{
  "add": [
    {
      "relativePath": "outputs/ceaia/updates/AIWPB-9235/mobile-registration-entry.md",
      "fileName": "mobile-registration-entry.md"
    }
  ]
}
```

The concrete operation keys supported by the export interface may include `add`, `delete`, and `replace`; use only the authorised operation(s) for the current item.

### Required outer export payload shape

```json
{
  "attachmentsJson": "{\"add\":[{\"relativePath\":\"outputs/ceaia/updates/AIWPB-9235/mobile-registration-entry.md\",\"fileName\":\"mobile-registration-entry.md\"}]}"
}
```

The following is invalid and must be blocked before confirmation/export because `attachmentsJson` is an object rather than a string:

```json
{
  "attachmentsJson": {
    "add": [
      {
        "relativePath": "outputs/ceaia/updates/AIWPB-9235/mobile-registration-entry.md",
        "fileName": "mobile-registration-entry.md"
      }
    ]
  }
}
```

Before preview and export, validate all of the following:

1. the outer payload is valid JSON;
2. `attachmentsJson` is a string, not an object, array, null, number or boolean;
3. parsing `attachmentsJson` succeeds exactly once and returns an object;
4. the parsed plan contains only the authorised attachment operation(s);
5. every upload path in the parsed plan passed Section 1; and
6. the serialized value in the final payload matches the validated internal plan exactly.

Do not double-serialize `attachmentsJson`. Do not manually alter escaping after serialization.

## 3. Batch and result handling

In batches, define attachment operations per item; a top-level `conversationId` may be inherited. Report partial attachment failure separately from the Jira issue result.

For each attachment operation, report:

- Jira key;
- operation type;
- confirmed Workspace-relative path and filename for uploads;
- preflight status;
- export/attachment status; and
- a sanitised error, if any.

An attachment failure does not conceal a successful Jira issue create/update. Do not automatically retry, substitute a different file, or delete an existing attachment after a failed upload. Obtain the user's explicit correction, retry, defer, or stop instruction.
