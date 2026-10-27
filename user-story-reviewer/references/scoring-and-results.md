# Scoring modes and result records

Read this only for assessment requests after selecting the content source. Explicit update-only requests bypass this reference and the scorer. Collect missing Jira sources only for Jira-mode scoring.

## Jira route

Use this route when the user selects Jira's current content, or supplies a Ticket Key/incoming JSON with no selected current local revision. A ticket mapped to a locally revised Story does not override the Workspace route. Do not convert Jira-mode inputs into Workspace Markdown.

Call `nodejs-base-mcp.score_requirement_markdown` with the ticket and its user-provided source:

```json
{
  "jira_number": "AIWPB-8052",
  "source": "WPB"
}
```

Accepted sources are `WPB`, `ALM`, `DATA`, `FCR`, and `GO`. Do not infer one from the issue-key prefix. The tool uses the authenticated user's Jira access, retrieves the issue, uses the latest `*spec.md` attachment when available, and treats Jira fields as supporting context.

Do not include `relativePath`. Omit `specFileStatus` and `specFilePath` in this skill version. Do not call a Jira retrieval tool before scoring. A successful Jira review is stored and associated by the scoring tool itself; then perform the automatic four-field [Jira update trigger](jira-score-sync.md) without a confirmation popup.

Jira ratings may be reused by source and issue key across conversations. Within the configured cache window the existing rating is reused; an identical versioned fingerprint may also reuse the prior rating afterward. A reused rating is a valid result.

## Workspace route

Use this route for ticketless content and selected current local Story revisions, including a Story created, scored and exported earlier in this conversation. Read and verify the actual edited Markdown according to [workspace input conversion](input-format.md); reuse its suitable current path, then call:

```json
{
  "relativePath": "outputs/user-story-reviews/story-001/story-001-score-input.md"
}
```

Use the actual workspace-relative `.md` or `.markdown` path. Do not pass pasted content, a URL, an absolute path or the result path. Do not include `jira_number` or `source`. Workspace cache identity uses the current conversation, relative path and content checksum when available.

Workspace assessment does not automatically run the reviewer's four-field trigger, even when the Story has an exported Ticket Key. Carry the current score input/result and known Ticket/source mapping back to the caller. When content export/update is requested, the existing helper updates the current local content to that Ticket and then performs its five-field binding with the exact scored `relativePath`. This assessment step does not collect IDs for a four-field call.

Workspace identity comes from trusted platform context. Do not add a conversation ID to either scoring call; the separate Jira update trigger uses its own required arguments.

## Optional parameters

Normally omit `force_review` and `metadata`. Only an explicit user request for a fresh review of unchanged content authorizes `force_review`; follow the live callable schema's actual type. A low score, cache hit, retry or formatting concern does not authorize it.

This skill currently omits `specFileStatus` and `specFilePath`. They are persistence metadata and do not affect scoring.

The two route inputs are mutually exclusive: use either `relativePath` or the complete `jira_number`/`source` pair, never both.

## Create the result only after the call

For Jira mode, initially create `<TICKET>-score-result.md` after the call; there is no local score-input file. For Workspace mode, save the result in its established candidate folder after success, returned error, timeout or other observed completion. For either scoring mode, use the next unused `-v2`, `-v3`, etc. result version for every later assessment and preserve prior records. Existing suitable local Markdown needs no duplicate input file. If the scoring tool was never invoked, do not create a score-result file.

Decode the documented single JSON text content block. If the runtime exposes an unambiguous wrapper and nested result, use the result for evaluation and retain the complete wrapper. Preserve raw JSON text verbatim when available; otherwise serialize the complete object without claiming byte-for-byte preservation. Do not omit unknown fields, truncate arrays, select a favorable nested object or recalculate `finalScore`.

Keep these sections in the result file:

- **Target and route:** actual selected content source, invocation parameters excluding secrets and assessment version. For Workspace content with an exported target, retain its verified Ticket/source mapping separately from scorer arguments; record the current scored input path and known content revision.
- **Input conversion:** workspace field mapping, presentation changes, expected AC count and read-back result; for Jira mode state that the tool read Jira directly and no local conversion was used.
- **Assessment:** actual tool status and all returned score fields and substantive diagnostics.
- **Recognition:** returned AC counts, warnings, parse errors and any checkable mismatch or limitation.
- **Complete tool response:** full scoring JSON plus any transport wrapper or actual error information.
- **Persistence outcome:** Jira mode records the scoring tool's Jira review/cache outcome and the automatic four-field trigger's invocation/response or reason it was not sent. Generic tool success is not proof that Jira score fields were written. Workspace mode records its completed assessment and any pending export/update handoff; scoring itself does not bind the revised Workspace record to Jira. The helper owns the subsequent content write and path-based binding.

## Validate and report

A completed numeric assessment requires boolean `ok === true` and a finite numeric `finalScore`. A score below 75, including 0, is still a completed assessment. Tool success does not mean quality pass.

For workspace mode, compare returned `acCount` and `dimensions.sizingSplit.acCount` when present with the verified number of source criteria. Report mismatches, missing-AC warnings and nonempty `parseErrors`. For Jira mode, report the returned counts and diagnostics without claiming comparison to uploaded JSON, because the tool scored the Jira issue it retrieved.

Preserve a completed numeric result even when recognition is suspect. A malformed response or missing usable score must not be reported as a successful assessment. For a timeout, record an unknown outcome and reconcile available service results before retrying. A failed local result-file write can be retried without another score call.

For each target, report its route, saved paths, exact returned score/status/band/summary, all substantive sub-scores and diagnostics, cache outcome, and scoring failure recovery when needed. Routine background update-trigger details stay in the result file as defined in jira-score-sync.md. Provide the complete untruncated JSON through the result link for long results or batches. Distinguish a sub-score of 0 from total score 0 and from a tool error.
