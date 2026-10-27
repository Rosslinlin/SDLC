# Automatic Jira update trigger

Enter through either of two paths: (1) the user explicitly requests only a score update/sync for an existing Jira Ticket, without new scoring or Story content changes; or (2) a Jira-mode scorer call has returned `ok === true` and a finite numeric `finalScore`, including 0 or a cache hit. Update-only requests do not call the scorer or require a local score record. Failed/indeterminate scoring does not qualify for the automatic post-score trigger.

This four-field call triggers backend handling for the existing Ticket. Only some projects support Jira score-field updates; generic success confirms tool execution, not final score-field writes. The call has no score-value or review-ID parameter; do not manufacture a score or claim which historical assessment the backend selected. A request to rescore must return to the selected assessment route first.

Workspace assessments, including locally revised Stories with exported Ticket mappings, do not automatically enter this four-field flow. Their requested content updates and five-field path bindings belong to the existing export helper. A separate explicit existing-ticket score-update request may use update-only mode, without pretending that a local Story content update has occurred.

## Automatic invocation and inputs

For an explicit update-only request, resolve the existing Ticket Key and source from the request or confirmed conversation context. Ask if “this score” has no unambiguous target; do not choose an unrelated Ticket. After a Jira assessment, reuse the ticket/source just scored. Perform one trigger per target without a sync confirmation, preview popup or project-support check. Respect an explicit no-Jira-write instruction. Reuse the actual `staffId` when reliably available; otherwise ask and wait. Collect missing inputs together when possible, then continue directly. Do not read `jira-user-info.md`, invent IDs, or silently skip missing inputs.

Use the current conversation's real ID for `conversationId`, resolving it from trusted platform context first. If unavailable, request the actual current ID; never derive it from a file path. Until inputs are available, keep the trigger pending, preserve any existing score and resume after the answer without rescoring. Report an unavailable tool or identity that cannot be supplied. Convert the explicitly confirmed target `source` to `almType`: `WPB`→`wpb`, `ALM`→`alm`, `DATA`→`data`, `FCR`→`fcr`, `GO`→`go`. Never infer source from the Ticket prefix.

## Exact call

For each eligible existing ticket, call `java-base-mcp.exportJiraByDynamicFields` once with exactly four direct, nonempty strings:

| Argument | Actual value |
| --- | --- |
| `staffId` | Current user's verified staff ID |
| `almType` | Lowercase mapping of the confirmed target source |
| `issueIdOrKey` | The exact existing Ticket Key requested or just scored |
| `conversationId` | Actual current conversation ID |

Use that confirmed target. Omit `markdownReviewRelativePath`, `summary`, `description`, `dynamicFieldsJson`, attachments and all other Jira content-change arguments. Do not send a manual score, local file path or workflow mode. The helper's five-field Workspace binding remains a separate route.

## Record the outcome

Keep any scoring response and score unchanged. Append the actual trigger arguments excluding secrets and full response/error to the matching existing score-result file when available; read back an edited record. With no local result, retain the tool response in the conversation and do not create a fictitious assessment or score-result file. Record missing prerequisites or an explicit no-write instruction accurately.

Record tool execution separately from any Jira score-field effect. Generic `success`, `pass`, a Ticket Key or absence of an error does not prove score-field synchronization. Do not infer that an unconfirmed project is enabled or disabled. Treat numeric `status=400` as a business failure even when MCP `isError=false`.

After normal scoring, show the assessment without routine trigger messages or claims that Jira score fields were written. For an explicit update-only request, acknowledge a successful invocation as “Update request submitted for <actual Ticket Key>”; report an actual failed/unknown invocation accurately. Required-input questions still apply. Neither mode automatically retries the trigger or rescoring; preserve the actual outcome and answer accurately if asked.
