# Core Rule

## Contents

- Core write contract and supported modes
- Mode selection and common Jira fields
- Field collection
- Attachment and description rules
- Final approval gate
- Preflight validation
- Result presentation and guardrails

Use this skill before any Jira creation, Jira export, testcase upload, work item push, Story creation, Epic creation, Task creation, attachment upload, existing-ticket update, or attachment replacement.

This skill owns the final Jira write gate for all supported Jira creation and update modes.

Use `java-base-mcp.pushJiraContent` only for creation/export modes. Use `java-base-mcp.updateJiraTicket` only for existing-ticket update mode. Do not call lowercase or alternate tool-name variants.

All final `java-base-mcp.pushJiraContent` and `java-base-mcp.updateJiraTicket` calls must use exactly one argument named `requestJson`. The `requestJson` value must be a serialized JSON object string.

Build and approve the Jira payload as a JSON object first, then call the tool with this wrapper shape:

```json
{
  "requestJson": "<serialized approved Jira payload JSON object>"
}
```

Do not pass Jira fields as naked tool arguments. Do not call either Jira write tool with root-level fields outside the serialized `requestJson` string.

Do not call either Jira write tool until:

- The export mode has been identified.
- Required Jira fields for that mode are available.
- The mode-specific upload preview or manifest has been shown.
- The user has confirmed the preview or manifest where required.
- Technical blocker checks have passed or payload-affecting technical warnings have been explicitly disclosed.
- The user explicitly approves the final Jira creation or update through `request_user_approval`.

Do not use `ask_user_question` for final approval, upload authorization, or any approval decision. Use `ask_user_question` for missing required information, blocker clarification, correction, retry, continuation after a partial operation, or explicit stop decisions.

If `request_user_approval` is unavailable, do not call Jira write tools. Show the mode-specific summary and invoke `ask_user_question` to let the user retry later, correct the environment, or explicitly stop. Keep the workflow active.

## Supported Export Modes

| Mode | Source rule | Jira content | Primary artifacts |
| --- | --- | --- | --- |
| `uat_testcase_export` | Generated test case upload rules | Jira Test issue(s) | Runtime-generated UAT testcase content handed off from `uat-test-case-generator` or explicitly selected by the user |
| `ceaia_story_spec_export` | Jira work item and attachment rules | Jira Story issue(s) plus matching SPEC attachment(s) | `STORY.md` and story-named SPEC files |
| `generic_work_item_export` | Explicit generic Jira work item request | Jira Story, Task, or Epic, optionally with attachments | User-provided Jira work item content |
| `ceaia_story_spec_update` | Existing-ticket update rules | Existing Jira ticket summary and description, plus selected CEAIA SPEC attachment replacement(s) | Reviewed per-ticket `.ceaia-work/updates/<TICKET-KEY>/update-manifest.md`, `outputs/ceaia/updates/<TICKET-KEY>/STORY.md`, and reviewed replacement SPEC files |

Use `uat_testcase_export` for generated testcase export. Use `ceaia_story_spec_export` for CEAIA Story/SPEC export. Use `generic_work_item_export` only when the user explicitly asks to create generic Jira work items outside the CEAIA Story/SPEC workflow.

Use `ceaia_story_spec_update` when the user explicitly asks to update one or more existing Jira tickets and their corresponding CEAIA SPEC attachments.

## Mode Selection

Select `uat_testcase_export` when:

- The user asks to export generated UAT test cases to Jira.
- The source artifact is generated testcase content.
- The Jira issue type is fixed to `Test` for generated testcase content.
- The payload requires testcase fields such as `summary`, `content`, `steps`, `result`, and `data`.

Select `ceaia_story_spec_export` when:

- The source artifacts are reviewed CEAIA `STORY.md` and story-named SPEC files.
- The user asks to create Jira Stories with SPEC attachments.
- The Jira description should come from `STORY.md` and the SPEC should be uploaded as an attachment.

Select `generic_work_item_export` when:

- The user explicitly asks to create a Jira Story, Task, or Epic outside the CEAIA Story/SPEC workflow.
- The request is not a generated testcase export.

Select `ceaia_story_spec_update` when:

- The user supplies one or more existing Jira ticket keys and asks to add, revise, or remove supported requirements in those tickets.
- The update changes only summary when explicitly provided, Jira description, and selected attachment replacements. It does not update priority, assignee, labels, custom fields, linked issues, or issue type.

At mode-selection time, an explicit existing-ticket update request and ticket key are sufficient. Do not require an existing CEAIA SPEC, an attachment ID, a reviewed manifest, or an attachment replacement mapping before selecting this mode. Those are mandatory preparation outputs after `java-base-mcp.readJiraAttachments` has completed.

If the mode cannot be safely identified, ask one concrete blocker question naming the candidate export targets. Do not guess between testcase export and work item export. Mode-specific rules do not bleed into each other:

- Testcase export uses fixed `issueType: "Test"`, uses user-selected `uploadType: "multiple"` or `uploadType: "single"`, and maps the payload according to the selected upload type. Do not ask the user to select `issueType` for UAT testcase export.
- Work item export uses Story, Task, or Epic payloads, and may include attachments.
- Work item attachment rules do not apply to testcase exports unless the user explicitly asks for supported work item attachments.
- Testcase `steps` array rules do not apply to Story/SPEC attachment export.
- Existing-ticket update does not create Jira issues, does not use `projectKey`, and does not use creation payload shapes.

## Common Jira Fields

Common required root fields:

- `source`
- `projectKey`

Supported `source` values:

- `WPB`
- `ALM`
- `DATA`
- `FCR`
- `GO`

Do not invent `source`, `projectKey`, `assignee`, `epicKey`, linked issue keys, ticket keys, or attachment IDs.

Do not infer or default Jira `source` from a ticket prefix, project key, prior tool behavior, organization context, or another ticket. Before Jira preparation, require the user to explicitly supply or confirm `WPB`, `ALM`, `DATA`, `FCR`, or `GO` for every ticket. For multiple tickets, collect and preserve a per-ticket source mapping in one structured popup with one independent constrained selector per ticket; tickets may belong to different sources and no shared default may be applied.

If a Jira tool returns not-found or another source-related error, preserve the exact tool error and use `ask_user_question` to reconfirm or correct that ticket's key and source. Never probe, retry, or cycle through other Jira sources automatically.

Treat missing source as a hard preflight gate. The next and only interaction must be a structured source popup with one independent `WPB | ALM | DATA | FCR | GO` selector per unresolved ticket. Do not combine unrelated fields or questions into this popup, call a Jira tool, or proceed until the mapping is confirmed.

Use this popup contract:

```text
Missing source:
  Problem: Jira source is required before ticket lookup.
  <ticket-key> source: [WPB | ALM | DATA | FCR | GO]
  Action: Confirm sources or stop.

Source-related read error:
  Error: <exact tool error>
  Ticket: <ticket-key>
  Confirmed source: <source>
  Choose: correct ticket key | correct source | retry same mapping | stop
```

Do not ask the user to provide the numeric suffix for a newly created Jira ticket. The caller supplies `projectKey`; Jira assigns the numeric suffix during creation. For an existing-ticket update, require the full existing ticket key.

Identity and authentication are resolved by `java-base-mcp.pushJiraContent` from the current logged-in request and selected Jira source.

## Field Collection Rules

Collect all missing user-fillable Jira field parameters in one structured popup or selection UI before payload preview or upload approval.

Do not ask two fields first, continue execution, and later ask for more Jira fields. If multiple required or user-fillable fields are missing, the single field-collection interaction must include every missing field needed for the selected export mode.

The field-collection popup must enforce one tab per Jira field. Each tab must be labeled for exactly one Jira field, contain exactly one independent control for that field, and must not collect or imply any other Jira field.

Each Jira field must have exactly one independent control. Do not combine multiple Jira fields into one input, textarea, JSON box, CSV box, natural-language prompt, or option set.

Do not use one shared tab, shared input box, shared textarea, shared JSON box, shared CSV box, shared natural-language prompt, or shared option set to collect multiple required fields.

Invalid shared-popup examples:

- `source=WPB, projectKey=PHH`
- `projectKey PHH assignee 12345678`
- JSON objects containing multiple Jira fields
- CSV rows containing multiple Jira fields
- A multiline key-value block for several Jira fields

Constrained fields must be selectable options, not free text. `source` must show `WPB`, `ALM`, `DATA`, `FCR`, and `GO`.

Free-text values such as `projectKey`, `assignee`, and `epicKey` may use text inputs, but each must have its own separate field and control.

If the user's response leaves a required field blank, invalid, or contradictory, ask one follow-up blocker question only for the invalid or unresolved field. Do not restart the full collection flow.

## Attachment Rules

Attachments are supported for work item exports.

Attachments must contain a workspace file relative path copied from workspace context or workspace tools. Use `relativePath`; `path` and `filePath` may be accepted by the tool but this skill should prefer `relativePath`.

A correct `relativePath`:

- Is relative to the current conversation workspace root.
- Does not start with `/`.
- Does not include URL schemes.
- Does not include `..` path traversal.
- Points to the intended attachment file for the corresponding Jira issue.

Do not pass these as attachment references:

- artifactId
- base64Content
- frontend blob URLs
- HTTP URLs
- data URLs
- file URLs
- absolute filesystem paths
- paths containing `..`

Attachments are uploaded only after the corresponding Jira issue is created successfully. Attachment failures may make the issue status partial but do not delete the created Jira issue.

## Description Formatting

The Jira write tools send `description` into Jira. Jira may render this field as Jira wiki markup.

By default, `description` is sent unchanged except unsupported code macro formatters may be normalized.

If `descriptionFormat` is `markdown` or `md`, the tool converts common Markdown to Jira wiki markup before creation or update.

Use `descriptionFormat: "markdown"` when `description` is copied from a Markdown workspace file such as `STORY.md`.

The converter handles common Markdown headings, bullet and numbered lists, fenced code blocks, inline code, links, and block quotes.

For Jira wiki markup descriptions, omit `descriptionFormat`.

## Final Approval Gate

Use `request_user_approval` for final export or update approval.

Do not ask for approval as plain chat text.

Do not use `ask_user_question` for this approval.

Do not call `java-base-mcp.pushJiraContent` or `java-base-mcp.updateJiraTicket` unless the final approval decision is approved.

If the approval decision is rejected:

- Do not call either Jira write tool.
- If the rejection reason contains corrections or extra instructions, incorporate them and prepare a fresh preview or manifest.
- If no actionable correction is supplied, invoke `ask_user_question` to ask whether to revise the payload or explicitly stop.
- Request approval again before any Jira upload.

If any Jira creation field, Epic link, attachment path, selected testcase list, linked issue, label, ticket key, update summary, update description, attachment ID, attachment replacement mapping, or other payload-affecting option changes after approval, request approval again before calling the relevant Jira write tool.

## Validation Before Jira Write

Before calling either Jira write tool, validate for all modes:

- Export mode is identified.
- Export flow was triggered by explicit user request or clean mode-specific handoff.
- Required fields are available.
- Preview or manifest has been shown.
- User has confirmed the preview or manifest where required.
- Final approval was granted by `request_user_approval`.
- Payload matches the approved preview or manifest.
- Payload is serialized into the single `requestJson` tool argument.
- No Jira fields are passed as naked tool arguments.
- `source` is one of `WPB`, `ALM`, `DATA`, `FCR`, or `GO`.
- `projectKey` is present for creation/export modes.
- No internal artifact ID, blob URL, temporary URL, absolute path, or unsafe attachment path appears in Jira-facing fields.
- Known unresolved technical blockers are none.

A clean CEAIA review handoff is sufficient to start Jira preparation. Do not ask for a plain-chat `continue` confirmation before field collection, preview, manifest creation, or `request_user_approval`.

## Result Presentation

After a Jira export or update, return:

1. Export status: success, partial, failed, or blocked.
2. `source` and `projectKey` used.
3. Fixed `issueType: "Test"`, `assignee`, and selected `uploadType` when using testcase export.
4. Total number of created Jira issues.
5. Full created Jira issue list when returned by the tool.
6. Each created issue as a clickable Markdown link when a URL or browsable key is available.
7. Failures and reasons when partial failures occur.
8. Attachment upload results for work item exports.
9. For export modes, a reminder that Jira export did not change the saved source artifact.

After any partial, failed, rejected, blocked, invalid, or unverified result, invoke `ask_user_question`. State what succeeded, what failed, what remains unattempted, and what correction or choice is required. Offer retry or correction where valid and always offer explicit stop. These states do not complete the bundled SDLC workflow.

## Guardrails

- Treat explicit Jira export intent as a fast path to export preparation, not as upload approval.
- Treat explicit Jira update intent as a fast path to update preparation, not as update approval.
- Do not create or update Jira without `request_user_approval`.
- Do not use `ask_user_question` as approval.
- Do not invent required Jira fields.
- Do not invent Epic tickets or linked issue keys.
- Do not ask for Jira ticket numeric suffixes.
- Do not mix testcase payload fields with work item attachment payload fields.
- Do not silently truncate, abbreviate, cap, split, or paraphrase testcase execution fields.
- Do not treat a successful Jira issue-creation response as proof that `steps`, `result`, or `data` was preserved when those fields were not returned for verification.
- Block export on any source-to-payload mismatch or numbered-item count mismatch.
- Do not continue under unresolved blockers in Jira fields.
- Do not pass `label` or `labels` in work item payloads.
- Do not attach standalone CEAIA `TEST_CASE.md`.
- Do not include standalone CEAIA `TEST_CASE.md` in Jira payload.
- Do not paste full SPEC content into Jira description for CEAIA Story/SPEC exports.
- Do not force attachment file name to `SPEC.md`.
- Use the actual attachment file name as `fileName`.
- Use workspace-relative paths only.
- Do not use artifact IDs, blob URLs, temporary URLs, HTTP URLs, data URLs, file URLs, uploaded document paths, or absolute paths as attachment references.
- If approved payload changes, request approval again.
- Do not update priority, assignee, labels, custom fields, issue type, links, parents, or attachment deletions in `ceaia_story_spec_update`.
- Do not infer an existing Jira summary or send a summary replacement unless it is explicitly supported.
- Do not ignore readable Jira attachments. Review every readable `.md`; use them as source evidence.
- Do not replace ambiguous attachments. Use `ask_user_question` to let the user select attachment filenames and one-to-one mappings from the attachment-reader inventory; retain the corresponding attachment IDs internally and never ask the user to type them.
- Do not treat multi-ticket updates as atomic. Pause before remaining tickets after the first failed or partial update and invoke `ask_user_question` for correction, retry, newly approved continuation, or explicit stop.
