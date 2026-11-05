# Path-only export examples

Tool: java-base-mcp.exportjirabydynamicfield. Every invocation has exactly one argument. Example paths are structural, never evidence or defaults.

## One reviewed, scored and currently confirmed Story

```json
{
  "story_json_workspace_path": "outputs/ceaia/registration-status/story.json4jira"
}
```

Copy the exact path from this candidate's actual passing scoreUserStory input. The file already contains the complete approved Jira fields and root attachmentsJson operations under [attachment rules](attachments.md). attachmentsJson belongs inside that file, not beside story_json_workspace_path as a second argument. Do not pass staffId, source, projectKey, content, SPEC status/path, attachment controls, requestJson, issuesJson or an association payload beside the path. Identity/session context is platform-owned.

## Several candidates

First confirmed candidate:

```json
{
  "story_json_workspace_path": "outputs/ceaia/registration-status/story.json4jira"
}
```

Only after its required synchronous outcomes are confirmed, submit the next confirmed candidate:

```json
{
  "story_json_workspace_path": "outputs/ceaia/application-details/story.json4jira"
}
```

Each file has its own actual review, score and current-content confirmation. Each actual invocation gets one numbered export record under its `paths.historyRoot`; a resume/read/save retry does not allocate another. Do not join paths into an array/string wrapper or substitute one candidate's score for another. Stop further writes after a failed/partial/unknown required outcome; accepted async score synchronization may remain pending.

## Existing-ticket updates

The user-confirmed update JSON has root issueIdOrKey after issueType, containing the actual existing-ticket number/key. Create JSON omits it. Reuse the scored file without adding the ticket key to tool arguments:

```json
{
  "story_json_workspace_path": "outputs/ceaia/updates/WPB/EXAMPLE-123/story.json4jira"
}
```

This is a structural example, not a real ticket or an authorized write. Its JSON target must match the actual selected baseline and confirmed update intent; use approved changed fields with preservation/omission semantics. Story changes synchronize related local SPECs without waiting for the words "update SPEC". Identified related Jira SPECs use the tested root object below, assembled before scoring; each original ID comes from readJiraAttachments for this same source/ticket and its read contents positively identify the related SPEC. Example placeholders are not actual IDs or paths:

```json
{
  "attachmentsJson": {
    "replace": [
      {
        "attachmentId": "<actual old SPEC ID returned by readJiraAttachments>",
        "relativePath": "outputs/ceaia/updates/WPB/EXAMPLE-123/current-story-spec.md",
        "fileName": "current-story-spec.md"
      }
    ]
  }
}
```

This is a property excerpt inside the full Story JSON, not another tool invocation. With established absence of a related SPEC, use attachmentsJson.add entries containing relativePath/fileName instead. For add/replace omit the legacy attachments path list; never combine operations with a nonempty path list. Explicit remote-preservation intent uses attachments=[] without attachmentsJson, with the synchronized local SPEC still scored through specFilePath. Non-SPEC/unselected/unresolved originals remain untouched. This confirmed format needs no repeated capability proof; do not emulate replace with add or introduce standalone delete.

The backend response schema is not fully supplied. Interpret and preserve the real response under [submission and recovery](jira-write-and-recovery.md); do not copy a fictional success response or send the old five-field B call.

## Internal evidence paths

For a new registration-status candidate, the compact preview is `.ceaia-work/stories/registration-status/jira-preview.md`; its actual invocation record is `.ceaia-work/stories/registration-status/jira-export-attempt-001.md`. Planning and Assembly share `.ceaia-work/stories/registration-status/preparation.md` in version-bound sections. An update uses the corresponding `.ceaia-work/updates/<SOURCE>/<TICKET-KEY>/` root. These are structural examples, never save defaults for another candidate. Resolve the actual historyRoot and verify the returned save path before writing; only current business deliverables belong beside story.json4jira. Preserve actual legacy evidence paths rather than renaming them to create a new attempt.
