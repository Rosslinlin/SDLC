# scoreUserStory — confirmed JSON input and persistence

This branch follows the supplied backend screenshots and subsequent user confirmations: scoreUserStory retains SPEC status/path alongside the JSON path; Story Score is not written into SPEC. The latest file contract uses fields plus a root acceptanceCriteria field-ID string, without customFields, fields.reporter or root staffId. The earlier nested-map and one-argument scoring examples do not override the current file format or confirmed three-argument call below. No complete deployed response or evaluator weights were supplied.

## Invoke on the complete saved package

Tool: nodejs-base-mcp.scoreUserStory. Parent calls it after current independent content PASS and verified Result/Reviewer Notes in every current SPEC. Candidate source coverage is complete; whole-batch reconciliation additionally precedes export.

```json
{
  "story_json_workspace_path": "outputs/ceaia/example-story/story.json4jira",
  "specFileStatus": "Pass",
  "specFilePath": "outputs/ceaia/example-story/example-story-spec.md"
}
```

Use exactly these three arguments. Both creates and updates score their current Workspace JSON; never send relativePath, jira_number/source, conversationId, staffId, pasted content or a score-record path. Do not send optional old force_review/metadata fields that are not in this confirmed contract. Map the actual primary SPEC Result PASS to case-sensitive Pass (the enum is Pass/Fail); a non-PASS review does not reach normal SDLC scoring.

The JSON and SPEC paths are actual readable Workspace-relative files, not absolute paths, URLs, aliases, traversal or guessed prefixes. Record them once and reuse exact story_json_workspace_path for export. Before scoring an update, synchronize all affected local SPEC/test content with the Story delta and resolve its automatic delivery plan; user silence about SPEC never permits scoring stale content. The JSON attachments list controls uploads. Only explicit remote preservation uses attachments=[] while still passing the synchronized local SPEC through specFilePath, without an extra score-only proof. Default add/replace plans include their authorized actual paths. If the service rejects a package, preserve the precise error; do not override explicit none, omit intended replacement or switch to create.

session_id comes from trusted platform request context (the new document names Conversation-ID), never caller-generated arguments. Do not manufacture or rewrite headers. The actual gateway may map headers; follow its real contract rather than assuming the old X-AF name or treating a stored conversation ID as current.

## Backend validation, cache and records

The backend validates JSON syntax, required fields/types and enums before evaluating Story quality. A validation failure can be a specific VALIDATION_FAILED text response with field/line details; it is not a usable score. Fix only the identified supported defect within the local budget and revalidate changed inputs. Missing business values need real facts, not invented defaults. SKIPPED is for backend-exempt types, not a passing Story score and never permission to change Story to an exempt type.

The supplied design keys results by session/path and version, with cache matching session/path/content_checksum. JSON and scoring SPEC content participate in currentness; do not assume SPEC edits are metadata-only or a normal call must hit cache. The backend computes checksums; the model does not create substitutes. Cache-hit audit/version semantics are not fully supplied: preserve actual responses/IDs without claiming each call necessarily creates a new version or audit row.

Before invoking, read the exact JSON and scored SPECs. Verify the [required fields structure](jira-json-assembly.md) and completed [text encoding](jira-text-format.md): root staffId/customFields and fields.reporter are absent, fields.description is a Jira wiki string, root acceptanceCriteria is the real field ID, and fields[that ID] contains the actual AC Jira wiki. Updates include the confirmed target issueIdOrKey in the file, matching actual source/baseline/state; creates omit it entirely. This never adds a fourth score argument. Mapping IDs are not Story text; a missing/empty/non-string mapped value or a legacy nested-map/flat/Markdown file returns to production precheck before scoring. Record storyJsonRevision, each relevant SPEC fileRevision, primary path/status and review attempt, then keep them unchanged during the call. The sole JSON path is the later database lookup identity; a renamed file is not the same scored package. Content PASS and local records do not replace actual service persistence.

## Preserve and interpret the actual response

Allocate the next unused story-quality-score-attempt-<nnn>.md using [record template](../templates/score-record-template.md). Preserve the complete actual returned text, including unknown fields, error strings and service identifiers. If an MCP text block contains the JSON assessment, parse that entire object and retain its original text; never select a favorable nested result. For an already decoded object, serialize the complete object once and label that mode rather than claiming byte-verbatim preservation. Conflicting/truncated responses are technical blockers.

Record the exact three inputs and observed file bindings outside the untouched response. The expected assessment can expose ok/finalScore/cache flags, counts, parser errors and service IDs; no single optional diagnostic/ID is mandatory unless the live contract requires it. A validation string or SKIPPED cannot be coerced into an assessment. Never calculate finalScore from components or infer it from a band/status.

Retain the existing SDLC policy: actual boolean ok=true, finite numeric finalScore in 76–100 and valid AC/parser recognition. The screenshots do not specify the backend threshold number; do not claim its configured threshold has been verified as 76. A backend rejection still blocks creation. Inspect returned parseErrors/warnings and any AC counts against the actual Jira wiki at fields[acceptanceCriteria]; count distinct declared AC IDs/entries there, not the mapping, BDD references or repeated mentions in description/SPEC. Never count an absent old top-level array as zero ACs. Missing optional counts alone do not fail; contradictory/zero returned counts for nonempty ACs or unresolved recognition errors block readiness. Do not switch the canonical field back to Markdown to chase a score; preserve diagnostics for technical diagnosis. No additional exact-heading parser requirement has been supplied.

## Freeze and recovery

Save/read back the full response before readiness and confirm inputs did not change. The actual score is shown in the independent record/UI only. **Do not write it into SPEC, JSON or an attachment, and do not make the old display-driven S2 call.** This removes the old score → SPEC edit → checksum change cycle while preserving the real score call and its persisted SPEC status/path.

JSON, scored SPEC, attachment selection or routing changes invalidate this assessment and preview. Align affected artifacts, independently re-review changed business content, synchronize SPEC headers before the next assessment, and score the final package again. Do not force unchanged reassessment to seek a different number. Normal backend cache reuse is valid if returned for the exact current package.

If received-response persistence fails, retry that save, not scoring. A confirmed pre-evaluation technical failure permits at most one supported corrected retry; an unknown timeout requires supported reconciliation, not blind replay. A low score permits only evidence-backed repair within the shared content budget; canonical content still rejected is a service issue. Keep readiness false for invalid/unknown score/persistence and never invent DB success.
