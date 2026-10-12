---
name: user-story-reviewer
description: Score one or more user stories with the platform Markdown scoring tool, show the complete scoring results, and bind each score to its verified existing Jira ticket when one is known. Use for standalone scoring or after a story-generation skill; do not create or edit Jira Story content.
---

# User Story Reviewer

Score each Story separately, save the tool response, and show it to the user. If the Story has a verified existing Jira ticket, associate that score with that ticket. Accept a direct user request or a handoff from a team-specific Story generation skill.

Follow this order for **each** Story: identify the Story and its own ticket → score → save the complete response → bind an existing ticket if verified → report score and binding status. Never mix paths, scores, or tickets across batch items.

| Situation | Action after scoring |
| --- | --- |
| No ticket, or only a ticket mentioned as a dependency/reference | Keep the score; do not bind. |
| Clearly identified own Jira ticket | Bind this Story's scored path to that ticket. |
| Possible own ticket, but ownership or Jira source is unclear | Keep the score; report binding pending and the exact missing fact. |
| Score call failed or has no usable numeric score | Report the failure; do not claim a saved score or perform binding. |

## Required tools and boundaries

- `nodejs-base-mcp.score_requirement_markdown`: score a saved workspace Markdown Story; the tool saves a service score/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: make only the five-field **binding call** below for a verified existing ticket.
- Workspace read/write tools: save pasted Stories and complete score responses. Use `java-base-mcp.getJiraInfos` or another available Jira read tool if the Story or ticket facts must be retrieved.

Use the live tool schema. If a tool or required binding identity is unavailable, complete the scoring work that is possible and report the remaining step as pending. This skill does not create or edit Jira Story content.

## Identify each Story and ticket

1. Give every Story, including each batch item, a stable label. Keep its content, path, tool response, and optional ticket together.
2. Score a real `.md` or `.markdown` file in the active conversation workspace. Reuse a supplied readable workspace-relative path. For pasted text, save each Story to a distinct file and read it back. Preserve the user's content; do not rewrite it to raise the score. If the user supplied only a Jira key, retrieve the Story with an available Jira read tool or ask for its content.
3. Identify the Story's **own ticket** from an explicit target key/link or a clearly labeled own-ticket field. Check ambiguous embedded keys against Jira when possible. A dependency, parent, example, or unrelated key is not the target ticket.
4. Before binding, obtain the full issue key, actual `almType` (`wpb`, `alm`, `data`, `fcr`, or `go`), real `staffId`, and current `conversationId` from trusted context or confirmed input. Do not infer `almType` solely from a key prefix or invent identity values.

## Score and retain the result

For each candidate, call `nodejs-base-mcp.score_requirement_markdown` with the actual workspace-relative Story path:

```json
{"relativePath":"path/to/story.md"}
```

Omit `force_review` and `metadata` normally. A cache hit is valid. Set `force_review` only if the user explicitly requests a fresh assessment of unchanged content, using the live schema's type. Within the same workflow, reuse a complete response for an unchanged Story; saving a record or binding a ticket must not trigger another score call. Score changed content separately.

Parse the documented single JSON text content block, not the outer MCP wrapper. If only a decoded scoring object is available, serialize the entire object once without claiming it is byte-identical to the server text. Preserve unknown fields. Never calculate `finalScore` from sub-scores or substitute `finalStatus` for it.

Save each actual invocation to a new numbered Markdown score record. Include its candidate label, scored Story path, observed content version, tool status, and **complete JSON scoring object** in a fenced block. Preserve raw JSON text verbatim when available. Read back the record and Story. If the Story changed during scoring, mark the result stale and score the current version separately. Never overwrite a prior record. Never use the score-record path for ticket binding.

`ok === true` plus a finite numeric `finalScore` means a completed numerical assessment. A score below 75 is still saved and may be bound. Surface `parseErrors`, warnings, missing Acceptance Criteria recognition, and returned AC counts; never silently repair source content or claim a quality pass. For malformed or failed results, report the failure and do not invent a score. Reconcile an unknown timeout if possible; do not blindly replay it.

## Bind a verified existing Jira ticket

After a completed assessment, if this candidate has its own verified Jira ticket, make a **separate** `java-base-mcp.exportJiraByDynamicFields` call with exactly these five non-empty direct string arguments:

```json
{
  "staffId": "actual staff ID",
  "almType": "verified Jira source",
  "issueIdOrKey": "FULL-123",
  "conversationId": "actual current conversation ID",
  "markdownReviewRelativePath": "the same workspace-relative Story path that was scored"
}
```

Use the full Ticket **Key**, never a numeric-only ID. `markdownReviewRelativePath` must be the same Story path passed to the score tool. Pass no `description`, `summary`, `dynamicFieldsJson`, `attachmentsJson`, `issuesJson`, or other Jira-change fields. Do not wrap the call in `request` or `requestJson`. For a batch, make one binding call per Story; never use one shared path for multiple tickets.

Confirm binding from the returned `markdownReview` result. Transport success alone is insufficient; numeric `status=400`, partial, missing, or unknown results are not success. Keep the saved score and ticket key, report binding as pending or failed, and do not automatically replay an uncertain call. If Jira export creates a ticket later, hand off this scored Story path and record so the export skill can bind the new key.

## Tell the user the actual scoring details

For each Story, report these in this order:

1. Story label and path; own Jira key if known.
2. Database scoring status and exact returned `finalScore`, `finalStatus`, `qualityBand`, and `summary` when present.
3. Every returned section/dimension/breakdown score, strengths, critical issues, warnings, evidence gaps, parse errors, AC counts, and other scoring diagnostics. Preserve tool wording and labels; never fill in missing fields.
4. Ticket binding status: **bound**, **pending** (with reason), **failed** (with actual error), or **no ticket**.

Also give the user the **complete scoring-tool result**: show its full decoded JSON for each Story when practical; for a large batch, link each complete, untruncated record and summarize every substantive returned field per Story. Never present the outer MCP envelope as the scoring object. Keep scoring JSON, audit identifiers, and internal paths out of Jira-visible Story fields.

This standalone review has no 75- or 76-point export gate. Any separate SDLC workflow keeps its own scoring and review rules; this skill's result does not silently override them.
