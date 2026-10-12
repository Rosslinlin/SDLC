# Scoring call and result record

Read this after preparing a verified input file. The Story folder and filename prefix come from SKILL.md.

## Call the scoring tool

Use the actual saved input path, for example:

```json
{"relativePath":"outputs/user-story-reviews/aiwpb-123/AIWPB-123-score-input.md"}
```

Call `nodejs-base-mcp.score_requirement_markdown` with the workspace-relative Markdown path, not pasted content, a URL, an absolute path or the result path. Workspace identity comes from trusted platform context; do not add a conversationId argument to this call.

Normally omit `force_review` and `metadata`. A cache hit is valid. Only explicit user intent to refresh unchanged content authorizes `force_review`, in the live schema's type. Reuse a completed assessment of unchanged input when only reporting, binding or recovering a result-file write.

## Create the result only after the call

Before invocation there is only the input file for this assessment. Once the call returns success, returns an error, or ends in an observed failure/timeout, create the paired `<prefix>-score-result.md` beside that input in the same Story folder. Do not pre-create a skeleton or claim a score while the call is still running. If the scoring tool was never invoked, do not generate a score-result file.

Decode the documented JSON text content block. If the runtime exposes a wrapper with an unambiguous `result` object, use that object for evaluation and retain the complete wrapper separately in the same result file. Preserve raw JSON text verbatim when available; otherwise serialize the complete object without claiming byte-for-byte preservation. Do not omit unknown fields, truncate arrays, select a favorable nested object, or calculate finalScore from sub-scores.

Keep these sections in the single result file:

- **Story and source:** own ticket/source when known, original file/Jira/message reference, actual input path, assessment version.
- **Input conversion:** original-to-canonical field map, presentation changes, expected AC count, read-back outcome, unresolved attachment references or other limitations.
- **Assessment:** actual tool status and returned finalScore/finalStatus/qualityBand/summary when present; all returned score dimensions and substantive diagnostics.
- **Recognition:** expected count versus every returned AC count, warnings, parseErrors, and any mismatch.
- **Complete tool response:** full scoring JSON in a fenced block, plus any transport wrapper or actual error information.
- **Ticket binding:** no ticket, pending, bound or failed; exact target and five-field call when attempted, complete binding response/error, and actual user-info persistence outcome.

Do not add expected fields to the raw tool JSON. Write explanations and derived recognition checks outside it. Update only the binding/status sections when binding completes, preserving the scoring return.

After scoring, read back the input and compare it with the scored version and available original source. If input/source changed during the call, preserve the return, mark it stale and do not bind it as the current version. Use new filenames for any new assessment; do not overwrite the scored input.

Check any returned path or conversation identity against the actual invocation and trusted current context. A conflicting result identity must be resolved before binding; do not attach another Story's cached/wrapped result merely because it has a numeric score.

## Interpret completion and recognition separately

A completed numeric assessment requires boolean `ok === true` and a finite numeric `finalScore`. Below 75, including 0, is still a completed score that may be bound. Tool success does not mean quality pass.

Check returned `acCount` and `dimensions.sizingSplit.acCount` when present against the number of complete source criteria. A mismatch, conflicting returned counts, missing-AC warning for a present canonical section, or nonempty parseErrors must be reported as a recognition/parse concern. Empty warnings/parseErrors do not prove correct grouping. If no count is returned, state that the count could not be checked; do not fabricate it.

Preserve and report a completed numeric assessment even when recognition is suspect. If it is bound, explicitly retain that concern in the local result and user report; binding does not validate the score. Do not silently change the score, invent requirements, or repeatedly rewrite/re-score to seek a better number. A corrected input needs a separate assessment pair and a real explanation of the correction.

A malformed/failed response or no usable numeric score must not be claimed as a saved successful assessment and must not trigger binding. For a timeout, record unknown outcome and reconcile available service results before any retry. A failed local save can be retried with the already received response without another score call.

## Report to the user

For each Story, show:

1. Actual input/result paths, source, own ticket if known, and a short account of formatting conversion.
2. Database scoring outcome and exact returned finalScore, finalStatus, qualityBand and summary when available.
3. All returned sub-scores, strengths, critical issues, evidence gaps, warnings, parse errors, AC counts and other substantive diagnostics. Preserve tool labels and wording; do not substitute an overall paraphrase for the details.
4. Actual ticket-binding outcome and any required recovery.

Provide the complete untruncated JSON, either inline or through the saved result link for long results/batches. Distinguish a sub-score of 0 from total score 0 and from a tool error. Keep internal scores, audit IDs and workspace paths out of Jira-visible Story fields.
