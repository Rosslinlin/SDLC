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

The call shape is still one JSON path, but this is not evidence of update support. Actual backend/schema evidence must define the JSON's exact target and approved none/replace semantics, including score-only SPEC handling. Until then keep WAITING_TOOL; no invented update payload or create-shaped example is provided. See [attachments](attachments.md).

The backend response schema is not fully supplied. Interpret and preserve the real response under [submission and recovery](jira-write-and-recovery.md); do not copy a fictional success response or send the old five-field B call.
