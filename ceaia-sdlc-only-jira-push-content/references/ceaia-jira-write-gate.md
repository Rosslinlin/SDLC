# CEAIA Jira write gate

Applies only to `ceaia_story_spec_export` and `ceaia_story_spec_update`. UAT and generic callers retain the original common-jira-write-gate.md; do not load this reference for those modes.

## C1 — Mode and supported interface

Creation/export: java-base-mcp.pushJiraContent. Existing update: java-base-mcp.updateJiraTicket. Read the actual published schema before calling; do not invent optional fields or recovery endpoints.

```json
{
  "requestJson": "{\"source\":\"WPB\",\"projectKey\":\"EXAMPLE\",\"issueType\":\"Story\",\"summary\":\"Example\",\"description\":\"Example description\"}"
}
```

This is a syntactically complete transport example, not permission to use example values. requestJson must parse once to the approved object; no naked Jira fields outside it.

| Mode | Required destination | Artifact contract |
|---|---|---|
| CEAIA create | confirmed source, projectKey | current reviewed Story, exactly one matching SPEC |
| CEAIA update | confirmed source, existing ticketKey; no projectKey payload field | current reviewed replacement description, optional supported summary, none/replace attachments |

Source options: WPB, ALM, DATA, FCR, GO. Confirm per target; do not infer from key/project/another source. Reuse explicit current-request confirmation. Missing mappings are collected first, independently per target. Ticket/source errors ask for correction/retry/stop; never probe other sources automatically.

## C2 — Field collection

Use staged collection:
1. Missing source only, before Jira access.
2. Project/destination and missing supported fields, grouped in one focused popup where known.
3. Conditional fields (e.g. Epic linking selected after destination) when that decision becomes applicable.

Each field has its own labeled control; constrained options are selectable. Do not request unsupported UI features if the platform cannot render tabs; independent controls are sufficient. Do not repeat already answered fields. Blank/invalid answers get a focused correction, not a restarted form.

Creation requires issueType/summary/description; CEAIA type is Story. Update does not collect projectKey/type/assignee/priority. Identity/authentication follow the logged-in tool runtime; do not collect secrets.

Epic is optional: reuse explicit choice or ask whether to link when unknown; existing-Epic linking needs an actual key. If the user explicitly asks to create a new Epic with Stories, use work-item-field-mapping.md instead of asking for a nonexistent key. Do not create an Epic implicitly. Additional create fields/links require explicit requested scope or supported evidence and final preview.

## C3 — Artifact currentness and approval binding

During final preparation read [approval-interface-examples.md](approval-interface-examples.md) as the required final approval tool-call contract. Use its original mode-specific field vocabulary unless the platform owner has explicitly supplied a newer contract.

CEAIA: read current state, current full review and actual artifact bytes. Check SCORE and review gates are bound to current Story/Test Case/SPEC body; header is synchronized; global selected-source coverage is complete. Perform the shared text-only verification checklist; it does not replace independent source review.

Save exact requestJson string(s) in the current `.ceaia-work/jira-request.json`; record requestRevision, indices, execution order and each upload's path/fileRevision in compact approval state. Read the complete current request and attachment content for preview. After approval make no edits, re-read and compare with what was approved, then send the same payload. A difference invalidates approval and affected gates. Follow workflow-contract.md for uncertain currentness or concurrent edits; do not claim atomic/cryptographic immutability and do not require any local hash tool.

Show actual business summary/description, destination and attachment plan/content or reviewable changes. No score diagnostics/audit fields in preview or payload. Internal paths used solely as transport attachment references are permitted, not embedded in description.

Invoke `request_user_approval` as a tool call using the applicable complete JSON shape in the approval contract. Do not print its name or arguments as prose. Binding must include actual complete arguments, not a representative/truncated example. A batch binds each serialized payload and reviewed attachment content/revision in order. Information collection via `ask_user_question` is not approval.

If rejected, use any correction to determine affected gates; ask a focused revise/defer/stop question only if needed. If unavailable, WAITING_TOOL; do not substitute chat approval. Never immediately re-prompt identical approval without a change or user direction.

Changes after preview/approval:
- Story/business content → return generation for invalidation/score as applicable and independent review.
- Test Case/SPEC content → affected generation/review, not unchanged Story scoring.
- Attachment target/source/identity → preservation/destination review as affected.
- Metadata-only supported destination change → revalidate compatibility/preview/approval.
Every approved-content change invalidates approval, even when its path/requestJson string stays the same.

## C4 — Serialization and paths

Construct object once, serialize once, parse to verify equality. Description copied from Markdown uses descriptionFormat=markdown. No hand-truncated descriptions or JSON strings.

Uploads use actual filename and workspace-relative relativePath; reject absolute paths, URL schemes, '..', artifact IDs, blobs and base64. Check file exists/is readable and belongs to the approved candidate. No standalone TEST_CASE.md, score, planning, state or review attachment. SPEC alone is the CEAIA business attachment.

## C5 — Write outcomes and recovery

Track per target and operation: not_attempted, confirmed_success, confirmed_failure, partial or unknown; store actual returned keys/IDs and issue-vs-attachment outcomes. Never assume a generic success flag proves all attachments completed.

On failed/partial/unknown result, pause remaining writes, report known success and outstanding actions and ask for a supported recovery decision. Before retry:
1. Reconcile actual remote state via supported reads/query/status and returned IDs.
2. Preserve known completed work. Creation succeeded but upload failed means never replay create.
3. Replacement upload succeeded but old deletion failed means never blindly upload again.
4. Unknown timeout is not confirmed failure; do not replay until outcome is known or runtime offers documented idempotent recovery.
5. Use only supported compensation operation for unfinished work and new exact preview/approval. If API cannot express it safely, return WAITING_TOOL for platform/manual recovery; do not invent an endpoint or silently switch helpers.

Approving a retry does not establish idempotency. No automatic retries of uncertain writes. Refresh attachment inventory/baseline when remote changes or partial replacement make old mapping stale; a previous completed read is not a lifetime prohibition on reconciliation.

## C6 — Report and completion

Report each key/URL separately, never ranges. Include description/summary/attachment outcomes and unattempted targets. Do not construct unverified URLs or claim creation/update without confirmation.

Source evidence stays available; generated documents are current-only. All intended confirmed successes complete the workflow; otherwise return waiting/stopped status and exact next action. No busy waiting.
