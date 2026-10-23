# Optional Jira score sync

Read this only after a Jira-route call to `nodejs-base-mcp.score_requirement_markdown` has returned `ok === true` and a finite numeric `finalScore`. A low score, including 0, remains eligible. A failed or indeterminate assessment is not eligible. Ticketless workspace reviews never use this flow.

The Jira scoring route already persists the assessment and its ticket association in the scoring service. The operation here has a different backend purpose: synchronize the completed score to Jira when the user requests it. Do not describe this optional call as necessary for the original score/database association.

## Confirmation and inputs

Show each eligible ticket, its confirmed source, and its exact returned `finalScore`; ask whether to sync the score to Jira. For a batch, use one question that lets the user choose all, some, or none of the eligible tickets. Do not infer approval from the original scoring request. If the current user's `staffId` is not already reliably available, request it in that same question rather than querying `jira-user-info.md` or inventing an ID. Do not call the sync tool for declined or unanswered tickets.

Use the current conversation's real ID for `conversationId`; do not derive it from a workspace path or fabricate one. If a required value is unavailable after confirmation, explain what is missing and leave that ticket unsynced. Convert the confirmed scoring `source` to the export tool's lowercase `almType`: `WPB`→`wpb`, `ALM`→`alm`, `DATA`→`data`, `FCR`→`fcr`, `GO`→`go`.

## Exact call

For each confirmed existing ticket, call `java-base-mcp.exportJiraByDynamicFields` once with exactly these four direct, nonempty arguments:

```json
{
  "staffId": "<actual current-user staff ID>",
  "almType": "wpb",
  "issueIdOrKey": "AIWPB-8052",
  "conversationId": "<actual current conversation ID>"
}
```

Use the ticket key that was just scored. Omit `markdownReviewRelativePath`; do not substitute the score-result file, an uploaded Story, or any workspace Story path. Also omit `summary`, `description`, `dynamicFieldsJson`, attachments and other Jira create/update arguments. This four-field reviewer sync is distinct from the five-field SDLC Markdown-review binding route; do not change the latter.

## Record the outcome

Keep the scoring tool's complete response and score unchanged. In the same `<TICKET>-score-result.md` file, add the user's sync decision and, if called, the actual sync tool response/status; read back the updated file. Do not create another per-story file. Report scoring and Jira sync as separate outcomes. A failed, partial or uncertain sync does not invalidate the score and does not justify rescoring or automatically repeating the sync call. Report the actual failure or uncertainty and ask how the user wants to proceed. Only claim Jira sync succeeded when the tool response confirms it.
