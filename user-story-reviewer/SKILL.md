---
name: user-story-reviewer
description: Review and score user stories by always calling the platform scoring tool through Jira or workspace input. For existing Jira tickets, offer an optional, user-confirmed score sync to Jira after scoring; keep ticketless stories on the workspace Markdown route.
---

# User Story Reviewer

**Mandatory:** invoke `nodejs-base-mcp.score_requirement_markdown` for every Story requested for review before reporting any score. A saved result or model judgment is never a substitute for this call.

Score each Story through exactly one route:

- **Jira route:** any Story input containing a Jira ticket number, including a ticket supplied alone or inside uploaded JSON/text.
- **Workspace route:** Story input with no Jira ticket number.

Do not decide that a detected ticket is merely a reference and silently choose workspace mode. If one input contains several distinct ticket numbers, treat each number as a Jira review target. Keep batch items, sources, calls and results separate.

## Tools

- `nodejs-base-mcp.score_requirement_markdown`: scores either a workspace Markdown file or one Jira issue and persists the service result/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: for an existing Jira ticket only, synchronizes its completed score to Jira after the user confirms, using the four-field call in [Jira score sync](references/jira-score-sync.md).
- Workspace read/write tools for ticketless input and all local result records.
- `ask_user_question`, or the platform's available input UI, to collect missing Jira sources, ask whether to sync the displayed score to Jira, or recover from an actual failure.

Expose both named tools when installing this skill. Direct Jira review retrieves the Jira issue and already records its Jira association in the scoring service. The optional second call is only for user-confirmed Jira score sync; it is not needed for database association. Do not call a separate Jira read tool. Workspace review has no Jira ticket to sync.

## Workspace layout

Use one folder per review target:

```text
outputs/user-story-reviews/
  aiwpb-123/
    AIWPB-123-score-result.md
  story-001/
    story-001-score-input.md
    story-001-score-result.md
```

For a Jira target, use the lowercase ticket key as the folder and the actual key as the filename prefix. If the same key already exists for a different Jira source, prefix the new folder with its lowercase source to prevent a collision. Do not create a score-input file; the scoring tool reads Jira directly.

For a ticketless Story, use the next unused `story-001`, `story-002`, etc. folder and prefix. Create its input first and its result only after the scoring call returns or fails.

For an explicitly requested fresh assessment, use the next unused `-v2`, `-v3`, etc. suffix for the new result and, for workspace mode, its matching input. Do not overwrite a prior assessment. Keep at most the files required by the selected route; do not add review/revision subfolders, source copies, manifests or handoff files.

## Review sequence

1. **Inventory and route.** Separate the user's input into Story items and detect ticket numbers. Every item with a ticket number uses the Jira route; only items without one use the workspace route. Deduplicate an identical ticket/source pair within the same request.
2. **Resolve Jira sources once.** Use an explicit source supplied with the current request. For every Jira target whose source is absent or uncertain, ask one combined question listing all affected tickets and request a source for each: `WPB`, `ALM`, `DATA`, `FCR`, or `GO`. Do not infer source from the ticket prefix and do not read `jira-user-info.md` or other saved defaults.
3. **Prepare ticketless input only.** Before a workspace call, read [workspace input conversion](references/input-format.md), preserve the source wording and verify the saved Markdown. Jira input, including uploaded JSON, is not converted to Markdown.
4. **Score with the tool, without exception.** Read [scoring modes and result records](references/scoring-and-results.md). Call `nodejs-base-mcp.score_requirement_markdown` once per target with only the parameters for that route, even when a prior score file or cached result exists. Omit SPEC parameters in this version. Never substitute the model's own score or a prior file for this call; if the tool cannot be called or returns no valid score, report that scoring could not be completed.
5. **Show every score, including newly generated Stories.** Create the result only after scoring finishes, preserve the complete return, and read the saved result back. For both routes, send a user-visible message with the Story or ticket/source, exact `finalScore`, status, returned score breakdown, key diagnostics, and a link to the complete raw result. For a batch, present the scores together. If a generation skill called this reviewer for a new ticketless Story, show that Workspace score before returning to the caller; do not leave it only in the result file or tool output. For the Jira route, this must be an intermediate message before the sync popup; keep the run active.
6. **Ask in a popup, then continue.** Only for completed numeric Jira-route assessments, read [Jira score sync](references/jira-score-sync.md) and call `ask_user_question` (or the platform's popup input UI) after the score message is visible. Ask once for a batch. Wait for the popup answer in this run; call the four-field Jira tool only for tickets the user confirms, then report its separate outcome. Workspace assessments finish after reporting the score; if this is the end of the run, include the exact score in the final answer too.

For a batch, collect every missing source before starting Jira calls so the user is not questioned one ticket at a time. A failure for one independent target does not block the others.

## Boundaries

Do not create Jira issues, edit Story content, or use the SDLC five-field Markdown-review binding call. The Jira scoring route already records the score and ticket association in the scoring service; the optional four-field sync has a distinct backend purpose. Never offer that sync for the workspace route, even if its Story may be exported later.

Preserve original business wording in ticketless conversion. Do not invent Given/When/Then steps, add business value, interpret unseen images or repair requirements to raise a score.

Report the returned score separately from recognition reliability. Expose AC-count concerns and parser diagnostics. Never replace the tool's score with an estimate or force a fresh review merely because a cached or low score was returned.
