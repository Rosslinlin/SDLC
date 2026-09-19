## CEAIA Story/SPEC Update Mode

### Contents

- Supported update surface
- Update source and attachment intake
- Update tool contract and attachment mapping
- Per-ticket payload and preview
- Multi-ticket coordination
- Preflight validation, and result presentation

Use `ceaia_story_spec_update` only for an explicit request to update one or more existing Jira tickets. It is a replacement workflow, not a creation workflow.

The supported update surface is intentionally limited to:

- `summary`, only when the user explicitly supplies the replacement summary or a reliable current source provides it;
- `description`, using the reviewed updated `STORY.md`;
- selecting Jira attachment replacements using reviewed CEAIA-format story-named SPEC files.

Do not send `priority`, `assignee`, `assigneeAccountId`, `labels`, `customFields`, `issueType`, `projectKey`, linked issue fields, parent fields, or attachment deletions in this mode. Omit every unsupported field rather than sending the existing or guessed value.

For each ticket, read the reviewed update deliverables from:

```text
outputs/ceaia/updates/<TICKET-KEY>/STORY.md
outputs/ceaia/updates/<TICKET-KEY>/TEST_CASE.md
outputs/ceaia/updates/<TICKET-KEY>/<story-named-spec>.md
```

Use `.ceaia-work/updates/<TICKET-KEY>/` only for the update manifest, intake evidence, score result, and review reports. `TEST_CASE.md` remains a user-visible workspace deliverable but must not be included in the Jira update payload.

### Update source and attachment intake

Before an update payload is prepared, the generation workflow must, for every target ticket:

1. Read its Jira description and Acceptance Criteria.
2. Call `java-base-mcp.readJiraAttachments` for the full attachment inventory.
3. Use every imported readable attachment as source evidence.
4. Preserve the target ticket's source, ticket key, attachment IDs, original display names, and imported workspace-relative paths in the reviewed update manifest.

Request `description` and `AcceptanceCriteria` separately. The `AcceptanceCriteria` result may legitimately be empty, null, unavailable, not found, or a field-level error because some Stories keep their acceptance statements inside `description`.

- If `description` is non-empty, record the separate Acceptance Criteria status as `empty-or-unavailable`, inspect `description` for embedded acceptance statements, and continue.
- Do not treat an empty separate Acceptance Criteria result as an intake failure by itself.
- A non-empty `description` completes Jira field intake even when it contains no separately identifiable Acceptance Criteria. Any later content-quality gap follows the normal score or review gates.
- If `description` is also unavailable, block the ticket and request the missing description content.

`java-base-mcp.readJiraAttachments` is mandatory before replacing an existing text attachment. Do not invent attachment IDs, use a download URL, or replace an attachment from an unreviewed workspace file.

The attachment reader may skip binary, Office, PDF, archive, oversized, or invalid-UTF-8 files. The update manifest must disclose every skipped attachment and its reason. If a skipped attachment may affect the requested update, ask the user for readable content or a handling decision before review or preview.

If `java-base-mcp.readJiraAttachments` is unavailable, fails, or has no complete result for a target ticket, block that ticket's update. Do not ask the user for an attachment ID as a workaround. Do not prepare a preview, request final approval, or call `java-base-mcp.updateJiraTicket` for that ticket.

### `java-base-mcp.updateJiraTicket` tool contract

Call `java-base-mcp.updateJiraTicket` only after the reviewed preview has been shown and final approval has bound the exact payload.

The tool call has exactly one top-level argument:

```json
{
  "requestJson": "<serialized single-ticket update payload JSON object>"
}
```

The serialized payload must contain:

- `source`: explicitly supplied or confirmed by the user as `WPB`, `ALM`, `DATA`, `FCR`, or `GO`; never inferred from the ticket key or project prefix;
- `ticketKey`: one existing Jira key;
- `description`: the full reviewed replacement Jira description;
- `descriptionFormat`: `markdown`;
- optional `summary`: only when explicitly approved;
- optional `attachments.replace`: only selected attachment IDs from the prior `java-base-mcp.readJiraAttachments` result.

For every `attachments.replace` item, send `attachmentId`, workspace-relative `relativePath`, and reviewed replacement `fileName`. The tool verifies that the attachment belongs to the ticket, uploads the replacement first, and deletes the original only after a successful upload. If replacement deletion fails, the result is partial and both files can remain.

Do not call `java-base-mcp.updateJiraTicket` with naked Jira fields outside `requestJson`. Do not send URLs, absolute paths, path traversal, `attachments.deleteAttachmentIds`, or unapproved fields.

### Attachment mapping and ambiguity

All readable attachments are evidence inputs, but only explicitly selected attachment IDs may be replaced.

- If exactly one readable attachment is clearly the current SPEC, propose it as the replacement target.
- If two or more attachments could be SPECs, including multiple `spec.md`-like files, stop and present selectable filename-based candidates, with MIME type and concise evidence-based purpose when available. Keep the tool-returned attachment IDs internal and use the ID for the filename selected by the user.
- Do not choose based only on file order, most recent timestamp, generic filename, or a guessed semantic match.
- If a selected text attachment is not CEAIA-formatted, its reviewed replacement must be a CEAIA-format story-named `.md` SPEC. Retain the existing attachment ID and replace it only after approval.
- Do not replace two attachments with the same workspace file or collapse two attachments into one replacement unless the user explicitly confirms that mapping.
- If no attachment is a safe SPEC target, ask the user which existing attachment should be replaced or ask for the missing source content. This mode never adds or deletes an attachment implicitly.

Use the exact attachment ID returned by `java-base-mcp.readJiraAttachments`. For every replacement, use the edited imported workspace file or its reviewed final workspace copy and a workspace-relative path. The replacement `fileName` must be the reviewed CEAIA SPEC filename, not necessarily the original attachment's display name.

Never ask the user to type, know, or retrieve an attachment ID. If the attachment reader did not return a usable ID, the update is blocked until attachment reading is completed successfully.

### Per-ticket update payload

Build one JSON payload object per existing Jira ticket. Never place multiple ticket keys in one `java-base-mcp.updateJiraTicket` payload.

```json
{
  "source": "WPB",
  "ticketKey": "WPB-9444",
  "description": "Full reviewed Markdown content from STORY.md",
  "descriptionFormat": "markdown",
  "attachments": {
    "replace": [
      {
        "attachmentId": "10002",
        "relativePath": "outputs/ceaia/updates/WPB-9444/review-term-deposit-spec.md",
        "fileName": "review-term-deposit-spec.md"
      }
    ]
  }
}
```

Include `summary` only when it is an explicitly approved change:

```json
{
  "source": "WPB",
  "ticketKey": "WPB-9444",
  "summary": "Review term deposit details before confirmation",
  "description": "Full reviewed Markdown content from STORY.md",
  "descriptionFormat": "markdown",
  "attachments": {
    "replace": [
      {
        "attachmentId": "10002",
        "relativePath": "outputs/ceaia/updates/WPB-9444/review-term-deposit-spec.md",
        "fileName": "review-term-deposit-spec.md"
      }
    ]
  }
}
```

`description` must be the full reviewed content from the matching `STORY.md`, and `descriptionFormat` must be `markdown`. Do not send a partial patch or append-only text. A replacement uploads the new file before deleting the old attachment; the tool reports partial status if the old attachment cannot then be removed.

### Update preview

For each ticket, show a mandatory Jira Update Preview before final approval. The preview must be built from the exact per-ticket payload that will be serialized into `requestJson`.

The preview must show:

```markdown
## Jira Update Preview

- Update mode: ceaia_story_spec_update
- Jira source:
- Ticket key:
- Summary: [unchanged / current summary not retrieved; user-provided replacement: <new summary>]
- Description: [replace with reviewed STORY.md, including a concise baseline-to-proposed change summary]
- Description format: markdown
- Readable attachment evidence: [all imported attachment IDs and filenames]
- Skipped attachment evidence: [filename, skip reason, or None]
- Attachment replacements:
  - attachment ID:
  - original filename:
  - replacement filename:
  - workspace-relative replacement path:
  - concise content change summary:
- Unsupported fields intentionally omitted: priority, assignee, labels, custom fields, issue type, links, and attachment deletions
- Known unresolved items:
```

Show the exact updated summary when present, the complete updated Jira description, and a reviewable diff or section-level change summary for each replacement SPEC. If the user corrects a ticket, attachment mapping, summary, description, or replacement file after preview, regenerate the affected review output and preview before requesting approval.

If any attachment target, required source evidence, requested behavior, or ticket mapping is ambiguous, ask a focused clarification question. Do not request final approval while an ambiguity remains.

### Multi-ticket update coordination

A request may update multiple existing tickets. Keep a separate manifest, preview, review result, payload, and tool call for each ticket.

After every target ticket has passed review and its preview has been shown, show a batch update manifest listing the execution order, ticket key, summary-change status, description-change status, and attachment replacement IDs. State that `java-base-mcp.updateJiraTicket` is not an atomic batch API.

Use one final `request_user_approval` decision for the complete reviewed batch only when every ticket's payload is known and no ambiguity remains. The approval must bind every exact serialized per-ticket `requestJson` payload in execution order.

After approval, call `java-base-mcp.updateJiraTicket` one ticket at a time in the approved order. If a ticket returns `failed` or `partial`, pause before updating any remaining tickets, report the completed, failed, partial, and unattempted ticket results, and invoke `ask_user_question`. Offer correction and retry, continuation with a newly previewed and approved remaining payload, or explicit stop. Do not retry, remap attachments, or continue with later tickets without a new user instruction and approval for the affected payloads.

For `ceaia_story_spec_update`, use this approval payload shape:

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
    "ticket_count": "<number>",
    "execution_order": ["<WPB-9444>", "<WPB-9445>"],
    "tickets": [
      {
        "jira_source": "<source>",
        "ticket_key": "<ticketKey>",
        "summary_change": "<unchanged or exact replacement summary>",
        "description_format": "markdown",
        "attachment_replacements": [
          {
            "attachment_id": "<attachmentId>",
            "original_file_name": "<original-file-name>",
            "replacement_file_name": "<reviewed-ceaia-spec-file-name>",
            "relative_path": "<workspace-relative edited file path>"
          }
        ],
        "skipped_attachment_evidence": "<none or filenames and reasons>",
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
    "targets_in_execution_order": ["<source>/<ticketKey>"],
    "arguments_preview": [
      {
        "requestJson": "{\"source\":\"<source>\",\"ticketKey\":\"<ticketKey>\",\"description\":\"<full reviewed STORY.md>\",\"descriptionFormat\":\"markdown\",\"attachments\":{\"replace\":[{\"attachmentId\":\"<attachmentId>\",\"relativePath\":\"<workspace-relative edited file path>\",\"fileName\":\"<reviewed-ceaia-spec-file-name>\"}]}}"
      }
    ]
  }
}
```

For each ticket in the update batch, `arguments_preview` must contain the one exact serialized payload that will be passed to `java-base-mcp.updateJiraTicket`. Do not use a representative payload, omit an attachment mapping, or append unapproved fields after approval.

After calling `request_user_approval`, stop the current turn. In the next turn, call the relevant Jira write tool only if the approval decision is approved and every payload still exactly matches the approved payload. For an approved update batch, call `java-base-mcp.updateJiraTicket` one ticket at a time in the approved order.

For `ceaia_story_spec_update`, additionally validate for every target ticket:

- The request was explicitly to update the existing ticket, not create a new Jira item.
- `source` is one of `WPB`, `ALM`, `DATA`, `FCR`, or `GO`.
- The source was explicitly supplied or confirmed by the user for this ticket and matches the persisted ticket-to-source mapping.
- A complete Jira ticket key is present.
- The ticket's Jira description and separate Acceptance Criteria field were requested before generation; an `empty-or-unavailable` Acceptance Criteria result is valid when the non-empty description was used as the authoritative baseline.
- `java-base-mcp.readJiraAttachments` was called and the complete attachment result is represented in the reviewed update manifest.
- The attachment-read result is complete, including an explicit empty attachment list when the ticket has no attachments. If the read tool failed or was unavailable, the ticket is blocked and has no update payload.
- Every imported readable attachment was used as source evidence or recorded as not applicable with an evidence-based reason.
- Every skipped attachment and skip reason is disclosed; any potentially material unreadable attachment can be clarified by the user.
- The update manifest, review result, preview, and final serialized payload all refer to the same ticket key and source.
- `summary` is omitted unless the user explicitly provided the replacement or a reliable source established it.
- `description` is the full reviewed `STORY.md` content and `descriptionFormat` is `markdown`.
- The SPEC embeds that exact reviewed Story content.
- Every replacement uses an attachment ID returned by `java-base-mcp.readJiraAttachments`, a reviewed workspace-relative path without `..`, and a reviewed CEAIA-format `.md` filename.
- Every ambiguous multiple-SPEC or multiple-attachment mapping was explicitly resolved by the user before review and approval.
- User attachment choices were collected by filename-based selection from the attachment-reader result; no Jira attachment ID was requested as user-provided input.
- No `attachments.deleteAttachmentIds`, `attachments.add`, `priority`, `assignee`, `assignee account ID`, `labels`, `custom fields`, `issue type`, `project key`, linked issue, or parent fields are present.
- The complete preview and batch manifest were shown, and the final approval binds every per-ticket `requestJson` in execution order.

For `ceaia_story_spec_update`, return:

1. Batch status: success, partial, failed, or blocked.
2. One result row per ticket in execution order, including source, ticket key, updated summary status, description status, and each attachment replacement result.
3. The exact ticket where processing stopped, if a failure or partial result occurs.
4. The remaining ticket keys that were not attempted.
5. A focused `ask_user_question` popup for correction, retry, newly approved continuation, or explicit stop after a failed or partial ticket.
6. A reminder that the reviewed workspace artifacts remain available after the Jira update.

Created Jira issue links must be displayed one by one. Do not summarize created issues as a range, starting key, ending key, or continuation statement. Never use wording such as “Created tickets begin at PHH-3637 and continue through PHH-3658.” If multiple issues are created outside `uat_testcase_export`, list every returned Jira key or URL on its own bullet or table row, for example:

```markdown
- [PHH-3637](<jira-url-for-PHH-3637>)
- [PHH-3638](<jira-url-for-PHH-3638>)
- [PHH-3639](<jira-url-for-PHH-3639>)
```

If only Jira keys are returned and no URLs are available, still list every created key individually. For `uat_testcase_export`, convert each returned key into the full browse link format above. Do not collapse sequential keys into `PHH-3637 through PHH-3658`, `PHH-3637 - PHH-3658`, `PHH-3637+`, or similar shorthand.

If export is skipped or blocked, do not claim Jira issues were created.

A skipped, blocked, failed, partial, rejected, or awaiting-approval update is not workflow completion. Keep the task active through `ask_user_question` until all intended ticket updates succeed or the user explicitly stops.
