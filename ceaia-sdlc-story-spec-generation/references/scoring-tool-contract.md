# Scoring tool contract — supplied platform documentation

Source: user-supplied Tool details screenshot and response screenshots, 2026-09-20. These describe the external service; they are not a local evaluator or permission to alter scoring weights. The response screenshots crop some long strings, so no reconstructed "complete real response" is supplied here.

## Input and identity

Tool ID: `nodejs-base-mcp.score_requirement_markdown`.

The only required input is the actual workspace-relative path to a fully written .md or .markdown file:

```json
{
  "relativePath": "outputs/ceaia/example-story/STORY.md"
}
```

Normal calls omit optional force_review and metadata. The documented default reuses a successful cached review for the same Markdown content checksum. This omission avoids a discrepancy in the screenshot: the parameter table labels force_review/metadata as String while the prose example uses boolean/object values. If either parameter is used, follow the live callable schema's type, not a guessed coercion.

A normal assessment must never force a fresh review. Only explicit user intent to refresh the same content authorizes force_review=true in the actual schema's type. Record that exceptional direction; do not interpret a low score, cache hit, automatic repair or generic retry request as fresh-review authorization.

Workspace identity comes from trusted X-AF-Conversation-ID request header. Do not send conversationId or fabricate a header. A returned conversationId is optional internal evidence, never a required caller input.

Reject URL/URI, absolute path, artifact ID, data/blob/file URL, null byte and '..' traversal. Use actual workspace path from tools. The documented UI form documents/<active-conversation-id>/name.md may be accepted along with equivalent workspace-relative forms; do not manufacture that prefix or strip a real directory by guesswork.

Optional metadata is audit-only and contains no secrets or copied source bodies. Every invocation writes an immutable service audit row, including cache hits. Local numbered Markdown score records supplement that service history and are never overwritten.

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

Generation owns invocation, response preservation and evidence-backed Story repair. Review only inspects current persisted evidence; it never invokes or refreshes the tool. Each candidate owns its own result; no averaging or cross-candidate reuse.

No repeated call for an unchanged current version in normal operation. An explicitly authorized fresh review is the sole freshness exception; invalidate downstream readiness first and assess its new returned evidence. At most one supported technical retry after a confirmed pre-evaluation failure; an unknown timeout must be reconciled, not blindly replayed. Saving an already received response can be retried without another score call, using the same reserved attempt path when no complete record was committed. Never overwrite a completed earlier attempt.

Scores, audit IDs, checksums, conversation identity and diagnostic data stay internal. Never include them in Story, Test Case, SPEC, update manifest, Jira preview or payload.
