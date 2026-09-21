# CEAIA existing-ticket update

Read shared workflow-contract.md and ceaia-jira-write-gate.md. Use source-qualified paths from current state.

## U1 — Allowed changes

Only reviewed description replacement, explicitly supported replacement summary, and selected SPEC attachment replacements. Omit projectKey, issueType, priority, assignee, labels, custom fields, links, parents, add and standalone delete actions.

Require original description, separately requested AC status, completed attachment inventory, all relevant readable evidence and resolved skipped-evidence concerns. Empty/unavailable separate AC is acceptable only when description is usable; retain actual AC status when returned. No complete description/inventory means no payload.

Verify reviewed preservation manifest, SCORE/current review, candidate identity and exact file bindings. Reconcile known concurrent Jira changes before applying a full description replacement. Read current baseline with supported read tools before writing when it may have changed; materially different baseline requires reconciliation, not blind overwrite.

## U2 — Attachment actions

none: mapping empty; do not include attachments in payload or require attachment selection. Story explicitly states no attachment change; its generated local SPEC is not uploaded or named as a new Jira attachment. updateReady may be true.

replace: use only reader-returned IDs selected by filename, exact reviewed output paths and final fileNames. Ambiguous targets require user selection. Multiple originals mapped to the same SPEC require explicit mapping confirmation and reviewed preservation of all relevant obligations. Distinct replacement contents use distinct reviewed files in the same ticket's attachments.replace array. Verify every state.specArtifacts entry, reviewedSpecArtifacts binding and manifest mapping; never silently collapse files or force separate Jira tickets.

No safe target for an intended replacement blocks it; do not add an attachment or silently switch action. Never modify imported source evidence.

Tool contract: replacement uploads new content before deleting the selected original; partial deletion can leave both. This authorized replacement is distinct from standalone attachment deletion.

## U3 — Exact payload

No-attachment example:
```json
{
  "source": "WPB",
  "ticketKey": "EXAMPLE-123",
  "description": "Actual complete reviewed STORY.md with no attachment-change reference",
  "descriptionFormat": "markdown"
}
```

Replacement example:
```json
{
  "source": "WPB",
  "ticketKey": "EXAMPLE-123",
  "description": "Actual complete reviewed STORY.md content",
  "descriptionFormat": "markdown",
  "attachments": {
    "replace": [
      {
        "attachmentId": "10002",
        "relativePath": "outputs/ceaia/updates/WPB/EXAMPLE-123/review-application-spec.md",
        "fileName": "review-application-spec.md"
      }
    ]
  }
}
```

Example IDs are not usable evidence. Summary is omitted unless expressly supported and approved; current summary must not be guessed. No append-only patch: description is full reviewed Story. Each call is one ticket, exactly {"requestJson": "<serialized approved object>"}.

## U4 — Preview, approval and batch execution

Per ticket show source/key, summary unchanged or exact replacement, full proposed description and baseline change summary, readable/skipped attachment evidence, attachmentAction, original filenames and selected replacement filenames/paths, reviewable SPEC changes, and unsupported fields omitted. No score/audit fields.

After all selected tickets are current PASS, show the full batch order and non-atomic execution statement. Load and invoke the exact update shape in [approval-interface-examples.md](approval-interface-examples.md), binding every per-ticket requestJson and final uploaded-file content/revision. If the approval capability cannot bind an array, use one actual popup call per exact ticket payload; never print the tool name as a substitute or pretend one wrapper covers the batch.

After approval execute one ticket at a time in approved order. Recheck exact arguments and file bytes before each call. Any changed content or destination invalidates applicable gates/approval. Asynchronous approval yields until actual result; no chat assent substitutes.

## U5 — Recovery and output

At first failed/partial/unknown ticket, pause remaining tickets. Reconcile description and attachment operations before offering a bounded supported retry. Refresh attachment inventory where replacement may have changed IDs. Do not blindly upload again or replay the original full payload after partial success. If API cannot express required compensation, retain WAITING_TOOL with exact completed/pending steps.

Continuation for remaining targets requires explicit direction and newly previewed approved remaining payloads. Report each ticket, source, summary/description/attachment status and unattempted remainder. No issue-link ranges, false success or unbounded polling.
