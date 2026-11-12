# scoreUserStory calls and result records

Version: 3.0.0-json-preview.3. Use the actual content source selected under SKILL.md. A requested assessment invokes nodejs-base-mcp.scoreUserStory; a sync-only request instead follows [synchronization and recovery](jira-score-sync.md) without rescoring.

## Two mutually exclusive routes

### Current Jira content

```json
{
  "jira_number": "EXAMPLE-123",
  "source": "WPB"
}
```

Use the actual requested primary ticket and confirmed source: WPB, ALM, DATA, FCR or GO. Reuse verified ticket/source context; ask once for unresolved mappings, preferably together for a batch. Never infer source from prefixes or use a cached destination as proof that a referenced ticket belongs there.

Pass exactly jira_number and source. Do not pass a JSON/Markdown carrier path, SPEC parameters, staffId or session values. Do not create a local input file, query project/type/metadata, or fetch Jira content separately before this call; the service reads current Jira itself. A raw Jira dump remains this route even when saved as Markdown/JSON to Workspace. A failed Jira assessment does not fall back to local file scoring.

After ok=true and finite numeric finalScore, including 0/cache hits, synchronize the score through java-base-mcp.exportjirabydynamicfield using the identical two arguments under [score synchronization](jira-score-sync.md). Respect assessment-only/no-Jira-write requests and retain any prior submission outcome. Scoring failures, SKIPPED and unknown outcomes do not trigger synchronization.

### New or selected local JSON, no SPEC

```json
{
  "story_json_workspace_path": "outputs/user-story-reviews/story-001/story.json4jira"
}
```

The user confirmed that when no SPEC exists, both SPEC parameters are omitted. Do not send empty strings, null, false, a fake Pass/Fail, a result-file path or a manufactured SPEC. Do not pass jira_number/source with the JSON route. A local revision of an exported Story stays on this route: any confirmed update target belongs in its JSON, not in extra score arguments.

### Local JSON with a real selected SPEC and actual status

```json
{
  "story_json_workspace_path": "outputs/user-story-reviews/story-001/story.json4jira",
  "specFileStatus": "Pass",
  "specFilePath": "uploads/provided-story-spec.md"
}
```

This example assumes an actual selected readable SPEC with evidenced PASS; map it to exact Pass. An evidenced FAIL maps to Fail and remains honest assessment metadata, not an SDLC gate that stops ordinary Story scoring. Include the two SPEC arguments together. A supplied SPEC with unknown/Not Reviewed status requires the missing fact to be resolved, not a guessed enum or file edit. Merely having an attachment does not prove its review status or authorize uploading it. Do not generate or review a SPEC solely to satisfy optional arguments.

Read back the exact JSON and selected SPEC before invoking. JSON root acceptanceCriteria is the actual field-ID string; read AC text from fields[that ID], not the mapping. Apply [input preparation](input-format.md) and [assembly](jira-json-assembly.md) only as needed. Keep the actual Workspace-relative input path unchanged through later export; do not rename it, normalize a prefix, add a session ID or score a report. Do not pass relativePath, old force_review/metadata, headers or manually calculated checksums. Only the confirmed route arguments above are used.

## Content, cache and bounded correction

Each new assessment request calls the service for the selected current content, even if a prior report exists. A returned cache hit is a valid service result; do not request another score merely for a different number. Cache identity and record/version/audit behavior are backend-owned. Preserve actual returned identifiers and flags, without promising that every call/cache hit creates a new row. Reusable profiles are configuration, not cross-session score evidence.

A JSON edit or change to a selected scoring SPEC/participating attachment invalidates the old input association. Preserve historical results and obtain a current assessment before representing changed content as scored. Do not assume presentation or SPEC metadata edits are checksum-exempt. A new result-file write or profile save alone does not change the scoring content.

Handle service outcomes separately:

| Actual outcome | Action |
| --- | --- |
| Boolean ok=true and finite numeric finalScore | Completed assessment, including a low score or 0. Show the exact returned value; no minimum score is imposed by this reviewer. |
| VALIDATION_FAILED string or structured input error | Preserve the complete error and exact location. Correct only evidenced technical syntax/mapping/encoding defects, preserving all business content. Collect known corrections in one pass; at most one correction and retry per actual cause. A repeated cause, canonical input still rejected or an unknown contract stops for precise technical recovery. Missing business content is never invented to pass validation. |
| SKIPPED | Report skipped/exempt as returned, with no invented Story score. Never change issueType to obtain exemption. |
| Malformed/truncated/conflicting response or missing usable score | Record the failure; do not calculate a replacement score or select a favorable nested object. |
| Unknown timeout/termination/connection loss | Record unknown and reconcile only through actually supported evidence/capability. No blind repeat, route switch or export to recover it. |
| Received response cannot be saved locally | Retry saving that response, not scoring. Preserve actual service success/error independently of the file-save failure. |

Do not repair weak business wording, add scenarios or rewrite ACs automatically to raise a score. A separate user-authorized content revision can later be assessed; it is not a technical retry. A total score of 0 is distinct from a zero sub-score and from a failed invocation.

## Result record after the actual invocation

Keep one candidate folder and preserve every earlier assessment. First use <candidate>-score-result.md; later assessments use the next unused -v2, -v3, etc. Save only after an actual invocation returns, fails or has an observed unknown outcome. Do not create a fictitious result for an uncalled tool. An unresolved input/configuration question can stay in conversation context without extra state or manifests.

Retain the entire returned text/object and transport wrapper, including unknown fields, arrays and error strings. Preserve verbatim text when available; if already decoded, serialize the complete object and label that mode rather than claiming byte-verbatim text. Do not overwrite the raw response with selected fields or recompute finalScore from components.

Use these concise sections in the existing Markdown result:

- Target/source route and routing basis; exact actual invocation arguments and known input revision, without secrets.
- Input preparation: reused JSON or source-preserving conversion, actual profile/query facts, criterion grouping/count when checkable, and input read-back. For direct Jira, state no local conversion was used.
- Assessment and recognition: returned score/status/band/sub-scores and substantive diagnostics, parser/AC-count concerns and cache outcome. Count identified source criteria as whole groups, including TBC/incomplete criteria, not their individual BDD steps or references inside code/prose. For direct Jira report the service counts without pretending a local dump was its scoring input.
- Complete raw scoring response/error, including all observed service IDs/checksums/version evidence. For existing-Jira synchronization, retain its separate actual call arguments and full response/error without overwriting the scoring result.
- Persistence, score synchronization and any requested handoff: actual observed score-save/linkage/queue/Jira-sync evidence or not returned/unknown, plus the exact selected JSON/SPEC paths and known target when relevant. These are observations, not mandatory invented response keys. Follow [synchronization and handoff](jira-score-sync.md).

Read the saved result back. Report the requested Story or ticket/source, exact returned score/status, actual synchronization outcome, material recognition limitations and full-result link; combine independent batch results for the user. A failure for one independent target does not block the others. Missing optional sync evidence does not turn a completed assessment into a score failure or justify duplicate sync calls. Only the direct Jira route has a two-argument score-sync follow-up; no legacy four/five-field trigger or business-content export follows scoring here.

## User-facing score presentation

For a completed numeric assessment, render each target in readable Markdown with its actual Story title (and actual Jira key when known), then these three sections in order: **1. Evaluation**, **2. Recommendations**, **3. Clarifying questions**. Use UK English unless the user requests another language. Evaluation contains one table with columns `Section | Score | Max | Notes` and the following rows in order. The Max values are the six dimension weights in the supplied 100-point rubric; they are display metadata, not instructions to score or adjust content.

| Section | Returned Score field | Max |
| --- | --- | ---: |
| Description format | `description_format_score` | 3 |
| Acceptance criteria format | `acceptance_criteria_format_score` | 3 |
| Description understandability | `description_understandability_score` | 25 |
| Acceptance criteria specificity | `acceptance_criteria_specificity_score` | 29 |
| Acceptance criteria testability and coverage | `acceptance_criteria_testability_coverage_score` | 25 |
| Scope | `scope_score` | 15 |
| Overall | `finalScore` | 100 |

Read the six section scores from the returned `sectionScores` or `scoreBreakdown` object; the observed response contains both with matching values. If both are present and disagree, disclose the conflicting field instead of choosing one. Use the returned `finalScore` for Overall, not a sum or `overall_score` replacement. Never infer a missing section score, change a returned number, or rescore to populate the grid. If a section value is absent, show `—` in Score and say it was not returned. If a returned value exceeds its displayed Max, show the actual value and flag the scoring-contract discrepancy in Notes; do not silently cap it. Preserve the complete response in the result record.

Keep Notes brief and supported by the returned summary, substantive diagnostics or assessed Story; do not expose internal rule codes or raw JSON in the user-facing grid. Put the returned `qualityBand` and a concise overall observation in Overall Notes when available. Derive recommendations from material gaps and ask only useful unresolved business questions; use `None identified.` in an otherwise empty required section. Do not manufacture a Jira key for a new local Story or a title missing from the actual input/result. For failed, skipped or unknown assessments, explain the actual outcome without a fabricated numeric grid.

Within Evaluation, after the table, include the existing concise assessment/synchronization status, material recognition limitation and full-result link where applicable. Keep score quality distinct from whether Jira score fields have actually updated. For a batch, repeat the same format per target. This presentation rule does not change score calls, result persistence, Jira synchronization, recovery or export behavior.
