---
name: user-story-reviewer
description: Score one or more user stories with the platform Markdown scoring tool, show the complete scoring results, and bind each score to its verified existing Jira ticket when one is known. Use for standalone scoring or after a story-generation skill; do not create or edit Jira Story content.
---

# User Story Reviewer

Score each Story separately, save the tool response, and show it to the user. If the Story has a verified existing Jira ticket, associate that score with that ticket. Accept a direct user request or a handoff from a team-specific Story generation skill.

Follow this order for **each** Story: capture the original → prepare and verify the scoring copy → score that copy → save the complete response → bind an existing ticket if verified → report score and binding status. Never mix paths, scores, or tickets across batch items.

| Situation | Action after scoring |
| --- | --- |
| No ticket, or only a ticket mentioned as a dependency/reference | Keep the score; do not bind. |
| Clearly identified own Jira ticket | Bind this Story's scored path to that ticket. |
| Possible own ticket, but ownership or Jira source is unclear | Keep the score; report binding pending and the exact missing fact. |
| Score call failed or has no usable numeric score | Report the failure; do not claim a saved score or perform binding. |

## Required tools and boundaries

- `nodejs-base-mcp.score_requirement_markdown`: score a saved workspace Markdown Story; the tool saves a service score/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: make only the five-field **binding call** below for a verified existing ticket.
- Workspace read/write tools: preserve the original Story, create the scoring copy, and save complete score responses. Use `java-base-mcp.getJiraInfos` or another available Jira read tool if Story or ticket facts must be retrieved.

Use the live tool schema. If a tool or required binding identity is unavailable, complete the scoring work that is possible and report the remaining step as pending. This skill does not create or edit Jira Story content.

## Identify each Story and ticket

1. Give every Story, including each batch item, a stable label. Keep its content, paths, tool response, and optional ticket together.
2. Read the actual source file when one exists. For pasted text, save it as a workspace file; for a Jira-only request, retrieve the Story with an available Jira read tool or ask for its content. Preserve the source exactly. Then prepare the separate scoring file below. Never pass text copied from chat as the score tool argument.
3. Identify the Story's **own ticket** from an explicit target key/link or a clearly labeled own-ticket field. Check ambiguous embedded keys against Jira when possible. A dependency, parent, example, or unrelated key is not the target ticket.
4. Before binding, obtain the full issue key, actual `almType` (`wpb`, `alm`, `data`, `fcr`, or `go`), real `staffId`, and current `conversationId` from trusted context or confirmed input. Do not infer `almType` solely from a key prefix or invent identity values.

## Prepare a uniform scoring file in the workspace

Create a new directory for each Story version; never write into the installed Skill folder or overwrite the user's source. Use the next unused names, for example:

```text
outputs/user-story-reviews/review-001/story-001/revision-001/
  original.md           exact Markdown source snapshot
  original.json         use instead when the source is Jira fields
  score-input.md        immutable file read by the score tool
  score-attempt-001.md  complete tool response and status
  handoff.json          paths, version, ticket, and binding status
```

Create **one** original snapshot: `original.md` for a Markdown source or `original.json` for raw Jira fields. For a batch, allocate `story-002`, `story-003`, and so on. If a Story changes, allocate `revision-002`; never replace the file that an earlier score used. Use actual workspace-relative paths in tool calls, not the example path above. In `handoff.json`, record at least `upstreamPath` (if a source file was supplied), `originalPath`, `scoredPath`, `scoreRecordPath`, `ticketKey` (or null), `almType` (or null), `normalizationStatus`, and `bindingStatus`. The later Jira export skill needs `scoredPath` to bind a newly created ticket to the score.

For a Markdown source, copy its text verbatim into `original.md`, then create `score-input.md` from it. Make only this deterministic formatting change when safe: a **standalone section heading or bold label line** whose entire label is `AC`, `Acceptance Criteria`, or `验收标准` (case-insensitive for English, optionally ending in `:` and/or preceded by Markdown heading markers) becomes exactly `## Acceptance Criteria`. The line must clearly introduce the Story's criteria, and there must be only one such section. For example, `## AC` becomes `## Acceptance Criteria`; the criterion lines below it remain identical. If the original already has one exact `## Acceptance Criteria` heading, copy it unchanged.

For a Jira-only source, save the retrieved field values unchanged in `original.json`. Build `score-input.md` from the actual Story text. A confirmed Jira wiki heading such as `h2. AC` may become `## Acceptance Criteria` while its section body stays unchanged. If Jira stores Acceptance Criteria in a separate, **confirmed** field, place that field's unchanged body after `## Acceptance Criteria`; keep the Description body unchanged. If Description already has an AC section, do not duplicate or merge it with another field unless the authoritative source is clear. Do not guess which custom field contains ACs. If rich text cannot be converted to Markdown without losing or changing content, ask for a Markdown source and do not score a reconstructed Story as though it were exact.

Do not change any other line, whitespace within criterion text, identifier, Given/When/Then wording, order, link, table, or business fact. Do not add missing ACs, IDs, or BDD steps. An inline mention of “AC” in prose, multiple possible AC sections, or unclear boundaries is **not** a safe heading match: use an identical scoring copy and tell the user normalization was skipped. Before scoring, read back the source and scoring copy. For Markdown, verify every difference is one allowed heading-line replacement. For Jira fields, verify that each field's content appears unchanged and exactly once, with only confirmed section headings added. If verification fails, correct the copy or stop before the score call. Keep both versions for audit and show their paths in the result.

## Score and retain the result

For each candidate, call `nodejs-base-mcp.score_requirement_markdown` with its verified workspace-relative **`score-input.md`** path. The tool reads that file directly:

```json
{"relativePath":"outputs/user-story-reviews/review-001/story-001/revision-001/score-input.md"}
```

Omit `force_review` and `metadata` normally. A cache hit is valid. Set `force_review` only if the user explicitly requests a fresh assessment of unchanged content, using the live schema's type. Within the same workflow, reuse a complete response for an unchanged Story; saving a record or binding a ticket must not trigger another score call. Score changed content separately.

Parse the documented single JSON text content block, not the outer MCP wrapper. If only a decoded scoring object is available, serialize the entire object once without claiming it is byte-identical to the server text. Preserve unknown fields. Never calculate `finalScore` from sub-scores or substitute `finalStatus` for it.

Save each actual invocation to a new numbered `score-attempt-<nnn>.md`. Include its candidate label, original and scored paths, observed content version, normalization status, tool status, and **complete JSON scoring object** in a fenced block. Preserve raw JSON text verbatim when available. Read back the record, scoring file, and supplied upstream file when there is one. If the source or scoring file changed during scoring, mark the result stale and prepare a new revision. Never overwrite a prior record or use its path for ticket binding. Update `handoff.json` only after the score record is complete.

`ok === true` plus a finite numeric `finalScore` means a completed numerical assessment. A score below 75 is still saved and may be bound. Surface `parseErrors`, warnings, missing Acceptance Criteria recognition, and returned AC counts. If the tool still reports a missing AC section for a verified canonical heading, report a parser issue; do not repeatedly rewrite the Story or force a fresh review to seek a higher score. For malformed or failed results, report the failure and do not invent a score. Reconcile an unknown timeout if possible; do not blindly replay it.

## Bind a verified existing Jira ticket

After a completed assessment, if this candidate has its own verified Jira ticket, make a **separate** `java-base-mcp.exportJiraByDynamicFields` call with exactly these five non-empty direct string arguments:

```json
{
  "staffId": "actual staff ID",
  "almType": "verified Jira source",
  "issueIdOrKey": "FULL-123",
  "conversationId": "actual current conversation ID",
  "markdownReviewRelativePath": "the same workspace-relative score-input.md path that was scored"
}
```

Use the full Ticket **Key**, never a numeric-only ID. `markdownReviewRelativePath` must equal this Story's `scoredPath` from `handoff.json` and the score tool call, not `original.md` or the score record. Pass no `description`, `summary`, `dynamicFieldsJson`, `attachmentsJson`, `issuesJson`, or other Jira-change fields. Do not wrap the call in `request` or `requestJson`. For a batch, make one binding call per Story; never use one shared path for multiple tickets.

Confirm binding from the returned `markdownReview` result. Transport success alone is insufficient; numeric `status=400`, partial, missing, or unknown results are not success. Keep the saved score and ticket key, update `handoff.json`, report binding as pending or failed, and do not automatically replay an uncertain call. If Jira export creates a ticket later, hand off `scoredPath` and the current score record so the export skill can bind the new key.

## Tell the user the actual scoring details

For each Story, report these in this order:

1. Story label, original path, scored path, and own Jira key if known. State whether an AC heading was normalized or left as supplied.
2. Database scoring status and exact returned `finalScore`, `finalStatus`, `qualityBand`, and `summary` when present.
3. Every returned section/dimension/breakdown score, strengths, critical issues, warnings, evidence gaps, parse errors, AC counts, and other scoring diagnostics. Preserve tool wording and labels; never fill in missing fields.
4. Ticket binding status: **bound**, **pending** (with reason), **failed** (with actual error), or **no ticket**.

Also give the user the **complete scoring-tool result**: show its full decoded JSON for each Story when practical; for a large batch, link each complete, untruncated record and summarize every substantive returned field per Story. Never present the outer MCP envelope as the scoring object. Keep scoring JSON, audit identifiers, and internal paths out of Jira-visible Story fields.

This standalone review has no 75- or 76-point export gate. Any separate SDLC workflow keeps its own scoring and review rules; this skill's result does not silently override them.
