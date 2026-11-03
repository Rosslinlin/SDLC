# Path-only export examples

Tool: java-base-mcp.exportJiraByDynamicFields. Every invocation has exactly one argument. Example paths are structural, never evidence or defaults.

## One reviewed, scored and currently confirmed Story

```json
{
  "story_json_workspace_path": "outputs/ceaia/registration-status/story.json4jira"
}
```

Copy the exact path from this candidate's actual passing scoreUserStory input. The file already contains the complete approved Jira fields and upload paths. Do not pass staffId, source, projectKey, content, SPEC status/path, attachment controls, requestJson, issuesJson or an association payload beside it. Identity/session context is platform-owned.

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

Each file has its own actual review, score, current-content confirmation and numbered export record. Do not join paths into an array/string wrapper or substitute one candidate's score for another. Stop further writes after a failed/partial/unknown required outcome; accepted async score synchronization may remain pending.

## Existing-ticket updates

The user-confirmed update JSON has root issueIdOrKey after issueType, containing the actual existing-ticket number/key. Create JSON omits it. Reuse the scored file without adding the ticket key to tool arguments:

```json
{
  "story_json_workspace_path": "outputs/ceaia/updates/WPB/EXAMPLE-123/story.json4jira"
}
```

This is a structural example, not a real ticket or an authorized write. Its JSON target must match the actual selected baseline and confirmed update intent; use approved changed fields with preservation/omission semantics. For a normal none update, attachments=[] preserves remote attachments and the local SPEC is scored through specFilePath. Replacement still needs the supported encoding in [attachments](attachments.md). An actual backend rejection is preserved and handled precisely, never converted into an extra upload or create fallback.

The backend response schema is not fully supplied. Interpret and preserve the real response under [submission and recovery](jira-write-and-recovery.md); do not copy a fictional success response or send the old five-field B call.
