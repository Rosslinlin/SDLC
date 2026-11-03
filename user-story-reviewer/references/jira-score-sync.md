# Assessment persistence, export handoff and recovery

Read after a score response, for an explicit persistence/synchronization-status request, or before returning a scored JSON to its caller. The filename is retained for existing links. This reviewer has no separate Jira writeback call and does not export content, upload attachments or modify Jira business fields.

## Preserve the actual assessment

Bind each result to the input actually sent to nodejs-base-mcp.scoreUserStory:

| Assessment source | Evidence to retain |
| --- | --- |
| Existing Jira | Exact jira_number and confirmed source, complete response/error and actual returned identifiers/cache information. A source-file carrier is provenance only, not the scoring input. |
| Workspace JSON | Exact story_json_workspace_path and observed saved content/revision; actual optional SPEC input pair and file binding when used; complete response/error and returned identifiers/cache/checksum information. Retain a known intended Ticket/source separately. |

Use the existing versioned score-result file and conversation context. Preserve every earlier assessment; do not create an extra state, manifest or handoff file. Keep the complete response unchanged. Parse it for the report without dropping unknown fields, truncating diagnostics, selecting a favorable nested result or recalculating finalScore. Preserve raw returned text when available; otherwise serialize the complete decoded object and label that representation honestly.

Returned service identifiers and checksums are evidence, not values for the model to manufacture. Trusted platform context supplies session identity; do not infer it from a path, request credentials, or add identity/session arguments to scoring. Profile configuration can be reused across conversations; a saved profile is not a score record for another session or JSON version.

For JSON without a SPEC, retain the actual one-argument call. Do not invent SPEC status/path values. When a real SPEC and its actual status were supplied, retain both exact inputs and the file content/revision used; a score-only file is not automatically an upload authorization.

## Report assessment and downstream outcomes separately

A valid numeric assessment, including a low score, zero or a cache hit, is a completed assessment under [scoring rules](scoring-and-results.md). Missing optional synchronization fields do not turn it into an endless pending review.

Interpret persistence and synchronization only from the real response or documented guarantees. These are reporting categories, not required backend property names:

- **Assessment storage:** actual evidence that the score was saved/reused, failed or remains unknown.
- **Jira association:** actual evidence linking this assessment to the selected ticket. A known target in local JSON is intent, not proof of a completed association or content write.
- **Synchronization queue:** actual accepted/enqueued, failed or unknown evidence; retain an ID only when returned.
- **Jira score fields:** actual completed, failed, pending or unreported state. Queue acceptance alone does not establish a Jira UI update.

Do not assume existing-Jira scoring has already completed every association/synchronization effect. Equally, do not require an optional queue ID or an extra trigger before reporting a valid Jira assessment. Report a missing downstream status as unreported/unknown where relevant, while preserving the completed score.

A transport envelope indicating success can contain a business rejection; inspect the complete response. A validation error, missing usable score or SKIPPED is not a successful numeric Story assessment. Follow the scoring reference for those outcomes rather than making up a score or changing the input route.

## Return the scored JSON without changing it

Only when the user or caller requests export/update, return the following facts from the actual result/context to the separately maintained JSON-capable export workflow:

- The identical scored JSON Workspace-relative path and current content/revision; never a result, preview, SPEC or newly copied payload path.
- The exact scoring arguments, assessment result-file/version and actual service/checksum identifiers when returned.
- The real optional SPEC status/path and file binding when used; without a SPEC, keep that pair absent.
- The confirmed source and known intended update ticket, if any, with their actual evidence. A ticket referenced inside business prose cannot select an update target.
- Any observed successful/failed/unknown downstream outcome and the actual current export intent.

The reviewer does not invoke the exporter or assume an older helper understands the new JSON contract. A request to score alone finishes with the assessment; it does not authorize immediate Jira creation/update. Leave preview, final content confirmation, actual writing and backend association handling to the export owner.

Keep the JSON path and actual scoring inputs unchanged for later export. Do not render rich text again, reserialize the file, rename it or append a score. Any JSON change, or change to a SPEC/attachment participating in that assessment, makes the old result historical for the changed package. The user/caller must obtain a current assessment before using the changed package for export; do not silently reuse an existing-Jira score for a new local JSON revision or automatically rescore just to repair a record.

## Recover the failed layer only

| Observed condition | Action |
| --- | --- |
| A complete response was received but saving the local result failed | Retry saving that received response and its bindings. Do not call the scorer again. |
| A request timed out or ended without a known result | Preserve the actual attempt/error and mark its outcome unknown. Use only an actually available supported read-only reconciliation capability; do not assume the backend did not run or retry blindly. |
| Local conversion/JSON validation failed before a valid assessment | Correct only evidenced presentation/schema defects under the scoring rules. Missing business content is not permission to invent requirements. |
| A score is stored but association/queue/UI synchronization is failed or unknown | Preserve the score and every known successful write. Do not rescore, re-export or replay content/attachment operations to repair it. |
| The user asks only to finish synchronization/writeback | Identify the actual referenced assessment from its call/result/context. Use a genuinely supported recovery interface only if available and within that request; otherwise report the precise missing recovery capability and retain the unresolved status. Do not create another assessment or substitute a content export. |

Earlier Markdown-based assessments or historical writeback records remain historical evidence; they are not current JSON-path scoring evidence. Do not relabel their source, issue legacy binding calls, fabricate a success marker, clear caches or invent force-review arguments to migrate them.

Respect an explicit no-Jira-write instruction. This reviewer performs no additional write after scoring; service-side persistence or synchronization must be described according to its actual contract and response, never promised beyond that evidence.
