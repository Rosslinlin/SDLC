# Path-only export examples

Tool: java-base-mcp.exportJiraByDynamicFieldsV2. Every invocation has exactly one argument. Example paths are structural, never evidence or defaults.

## One reviewed, scored and currently confirmed Story

```json
{
  "story_json_workspace_path": "outputs/ceaia/registration-status/story.json4jira"
}
```

Copy the exact path from this candidate's actual passing scoreUserStory input. The file already contains root descPath/acPath references to two distinct scored Jira Wiki files, other approved Jira fields and root attachmentsJson operations under [attachment rules](attachments.md). The backend resolves the Wiki files when exporting; the agent still sends only the JSON path. attachmentsJson belongs inside that file, not beside story_json_workspace_path as a second argument. Do not pass staffId, source, projectKey, content, SPEC status/path, attachment controls, requestJson, issuesJson or an association payload beside the path. Identity/session context is platform-owned.

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

Each file has its own actual SPEC review, score and current-content confirmation bound to its JSON, both Wiki files and SPECs. Each actual invocation gets one numbered export record under its `paths.historyRoot`; a resume/read/save retry does not allocate another. Do not join paths into an array/string wrapper or substitute one candidate's score for another. Stop further writes after a failed/partial/unknown required outcome; accepted async score synchronization may remain pending.

## Existing-ticket updates

The user-confirmed update JSON has root issueIdOrKey after issueType, containing the actual existing-ticket number/key. Create JSON omits it. Reuse the scored file without adding the ticket key to tool arguments:

```json
{
  "story_json_workspace_path": "outputs/ceaia/updates/WPB/EXAMPLE-123/story.json4jira"
}
```

This example uses the direct Story update target and preserves applicable baseline values. Assemble reviewed SPEC uploads before scoring using this root property:

```json
{
  "attachmentsJson": {
    "add": [
      {
        "relativePath": "outputs/ceaia/updates/WPB/EXAMPLE-123/current-story-spec.md",
        "fileName": "current-story-spec.md"
      }
    ]
  }
}
```

This excerpt is inside the full Story JSON. Both create and update require a nonempty attachmentsJson.add array. Omit the legacy attachments path list and keep all existing attachments on the issue. The synchronized local SPEC is still scored through specFilePath.

The backend response schema is not fully supplied. Interpret and preserve the real response under [submission and recovery](jira-write-and-recovery.md); do not copy a fictional success response or send the old five-field B call.

## Internal evidence paths

For a new registration-status candidate, the compact preview is `.ceaia-work/stories/registration-status/jira-preview.md`; its actual invocation record is `.ceaia-work/stories/registration-status/jira-export-attempt-001.md`. Planning and Assembly share `.ceaia-work/stories/registration-status/preparation.md` in version-bound sections. An update uses the corresponding `.ceaia-work/updates/<SOURCE>/<TICKET-KEY>/` root. These are structural examples, never save defaults for another candidate. Resolve the actual historyRoot and verify the returned save path before writing; only current business deliverables belong beside story.json4jira. Preserve actual legacy evidence paths rather than renaming them to create a new attempt.
