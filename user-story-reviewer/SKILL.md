---
name: user-story-reviewer
description: Review and score one or more user stories with the platform scoring tool. Send every Story that includes a Jira ticket number through direct Jira review after collecting its Jira source; convert only ticketless Story input to workspace Markdown. Save and explain the complete returned assessment without creating or editing Jira issues.
---

# User Story Reviewer

Score each Story through exactly one route:

- **Jira route:** any Story input containing a Jira ticket number, including a ticket supplied alone or inside uploaded JSON/text.
- **Workspace route:** Story input with no Jira ticket number.

Do not decide that a detected ticket is merely a reference and silently choose workspace mode. If one input contains several distinct ticket numbers, treat each number as a Jira review target. Keep batch items, sources, calls and results separate.

## Tools

- `nodejs-base-mcp.score_requirement_markdown`: scores either a workspace Markdown file or one Jira issue and persists the service result/audit record.
- Workspace read/write tools for ticketless input and all local result records.
- `ask_user_question`, or the platform's available input UI, to collect missing Jira sources in one batch question or recover from an actual failure.

Expose the scoring tool when installing this skill. Do not expose or call a separate Jira read, Jira export, ticket-binding or database-write tool. Direct Jira review retrieves the Jira issue and records its Jira association itself. Workspace review has no Jira ticket to bind.

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
4. **Score.** Read [scoring modes and result records](references/scoring-and-results.md). Call the scoring tool once per target with only the parameters for that route. Omit SPEC parameters in this version.
5. **Save and report.** Create the result only after the call finishes. Preserve the complete return, explain the actual score and diagnostics, and state whether the tool used Jira or workspace mode. Read the saved result back before reporting success.

For a batch, collect every missing source before starting Jira calls so the user is not questioned one ticket at a time. A failure for one independent target does not block the others.

## Boundaries

Do not create or update Jira content. Do not make a second call to associate a Jira ticket or write the score to another database tool. The Jira route already records the association; the workspace route intentionally has no Jira ticket.

Preserve original business wording in ticketless conversion. Do not invent Given/When/Then steps, add business value, interpret unseen images or repair requirements to raise a score.

Report the returned score separately from recognition reliability. Expose AC-count concerns and parser diagnostics. Never replace the tool's score with an estimate or force a fresh review merely because a cached or low score was returned.
