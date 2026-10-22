# Scoring modes and result records

Read this after routing every Story and collecting all missing Jira sources.

## Jira route

Use this route whenever the Story input contains a Jira ticket number. Uploaded JSON or pasted Story content with a ticket number selects this route; do not rewrite that content into a workspace Markdown file.

Call `nodejs-base-mcp.score_requirement_markdown` with the ticket and its user-provided source:

```json
{
  "jira_number": "AIWPB-8052",
  "source": "WPB"
}
```

Accepted sources are `WPB`, `ALM`, `DATA`, `FCR`, and `GO`. Do not infer one from the issue-key prefix. The tool uses the authenticated user's Jira access, retrieves the issue, uses the latest `*spec.md` attachment when available, and treats Jira fields as supporting context.

Do not include `relativePath`. Omit `specFileStatus` and `specFilePath` in this skill version. Do not call a Jira retrieval tool before scoring and do not call a separate binding/database-write tool afterward. A successful Jira review is stored and associated by the scoring tool itself.

Jira ratings may be reused by source and issue key across conversations. Within the configured cache window the existing rating is reused; an identical versioned fingerprint may also reuse the prior rating afterward. A reused rating is a valid result.

## Workspace route

Use this route only when the Story contains no Jira ticket number. Prepare and verify the Markdown input according to [workspace input conversion](input-format.md), then call:

```json
{
  "relativePath": "outputs/user-story-reviews/story-001/story-001-score-input.md"
}
```

Use the actual workspace-relative `.md` or `.markdown` path. Do not pass pasted content, a URL, an absolute path or the result path. Do not include `jira_number` or `source`. Workspace cache identity uses the current conversation, relative path and content checksum when available.

Workspace identity comes from trusted platform context. Do not add a conversation ID to either route.

## Optional parameters

Normally omit `force_review` and `metadata`. Only an explicit user request for a fresh review of unchanged content authorizes `force_review`; follow the live callable schema's actual type. A low score, cache hit, retry or formatting concern does not authorize it.

This skill currently omits `specFileStatus` and `specFilePath`. They are persistence metadata and do not affect scoring.

The two route inputs are mutually exclusive: use either `relativePath` or the complete `jira_number`/`source` pair, never both.

## Create the result only after the call

For Jira mode, create `<TICKET>-score-result.md` after the call; there is no local score-input file. For workspace mode, create the paired `<prefix>-score-result.md` beside its verified input only after success, returned error, timeout or other observed completion. If the scoring tool was never invoked, do not create a result file.

Decode the documented single JSON text content block. If the runtime exposes an unambiguous wrapper and nested result, use the result for evaluation and retain the complete wrapper. Preserve raw JSON text verbatim when available; otherwise serialize the complete object without claiming byte-for-byte preservation. Do not omit unknown fields, truncate arrays, select a favorable nested object or recalculate `finalScore`.

Keep these sections in the result file:

- **Target and route:** Jira ticket/source or original ticketless source, actual invocation parameters excluding secrets, and assessment version.
- **Input conversion:** workspace field mapping, presentation changes, expected AC count and read-back result; for Jira mode state that the tool read Jira directly and no local conversion was used.
- **Assessment:** actual tool status and all returned score fields and substantive diagnostics.
- **Recognition:** returned AC counts, warnings, parse errors and any checkable mismatch or limitation.
- **Complete tool response:** full scoring JSON plus any transport wrapper or actual error information.
- **Persistence outcome:** Jira mode records the tool's returned Jira review/cache outcome; workspace mode records that the assessment was saved without a Jira association. Do not claim a separate write or binding call.

## Validate and report

A completed numeric assessment requires boolean `ok === true` and a finite numeric `finalScore`. A score below 75, including 0, is still a completed assessment. Tool success does not mean quality pass.

For workspace mode, compare returned `acCount` and `dimensions.sizingSplit.acCount` when present with the verified number of source criteria. Report mismatches, missing-AC warnings and nonempty `parseErrors`. For Jira mode, report the returned counts and diagnostics without claiming comparison to uploaded JSON, because the tool scored the Jira issue it retrieved.

Preserve a completed numeric result even when recognition is suspect. A malformed response or missing usable score must not be reported as a successful assessment. For a timeout, record an unknown outcome and reconcile available service results before retrying. A failed local result-file write can be retried without another score call.

For each target, report its route, saved paths, exact returned score/status/band/summary, all substantive sub-scores and diagnostics, cache outcome, and failure recovery when needed. Provide the complete untruncated JSON through the result link for long results or batches. Distinguish a sub-score of 0 from total score 0 and from a tool error.
