# Final approval tool-call contract

Read this file before every CEAIA final approval. It restores the original platform approval-popup vocabulary and mode-specific payloads.

## Invocation rule

After showing the complete preview and resolving all ambiguity, invoke the actual `request_user_approval` capability with one JSON object in the applicable shape below.

- This is a tool call, not text to show the user. Never merely print `request_user_approval`, its JSON, or “waiting for request_user_approval”.
- Do not ask the user to type approve/continue and do not use `ask_user_question` for final authorization.
- Replace every placeholder with the actual previewed value. `action_binding.arguments_preview` contains the exact final wrapper(s), including the complete serialized `requestJson`; never use a shortened representative payload.
- Call approval only after the preview is final. Any payload, destination or uploaded-content change invalidates the decision and requires a new preview and approval call.
- After an asynchronous approval call, end/yield the current turn. On resume, act only on the actual decision returned by that call. A synchronous completed decision may be handled immediately.
- If this capability is not callable in the runtime, do not call either Jira write tool. Return exactly one actionable blocker: `WAITING_TOOL: request_user_approval is not exposed by the platform task/runtime`. Text instructions cannot create a missing popup capability.
- Rejected means no Jira write. Apply any supplied correction, rebuild the preview, then call approval again only for changed final arguments. If rejection contains no correction, use a focused `ask_user_question` for revise/defer/stop.

These shapes are the contract supplied by the original Skill. Do not replace them with a guessed schema. If the platform owner explicitly provides a newer live schema, update this contract rather than asking the model to discover one at runtime.

## CEAIA Story/SPEC creation approval

For one or more creation payloads, invoke `request_user_approval` using:

```json
{
  "title": "Approve Jira export?",
  "reason": "Explicit authorization is required before creating Jira issue(s) and uploading attachment(s).",
  "action_type": "jira_export",
  "target": "<source>/<projectKey>",
  "summary": "Create <issue_count> Jira issue(s) in <projectKey> and upload approved attachment(s) where applicable.",
  "risk_level": "medium",
  "details": {
    "export_mode": "ceaia_story_spec_export",
    "issue_count": 1,
    "jira_source": "<source>",
    "project_key": "<projectKey>",
    "issue_type": "Story",
    "description_format": "markdown",
    "epic_link_required": false,
    "epic_key_or_mapping": "none",
    "attachment_mapping": [
      {
        "issue": "<actual Story title>",
        "attachment_file_name": "<actual SPEC filename>",
        "relative_path": "<actual workspace-relative SPEC path>"
      }
    ],
    "known_unresolved_items": "none"
  },
  "approve_label": "Approve Jira export",
  "reject_label": "Reject",
  "require_reason": true,
  "action_binding": {
    "tool_name": "java-base-mcp.pushJiraContent",
    "target": "<source>/<projectKey>",
    "arguments_preview": {
      "requestJson": "<exact complete serialized creation payload>"
    }
  }
}
```

For several destination groups, use the approval runtime's supported grouping only when the actual tool schema supports multiple bound calls. Otherwise request one approval per exact creation call. Never treat one group's approval as authorization for another source/project.

For explicit Epic-plus-Stories, include the Epic in issue_count/details and bind the actual combined request. Do not add an Epic implicitly.

## Existing Jira update approval

After every selected ticket has a final payload and the non-atomic execution order has been shown, invoke `request_user_approval` using:

```json
{
  "title": "Approve Jira ticket update batch",
  "reason": "Explicit authorization is required before updating existing Jira ticket fields and replacing selected attachments.",
  "action_type": "jira_update",
  "target": "<source>/<ticketKey>, ...",
  "summary": "Update <ticket_count> existing Jira ticket(s) with reviewed descriptions and selected CEAIA SPEC attachment replacements.",
  "risk_level": "medium",
  "details": {
    "update_mode": "ceaia_story_spec_update",
    "ticket_count": 1,
    "execution_order": [
      "<source>/<ticketKey>"
    ],
    "tickets": [
      {
        "jira_source": "<source>",
        "ticket_key": "<ticketKey>",
        "summary_change": "<unchanged or exact replacement summary>",
        "description_format": "markdown",
        "attachment_replacements": [
          {
            "attachment_id": "<actual attachment ID>",
            "original_file_name": "<actual original filename>",
            "replacement_file_name": "<actual reviewed SPEC filename>",
            "relative_path": "<actual workspace-relative SPEC path>"
          }
        ],
        "skipped_attachment_evidence": "none",
        "known_unresolved_items": "none"
      }
    ],
    "batch_atomicity": "java-base-mcp.updateJiraTicket runs one ticket at a time. If a ticket fails or is partial, remaining approved tickets will not be updated until the user gives a new instruction."
  },
  "approve_label": "Approve Jira updates",
  "reject_label": "Reject",
  "require_reject_reason": true,
  "action_binding": {
    "tool_name": "java-base-mcp.updateJiraTicket",
    "targets_in_execution_order": [
      "<source>/<ticketKey>"
    ],
    "arguments_preview": [
      {
        "requestJson": "<exact complete serialized payload for this ticket>"
      }
    ]
  }
}
```

For description-only updates, keep `attachment_replacements` empty and omit attachments from the bound Jira payload. For multiple tickets, include one exact `arguments_preview` wrapper per ticket in the displayed execution order. After approval, call `java-base-mcp.updateJiraTicket` one ticket at a time; approval of the batch does not make the write API atomic.

If the live approval capability cannot bind an array, use one popup per exact ticket payload. Never collapse several update payloads into one representative wrapper.

## Final pre-write check

Immediately before a Jira write:

1. Approval decision is actually `approved`, not inferred from chat.
2. Tool name, target, exact `requestJson`, execution order and current upload content still match the approved call.
3. No unresolved item is hidden from details.
4. Use only `{"requestJson":"<exact approved serialized object>"}` as the Jira write call argument.
5. A rejected, unavailable, expired or stale approval never authorizes a write.
