# Scoring tool contract — supplied platform documentation

Source: user-supplied Tool details/response screenshots, 2026-09-20, plus the 2026-10-23 Story scoring tool handoff describing Workspace/Jira modes and SPEC metadata. These describe the external service; they are not a local evaluator or permission to alter scoring weights. Follow the live callable schema if it differs. No reconstructed "complete real response" is supplied here.

## Input and identity

Tool ID: `nodejs-base-mcp.score_requirement_markdown`.

The SDLC route uses Workspace mode. Its required input is the actual workspace-relative path to the final fully written Story `.md`/`.markdown` file. After independent content-review PASS and verified SPEC header synchronization, also provide the current primary SPEC review Result string and its workspace-relative path:

```json
{
  "relativePath": "outputs/ceaia/example-story/STORY.md",
  "specFileStatus": "Pass",
  "specFilePath": "outputs/ceaia/example-story/example-story-spec.md"
}
```

The scoring tool accepts `specFileStatus` only as the case-sensitive strings `Pass` or `Fail`. The independent review report and SPEC `Review Result` / `Result` header retain their existing `PASS` verdict; map that verified header value to `Pass` at the tool boundary. Never submit `PASS`, `pass`, or the document lifecycle `Status: Draft`. A failed/non-PASS review blocks SDLC scoring, so `Fail` is not sent in this normal route. Record both the source header verdict and the actual submitted value, and compare the latter with the mapped value during handoff validation.

`specFileStatus` and `specFilePath` are saved with the completed score; they do not affect the Story scoring calculation. The user confirmed that a normal cache hit may reuse the score while persisting the newly supplied SPEC metadata, and the later Jira ticket binding associates the final passing score record. The normal SDLC call omits optional force_review and metadata. The documented default may reuse a successful cached review for unchanged Markdown; do not force a new evaluation merely to refresh SPEC metadata. The screenshots label force_review/metadata types inconsistently; if used, follow the live callable schema rather than guessed coercion.

A normal assessment must never force a fresh review. Only explicit user intent to refresh the same content authorizes force_review=true in the actual schema's type. Record that exceptional direction; do not interpret a low score, cache hit, automatic repair or generic retry request as fresh-review authorization.

Workspace identity comes from trusted X-AF-Conversation-ID request header. Do not send conversationId or fabricate a header. A returned conversationId is optional internal evidence, never a required caller input. SDLC updates also score their current Workspace STORY.md through relativePath even when a Jira ticket key exists; do not mix relativePath with jira_number/source. The standalone user-story-reviewer has its own Jira mode and does not change this route.

Reject URL/URI, absolute path, artifact ID, data/blob/file URL, null byte and '..' traversal. Use actual workspace path from tools. The documented UI form documents/<active-conversation-id>/name.md may be accepted along with equivalent workspace-relative forms; do not manufacture that prefix or strip a real directory by guesswork.

Optional metadata is audit-only and contains no secrets or copied source bodies. Every invocation writes an immutable service audit row, including cache hits. Local numbered Markdown score records supplement that service history and are never overwritten. Record the three submitted scoring inputs, reviewAttempt and observed Story/SPEC revisions in the local score record outside the untouched raw response; do not claim the service echoed fields it did not return.

## Extract and preserve the response

Documented return: a single JSON text content block. For an MCP transport object, read its single type=text content item's text and parse that full JSON object. Allocate the next unused `story-quality-score-attempt-<nnn>.md`, render the human-readable fields from `templates/score-record-template.md`, and place the complete JSON text verbatim and unedited in its fenced `json` block. Do not persist the outer MCP wrapper as the evaluation object. Do not scrape a rendered screenshot or truncated chat log.

If the runtime already exposes exactly that decoded object, serialize the entire object once into the fenced block with persistenceMode=complete-object-json-in-markdown; never claim byte-verbatim preservation. When raw text is available, use persistenceMode=verbatim-json-in-markdown. Multiple conflicting text blocks, truncated JSON or ambiguous nested evaluation objects require technical recovery. Never pick a favorable nested result.

The current tool description lists:
ok, cache_hit, review_id, audit_id, content_checksum, title, finalStatus, finalScore,
sectionScores, qualityBand, reviewFramework, rubricVersion, reviewMode, acCount,
ruleHitsSummary, evidenceGaps, summary, attempts, parseErrors.

The supplied observed response additionally/instead includes:
conversationId, relativePath, fileName, scoreBreakdown, dimensions, criticalIssues,
warnings, strengths, details; AC count appears as dimensions.sizingSplit.acCount.

Preserve all returned fields, including unknown/new ones. These two observed field sets are not a reason to require every diagnostic on every response. Never expect section_scores.overall_score; never derive finalScore from sectionScores, scoreBreakdown or dimensions.

## Pass and parser integrity

Business threshold remains boolean ok === true AND finite numeric finalScore >= 76 (within the documented 0–100 scale). ok=true means invocation success, not quality PASS. A cache_hit=true response with ok=true and finalScore=36 is a valid low score, not a tool crash or permission to proceed. finalStatus is diagnostic, not a substitute threshold.

Before scoring, verify exactly one standalone `## Acceptance Criteria` heading with nonempty criteria, stable unique AC-001 etc. and evidenced Given/When/Then. BDD lives inside those criteria; do not rename the heading to "Acceptance Criteria and BDD Scenarios". Count distinct declared ACs, not occurrences in references or examples.

After scoring:
1. Read parseErrors, warnings and available AC counts. The warning "No Acceptance Criteria section found." is a format/parser blocker even when dimensions mention ACs.
2. If top-level acCount and/or dimensions.sizingSplit.acCount are returned, compare each with actual declared AC count. Conflicting counts or zero for a nonempty Story block downstream generation. Absence of both counts alone is not failure.
3. Nonempty parseErrors is a technical/parser blocker. Do not turn it into a request for invented business requirements.
4. A noncanonical heading can be corrected without changing business scope, then assessed as changed Story bytes. Use the shared bounded correction/repair policy and record the cause.
5. If canonical content still triggers recognition failure, pause for parser/service diagnosis. Do not endlessly reword ACs or set force_review merely to seek a better score.
6. True evidence gaps (missing approved design, copy, accessibility details or specified failure behavior) require source evidence. A heading fix does not justify promising that a 36 score will pass.

Preserve service content_checksum exactly when returned; it is service metadata, not a requirement to calculate a local hash. The sample prefix does not establish normalization semantics. Associate the actual invocation with the saved Story path/observed revision and read back current content as defined in workflow-contract.md. Never fabricate a checksum or claim read-based comparison is cryptographic proof.

## Owner and recovery

Generation owns the post-review invocation, response preservation and evidence-backed Story repair. Independent content review comes before this call and never invokes or refreshes the scorer. Each candidate owns its own result; no averaging or cross-candidate reuse.

No repeated call for unchanged current Story plus unchanged verified SPEC status/path/revision in normal operation. Changed SPEC metadata requires a new normal invocation even if Story bytes are unchanged; the content score may be cached. An explicitly authorized fresh assessment is the sole force_review exception; invalidate downstream readiness first. At most one supported technical retry after a confirmed pre-evaluation failure; an unknown timeout must be reconciled, not blindly replayed. Saving an already received response can be retried without another score call, using the same reserved attempt path when no complete record was committed. Never overwrite a completed earlier attempt.

Only the exact numeric finalScore may be shown as Story Score in the designated SPEC header, under [SPEC score display](spec-score-display.md). Complete score records, audit IDs, checksums, conversation identity and diagnostic data stay internal. Never put them, or the displayed total, in Story, Test Case, update manifest or Jira issue fields. The selected SPEC attachment may contain that one approved header value.
