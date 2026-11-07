# Existing-Jira score synchronization, persistence and recovery

Read after a direct Jira score response, for a score-sync/recovery request, or before returning a scored JSON to its caller. This reviewer synchronizes existing-Jira scores through java-base-mcp.exportjirabydynamicfield. Business-content export, attachments and local-JSON association remain with the separately maintained export workflow.

## Preserve the actual assessment

Bind each result to the input actually sent to nodejs-base-mcp.scoreUserStory:

| Assessment source | Evidence to retain |
| --- | --- |
| Existing Jira | Exact jira_number and confirmed source, complete response/error and actual returned identifiers/cache information. A source-file carrier is provenance only, not the scoring input. |
| Workspace JSON | Exact story_json_workspace_path and observed saved content/revision; actual optional SPEC input pair and file binding when used; complete response/error and returned identifiers/cache/checksum information. Retain a known intended Ticket/source separately. |

Use the existing versioned score-result file and conversation context. Preserve every earlier assessment; do not create an extra state, manifest or handoff file. Keep the complete response unchanged. Parse it for the report without dropping unknown fields, truncating diagnostics, selecting a favorable nested result or recalculating finalScore. Preserve raw returned text when available; otherwise serialize the complete decoded object and label that representation honestly.

Returned service identifiers and checksums are evidence, not values for the model to manufacture. Trusted platform context supplies session identity; do not infer it from a path, request credentials, or add identity/session arguments to scoring. Profile configuration can be reused across conversations; a saved profile is not a score record for another session or JSON version.

For JSON without a SPEC, retain the actual one-argument call. Do not invent SPEC status/path values. When a real SPEC and its actual status were supplied, retain both exact inputs and the file content/revision used; a score-only file is not automatically an upload authorization.

## Synchronize a direct Jira assessment

Current-Jira review includes one score-sync submission after scoreUserStory returns boolean ok=true and finite numeric finalScore, including low scores, 0 and cache hits. Use the same selected primary ticket and confirmed uppercase source as the actual scoring call. Honor an explicit assessment-only/no-Jira-write request. Do not ask again whether to synchronize when that action is already included in the authorized workflow.

Call java-base-mcp.exportjirabydynamicfield with exactly these two direct arguments:

```json
{
  "jira_number": "EXAMPLE-123",
  "source": "WPB"
}
```

The argument names and values match the existing-Jira scorer, including source casing. Do not substitute almType/issueIdOrKey, lowercase the source, wrap in requestJson, or add staffId, conversationId, a JSON/Markdown path, SPEC parameters, manual scores, review IDs, fields or attachments. Platform identity remains platform-owned. This is the confirmed new score-sync contract, not the historical four/five-field binding or the JSON-path business-content export mode.

Apply these route/outcome rules:

| Actual assessment/request | Action |
| --- | --- |
| Completed direct Jira assessment, no excluded writes or prior sync submission | Submit the two-argument score sync once and retain its full response/error. No query/profile/JSON preparation is needed. |
| Only finish score synchronization for a verified prior direct Jira assessment | Reuse its actual ticket/source and score evidence from calls, a result record or confirmed context; do not call the scorer. A local result file is not mandatory. Ask only if the referenced assessment or source is unresolved. |
| Selected local JSON assessment, even with issueIdOrKey | Do not use ticket/source synchronization. Keep its scored JSON unchanged for the content export/association owner. A matching ticket alone does not establish a direct Jira assessment. |
| Scoring failed, VALIDATION_FAILED, SKIPPED, missing usable score or unknown scoring outcome | Preserve the actual outcome; do not submit score synchronization or fabricate a numeric score. |
| This assessment's sync was already successful, accepted or pending | Retain that outcome; do not submit again because a Jira UI update or queue ID is absent. |
| Sync failed or its outcome is unknown | Preserve the completed score and failed/unknown sync evidence. No automatic retry or rescoring. Follow the recovery rules below. |

If the tool is unavailable, report the precise sync blocker while keeping the assessment result. The two-argument API cannot select a historical review ID or manually supplied score; do not promise which historical record it selects. If the intended prior assessment cannot be established or a competing/newer assessment makes recovery ambiguous, resolve that with supported evidence instead of manufacturing an identifier or rescoring to avoid the ambiguity.

Append the exact sync arguments and complete response/error to the matching existing result record, separately from the unchanged scoring response, then read it back. For a sync-only request without a result file, preserve actual tool evidence in conversation context; do not manufacture a new score-result file or extra manifest. A local save failure retries that save only.

## Report assessment and downstream outcomes separately

A valid numeric assessment, including a low score, zero or a cache hit, is a completed assessment under [scoring rules](scoring-and-results.md). Missing optional synchronization fields do not turn it into an endless pending review.

Interpret persistence and synchronization only from the actual scoring/sync responses or documented guarantees. These are reporting categories, not required backend property names:

- **Assessment storage:** actual evidence that the score was saved/reused, failed or remains unknown.
- **Jira association:** actual evidence linking this assessment to the selected ticket. A known target in local JSON is intent, not proof of a completed association or content write.
- **Synchronization queue:** actual accepted/enqueued, failed or unknown evidence; retain an ID only when returned.
- **Jira score fields:** actual completed, failed, pending or unreported state. Queue acceptance alone does not establish a Jira UI update.

Do not assume existing-Jira scoring alone completes synchronization; perform the eligible two-argument follow-up above. Report a successful sync submission as “Score update request submitted for <actual ticket>” unless the response specifically confirms completed Jira score-field updates. Queue acceptance alone is not final completion. Missing optional queue/UI evidence does not invalidate the score or justify another trigger.

A transport envelope indicating success can contain a business rejection; inspect the complete response. For example, an actual business status=400 is a sync failure even when MCP isError=false. A scoring validation error, missing usable score or SKIPPED is not a successful numeric Story assessment. Follow the scoring reference for those outcomes rather than making up a score or changing the input route.

## Return the scored JSON without changing it

Only when the user or caller requests export/update, return the following facts from the actual result/context to the separately maintained JSON-capable export workflow:

- The identical scored JSON Workspace-relative path and current content/revision; never a result, preview, SPEC or newly copied payload path.
- The exact scoring arguments, assessment result-file/version and actual service/checksum identifiers when returned.
- The real optional SPEC status/path and file binding when used; without a SPEC, keep that pair absent.
- The confirmed source and known intended update ticket, if any, with their actual evidence. A ticket referenced inside business prose cannot select an update target.
- Any observed successful/failed/unknown downstream outcome and the actual current export intent.

The reviewer does not perform JSON-path business-content export or assume an older helper understands the new JSON contract. Local JSON scoring does not authorize immediate Jira creation/update. Leave preview, final content confirmation, content writing and local-JSON association handling to the export owner. The direct-Jira score-only call above is separate from that workflow.

Keep the JSON path and actual scoring inputs unchanged for later export. Do not render rich text again, reserialize the file, rename it or append a score. Any JSON change, or change to a SPEC/attachment participating in that assessment, makes the old result historical for the changed package. The user/caller must obtain a current assessment before using the changed package for export; do not silently reuse an existing-Jira score for a new local JSON revision or automatically rescore just to repair a record.

## Recover the failed layer only

| Observed condition | Action |
| --- | --- |
| A complete response was received but saving the local result failed | Retry saving that received response and its bindings. Do not call the scorer again. |
| A request timed out or ended without a known result | Preserve the actual attempt/error and mark its outcome unknown. Use only an actually available supported read-only reconciliation capability; do not assume the backend did not run or retry blindly. |
| Local conversion/JSON validation failed before a valid assessment | Correct only evidenced presentation/schema defects under the scoring rules. Missing business content is not permission to invent requirements. |
| A direct Jira score is complete and its score-sync call was never submitted | Submit only the two-argument sync when authorized; do not rescore. |
| A score is stored but association/queue/UI synchronization is failed or unknown | Preserve the score and every known successful write. Reconcile an attempted sync through actually supported evidence before any user-directed recovery. Do not rescore, export content or blindly replay an unknown/accepted call. |
| The user requests recovery of a confirmed failed, unapplied direct-Jira sync | With the actual assessment/target resolved and the retry explicitly requested, repeat only the two-argument sync. A further failure stops; never start a rescore/retry loop. |
| Only finish synchronization, but the referenced assessment is local JSON or the prior attempt cannot be reconciled | Retain the actual evidence and report the precise export-owner/recovery capability needed. Do not substitute ticket/source sync, create another assessment or replay a business-content export. |

Earlier Markdown-based assessments or historical writeback records remain historical evidence; they are not current JSON-path scoring evidence. Do not relabel their source, issue legacy binding calls, fabricate a success marker, clear caches or invent force-review arguments to migrate them.

Respect assessment-only/no-Jira-write instructions. Only direct-Jira score synchronization is performed here; service-side persistence and Jira score-field completion must be described according to actual responses, never promised beyond that evidence.
