---
name: user-story-reviewer
description: Score one or more user stories with the platform Markdown scoring tool, show the complete scoring results, and bind each score to its verified existing Jira ticket when one is known. Use for standalone scoring or after a story-generation skill; do not create or edit Jira Story content.
---

# User Story Reviewer

Score each Story separately, save the tool response, and show it to the user. If the Story has a verified existing Jira ticket, associate that score with that ticket. Accept a direct user request or a handoff from a team-specific Story generation skill.

Follow this order for **each** Story: read the source → create and verify `score-input.md` → score that file → save `score-result.md` → bind an existing ticket if verified → report score and binding status. Never mix paths, scores, or tickets across batch items.

| Situation | Action after scoring |
| --- | --- |
| No ticket, or only a ticket mentioned as a dependency/reference | Keep the score; do not bind. |
| Clearly identified own Jira ticket | Bind this Story's scored path to that ticket. |
| Possible own ticket, but ownership or Jira source is unclear | Keep the score; report binding pending and the exact missing fact. |
| Score call failed or has no usable numeric score | Report the failure; do not claim a saved score or perform binding. |

## Required tools and boundaries

- `nodejs-base-mcp.score_requirement_markdown`: score a saved workspace Markdown Story; the tool saves a service score/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: make only the five-field **binding call** below for a verified existing ticket.
- Workspace read/write tools: read the original Story, create the scoring file, and save the complete result. Use `java-base-mcp.getJiraInfos` or another available Jira read tool if Story or ticket facts must be retrieved.

When installing this skill on the platform, expose **both** `nodejs-base-mcp.score_requirement_markdown` and `java-base-mcp.exportJiraByDynamicFields` to it; listing a tool in the instructions does not grant access. Provide a Jira read tool if ticket-only requests are supported. Use the live tool schemas. If a tool or required binding identity is unavailable, complete the scoring work that is possible and report the remaining step as pending. This skill does not create or edit Jira Story content.

## Identify each Story and ticket

1. Give every Story, including each batch item, a stable label. Keep its content, paths, tool response, and optional ticket together.
2. Read the actual source file when one exists. For pasted text, use the user's message as the source; for a Jira-only request, retrieve the Story with an available Jira read tool or ask for its content. Prepare the scoring file below without changing business wording. Never pass pasted text directly as the score tool argument.
3. Identify the Story's **own ticket** from an explicit target key/link or a clearly labeled own-ticket field. Check ambiguous embedded keys against Jira when possible. A dependency, parent, example, or unrelated key is not the target ticket.
4. Before binding, obtain the full issue key, actual `almType` (`wpb`, `alm`, `data`, `fcr`, or `go`), real `staffId`, and current `conversationId` from trusted context or confirmed input. Do not infer `almType` solely from a key prefix or invent identity values.

## Prepare the two workspace files

Keep **exactly two files per scored Story version** in the active conversation workspace:

```text
outputs/user-story-reviews/review-001/story-001/revision-001/
  score-input.md   the only file sent to the scoring tool
  score-result.md  complete tool result, source/field mapping, and ticket binding status
```

Use the next unused `review-<nnn>` and `story-<nnn>` names. A new assessment after Story content changes, or an explicitly requested fresh assessment, gets a new `revision-<nnn>` directory with the same two filenames. Do not overwrite a scoring file already used by the tool: the database binding depends on that exact path and content. Do not create `original.md`, `original.json`, `handoff.json`, or separate attempt files. Keep the original source file untouched, and record its actual path, Jira key, or message reference in `score-result.md`.

Build `score-input.md` from identified source fields in this order. The angle-bracket lines below describe where original values go; never send the placeholders to the scoring tool:

```markdown
# <actual Summary value>

## Description
<actual Description text>

## Acceptance Criteria
<actual AC text>

## Other Fields
### <actual field name>
<actual field value>
```

Use the actual Summary as the `#` title, not `# Summary` followed by its value. If Summary is absent, do not invent a title; record the missing field in `score-result.md`. Use exactly one standalone `## Acceptance Criteria` heading. Map a clearly identified source heading or field named `AC`, `Acceptance Criteria`, `Acceptance Criteria 1`, `验收标准`, or a confirmed Jira wiki equivalent to that heading. If AC text is embedded in Description, move that section once into the AC section; do not duplicate its text. Preserve the original field name or heading in `score-result.md`. When several AC fields exist, combine them only when their identity and order are clear; otherwise report the ambiguity rather than silently picking one.

Include `## Other Fields` only when other fields are present. Give each one its own `###` heading and keep its value separate from the heading. This section must start at `##` level so it cannot be mistaken for part of the AC section. Preserve every source value's wording, identifiers, Given/When/Then, lists, links, and order within its section. Convert only presentation syntax, such as Jira wiki headings or color tags, and restore paragraph/field boundaries without joining words (`BackgroundThe` is invalid). Do not invent ACs, IDs, BDD steps, business facts, or a missing Summary. If the original Story is already Markdown, use its existing content and change only the headings/boundaries needed for this structure.

Before scoring, read back `score-input.md` and compare it with the source file, Jira fields, or pasted text. Confirm that every source value appears unchanged and exactly once, with only headings, field separators, and presentation markup changed. Record that field mapping and any presentation-only changes in `score-result.md`. If a field's identity is unclear or a lossless conversion cannot be verified, stop before scoring and explain what needs clarification.

## Score and retain the result

For each candidate, call `nodejs-base-mcp.score_requirement_markdown` with its verified workspace-relative **`score-input.md`** path. The tool reads that file directly:

```json
{"relativePath":"outputs/user-story-reviews/review-001/story-001/revision-001/score-input.md"}
```

Omit `force_review` and `metadata` normally. A cache hit is valid. Set `force_review` only if the user explicitly requests a fresh assessment of unchanged content, using the live schema's type. Within the same workflow, reuse a complete response for an unchanged Story; saving a record or binding a ticket must not trigger another score call. Score changed content separately.

Parse the documented single JSON text content block, not the outer MCP wrapper. If only a decoded scoring object is available, serialize the entire object once without claiming it is byte-identical to the server text. Preserve unknown fields. Never calculate `finalScore` from sub-scores or substitute `finalStatus` for it.

Write `score-result.md` with the Story label, source reference, actual `score-input.md` path, source-to-section mapping, normalization status, tool status, and **complete JSON scoring object** in a fenced block. Preserve raw JSON text verbatim when available. If the runtime returns a wrapper with a clear `result` object, use that object as the score and preserve the wrapper separately within the same file. Read back both files and the supplied upstream file when there is one. If the source or scoring file changed during scoring, mark the result stale and prepare a new revision. Never use `score-result.md` as the binding path.

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

Use the full Ticket **Key**, never a numeric-only ID. `markdownReviewRelativePath` must equal this Story's `score-input.md` path recorded in `score-result.md` and passed to the score tool. Pass no `description`, `summary`, `dynamicFieldsJson`, `attachmentsJson`, `issuesJson`, or other Jira-change fields. Do not wrap the call in `request` or `requestJson`. For a batch, make one binding call per Story; never use one shared path for multiple tickets.

Confirm binding from the returned `markdownReview` result. Transport success alone is insufficient; numeric `status=400`, partial, missing, or unknown results are not success. Keep the saved score and ticket key, then add the actual binding result to `score-result.md` without altering its raw scoring JSON. Report binding as pending or failed, and do not automatically replay an uncertain call. If Jira export creates a ticket later, give the export skill the `score-input.md` and `score-result.md` paths; it can bind the new key after Jira creation succeeds. Do not repeat an already confirmed binding to the same ticket.

## Tell the user the actual scoring details

For each Story, report these in this order:

1. Story label, source reference, scoring file path, and own Jira key if known. State how source field headings were normalized.
2. Database scoring status and exact returned `finalScore`, `finalStatus`, `qualityBand`, and `summary` when present.
3. Every returned section/dimension/breakdown score, strengths, critical issues, warnings, evidence gaps, parse errors, AC counts, and other scoring diagnostics. Preserve tool wording and labels; never fill in missing fields.
4. Ticket binding status: **bound**, **pending** (with reason), **failed** (with actual error), or **no ticket**.

Also give the user the **complete scoring-tool result**: show its full decoded JSON for each Story when practical; for a large batch, link each complete, untruncated record and summarize every substantive returned field per Story. Never present the outer MCP envelope as the scoring object. Keep scoring JSON, audit identifiers, and internal paths out of Jira-visible Story fields.

This standalone review has no 75- or 76-point export gate. Any separate SDLC workflow keeps its own scoring and review rules; this skill's result does not silently override them.
