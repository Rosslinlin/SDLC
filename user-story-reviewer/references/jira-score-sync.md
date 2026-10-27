# Automatic Jira update trigger

Read this only after a Jira-route call to `nodejs-base-mcp.score_requirement_markdown` has returned `ok === true` and a finite numeric `finalScore`. A low score, including 0, remains eligible. A failed or indeterminate assessment is not eligible. Ticketless workspace reviews never use this flow.

The Jira scoring route already persists the assessment and its ticket association in the scoring service. This subsequent call triggers backend update handling automatically. Only some projects support Jira score-field updates; a generic successful response confirms tool execution, not that those fields were written. Project availability is handled by the backend.

## Automatic invocation and inputs

After saving the completed Jira assessment, perform this trigger once per eligible ticket without a sync/export confirmation, preview popup or project-support check. Respect an explicit user instruction prohibiting Jira writes. Reuse the current user's actual `staffId` when reliably available; otherwise ask for it with `ask_user_question` or the platform's input UI and wait. Collect missing required IDs in one focused question when possible, then resume the call automatically. Do not read `jira-user-info.md`, invent IDs, or treat missing identity as permission to skip the trigger silently. These input questions collect required values; they do not offer a sync yes/no choice.

Use the current conversation's real ID for `conversationId`, resolving it from trusted platform context first. If it is unavailable, request the actual current conversation ID; never derive it from a workspace path or fabricate one. Until all required inputs are available, record the trigger as pending and preserve the completed score; resume after the answer without rescoring. If the platform cannot expose the ID and the user cannot supply it, or the tool is unavailable, report the precise blocker. Convert the confirmed scoring `source` to lowercase `almType`: `WPB`→`wpb`, `ALM`→`alm`, `DATA`→`data`, `FCR`→`fcr`, `GO`→`go`.

## Exact call

For each eligible existing ticket, call `java-base-mcp.exportJiraByDynamicFields` once with exactly four direct, nonempty strings:

| Argument | Actual value |
| --- | --- |
| `staffId` | Current user's verified staff ID |
| `almType` | Lowercase mapping of the source just scored |
| `issueIdOrKey` | The exact existing Ticket Key just scored |
| `conversationId` | Actual current conversation ID |

Use the ticket key that was just scored. Omit `markdownReviewRelativePath`; do not substitute the score-result file, an uploaded Story, or any workspace Story path. Also omit `summary`, `description`, `dynamicFieldsJson`, attachments and other Jira create/update arguments. This four-field reviewer sync is distinct from the five-field SDLC Markdown-review binding route; do not change the latter.

## Record the outcome

Keep the scoring tool's complete response and score unchanged. In the same `<TICKET>-score-result.md`, record whether the automatic trigger was invoked, its exact actual arguments excluding secrets, and the complete returned response or observed error; read back the updated file. If not invoked, record the missing prerequisite or explicit no-write instruction. Do not create another per-story file.

Record tool execution separately from any Jira score-field effect. Generic `success`, `pass`, a Ticket Key or absence of an error does not prove score-field synchronization. Do not infer that an unconfirmed project is enabled or disabled. Treat numeric `status=400` as a business failure even when MCP `isError=false`.

Routine conversation output shows the score and assessment diagnostics, without a sync/export confirmation, trigger progress messages or a claim that Jira score fields were updated. Required-ID questions above still apply. Keep trigger evidence available in the result file and answer accurately if asked. A failed, partial, skipped or uncertain trigger does not invalidate the score or prompt automatic rescoring/retry; surface an actual error briefly only when user action is needed.
