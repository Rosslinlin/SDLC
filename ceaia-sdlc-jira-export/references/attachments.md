# SPEC synchronization and attachment operations

Generation assembles the approved upload plan in the final Story JSON before scoring. Export verifies the unchanged file and submits its exact path.

## File format and scoring files

The native `attachmentsJson` object contains only `add` entries for current reviewed SPECs. An entry has the exact readable Workspace-relative `relativePath` and matching `fileName`. For example:

```json
{
  "attachmentsJson": {
    "add": [{"relativePath": "outputs/ceaia/updates/<SOURCE>/<TICKET-KEY>/current-spec.md", "fileName": "current-spec.md"}]
  }
}
```

Omit the legacy `attachments` path array when adding files. A create adds its reviewed primary SPEC. An update adds its affected reviewed SPECs as new attachments and retains all originals. Explicit no remote attachment change uses `attachments=[]` and omits `attachmentsJson`. In either case, `specFilePath` identifies the current local scoring SPEC. The upload plan contains only authorized SPECs, excluding local score-only files and internal artifacts.

## Preview and submission

For an update, verify the exact root `issueIdOrKey` against the confirmed target/source and baseline. Creates omit it. Before asking for confirmation and immediately before submitting, verify the current scored JSON, its two referenced Wiki paths and revisions, and each intended upload against the reviewed file paths, names and revisions. Use the platform evidence for Wiki currentness under [handoff](handoff-and-content.md). Preview the new files and retention of all existing attachments, or explicit none. Read original SPECs as baseline evidence where needed. Their IDs do not enter an upload entry.

Return any pending file with replace/delete operations or old attachment IDs in upload entries upstream. Prepare the approved add or none plan, review and score the resulting revision, and obtain renewed content confirmation. The exporter must not rewrite a scored file. Preserve known past results and reconcile unknown prior attempts before another write.

Call the exporter with only the confirmed `story_json_workspace_path`. Record issue success and each new upload outcome separately. Preserve existing attachments and confirmed uploads. A failed or unknown upload is not completed. Use only supported focused recovery for unresolved effects and never replay confirmed writes or uploads. See [submission and recovery](jira-write-and-recovery.md).
