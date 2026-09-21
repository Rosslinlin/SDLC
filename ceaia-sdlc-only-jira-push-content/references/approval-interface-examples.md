# Final approval interface examples

Read only during CEAIA final approval preparation. These preserve the original approval field vocabulary. Use the actual published request_user_approval schema; if it differs, adapt the transport fields without weakening exact payload/file-content binding. Do not apply this reference to the preserved UAT route.

## Required preview content

Create preview: mode, Story/SPEC count, source/project, issue type, full summary/description per issue, description format, optional Epic/parent/association fields, each Story-to-SPEC mapping and attachment content or reviewable change summary, explicit standalone Test Case exclusion, unresolved blockers.

Update preview: source/ticket, unchanged or exact supported replacement summary, full replacement description and baseline change summary, every readable/skipped attachment and reason, each original ID/filename to final filename/relativePath mapping, content change summary for every replacement, omitted unsupported fields, execution order and non-atomic behavior.

Internal score/audit fields never belong in either preview. Missing fields/ambiguity use ask_user_question; authorization uses request_user_approval.

## Original single-write approval shape

Illustrative values only. Build arguments_preview from the actual serialized request, not by copying the example string.

```json
{
  "title": "Approve Jira export?",
  "reason": "Explicit authorization is required before creating issues and uploading attachments.",
  "action_type": "jira_export",
  "target": "WPB/EXAMPLE",
  "summary": "Create the reviewed Story and upload its reviewed SPEC.",
  "risk_level": "medium",
  "details": {
    "export_mode": "ceaia_story_spec_export",
    "jira_source": "WPB",
    "project_key": "EXAMPLE",
    "issue_count": 1,
    "attachment_mapping": [
      {
        "attachment_file_name": "review-spec.md",
        "relative_path": "outputs/ceaia/review/review-spec.md"
      }
    ]
  },
  "approve_label": "Approve Jira export",
  "reject_label": "Reject",
  "require_reject_reason": true,
  "action_binding": {
    "tool_name": "java-base-mcp.pushJiraContent",
    "arguments_preview": {
      "requestJson": "{\"source\":\"WPB\",\"projectKey\":\"EXAMPLE\",\"issueType\":\"Story\",\"summary\":\"Reviewed summary\",\"description\":\"Complete reviewed description\",\"descriptionFormat\":\"markdown\",\"attachments\":[{\"fileName\":\"review-spec.md\",\"relativePath\":\"outputs/ceaia/review/review-spec.md\"}]}"
    }
  }
}
```

No placeholder description may be sent. Before approval bind the actual full request plus every actual upload's final bytes/version in current approval state. Use the shared text-only read-back procedure and actual supported approval arguments. Do not add invented hash parameters or require script execution.

For update batches preserve original action_type=jira_update and tool_name=java-base-mcp.updateJiraTicket. action_binding contains targets_in_execution_order and arguments_preview as an array of exact single-ticket requestJson wrappers, only when the live approval schema supports this binding. Otherwise obtain supported per-operation approvals. A single example wrapper is not authorization for an entire batch.

For explicit Epic-plus-Stories, preview/count the Epic as well and bind the actual combined request. For multiple replacements include every selected file; approving one path never approves later changed bytes at that path.

After rejection, ask a focused revision/stop question only if correction intent is unclear. After asynchronous approval yield until its actual decision; synchronous completed approval may be used immediately. On every resume revalidate content/destination before writes. Partial or unknown success follows ceaia-jira-write-gate.md recovery rather than replaying the approved request.
