# Score writeback by assessment source

Use this reference for an explicit score-writeback request, or automatically after a Jira-mode scorer result with `ok === true` and finite numeric `finalScore` (including 0/cache hits). Writeback-only requests never call the scorer. Resolve the assessment the user refers to from actual calls, result records or confirmed context; a local score-record file is not mandatory.

## Choose the call before collecting arguments

| Referenced assessment | Follow-up |
| --- | --- |
| Workspace call using `relativePath`, including a local Story revised after earlier Jira export | Five fields, including that actual scoring input path, after this assessment's content was successfully written to the mapped Ticket. A pending current Workspace binding takes priority when the user says “finish writeback”. |
| Jira call using `jira_number` and `source` | Four fields, without a Markdown path. This also applies to an explicit request referring to an existing Jira-mode assessment, with no new scoring. |
| Source, target assessment or mapping is unclear | Ask a focused question. An existing Ticket Key alone does not establish Jira-mode scoring. Do not fall back to four fields, invent a path or rescore to avoid the question. |

Only use evidence for the selected Ticket/assessment, not another Story's latest score or an earlier version. A newer Jira-mode assessment must not inherit an older Workspace path. Confirmed user/context information can identify Jira-mode scoring without a local result file. Do not claim which historical score the backend selected: the API has no score-value or review-ID argument.

Classify a completed assessment by the scorer arguments actually sent. A raw Jira dump's carrier path does not prove Workspace scoring, and later saving a file does not change a Jira-mode call. If an earlier call incorrectly used `relativePath` for a dump, preserve its actual Workspace provenance; do not relabel it as Jira mode. Apply the corrected source routing before the next requested assessment.

Workspace assessment itself does not immediately trigger this tool. Its requested content export/update remains with `jira-createissue-helper`; after successful content write, that helper must complete the five-field binding. A subsequent explicit request can recover a missed binding under the rules below. Known SDLC handoffs remain with their dedicated gateway; do not bypass SDLC gates through this reviewer.

## Resolve required values

Resolve the Ticket Key, confirmed Jira source and real `staffId` from the request or trusted context. Resolve `conversationId` from actual platform context: Workspace binding must use the scoring Workspace's verified conversation identity; Jira-mode triggering uses the current conversation identity. Never derive an ID from a file path. Ask together for missing required inputs and wait, then continue without asking whether to sync. Do not read `jira-user-info.md` or infer source from the Ticket prefix.

Map confirmed source to `almType`: `WPB`→`wpb`, `ALM`→`alm`, `DATA`→`data`, `FCR`→`fcr`, `GO`→`go`. Respect an explicit no-Jira-write instruction. An unavailable tool/identity remains a precise pending blocker, not a fabricated successful call.

## Direct arguments

Both variants call `java-base-mcp.exportJiraByDynamicFields` with these four nonempty strings:

| Argument | Actual value |
| --- | --- |
| `staffId` | Current user's verified staff ID |
| `almType` | Lowercase mapping of the confirmed target source |
| `issueIdOrKey` | The exact existing Ticket Key |
| `conversationId` | Verified conversation identity for the selected variant |

For Workspace binding, add exactly one fifth string: `markdownReviewRelativePath` equals this assessment's actual `relativePath`. It is the Story scoring input, never the score-result file, SPEC or a newly invented Jira dump. For Jira-mode triggering, omit this fifth argument.

Neither variant sends Summary, Description, dynamic fields, attachments, batch data, a manual score or workflow state.

## Recover only a missing Workspace binding

Follow the binding-only recovery section of the helper's [generic Story score binding](../../jira-createissue-helper/references/generic-story-score-binding.md). Verify the current assessment/input and the successful content-write response for the same Ticket/revision. A previous assessment's binding success does not complete a newer one, even at the same path and Ticket Key.

If the current content write succeeded and this binding was never called, execute only its five-field call. Do not repeat create/update/attachments or rescore. If content has not yet been written, return the pending content update to the helper; do not bind a local revision to an older Jira write. If this assessment's binding already succeeded, retain that outcome. For failed/partial/unknown attempts, preserve their evidence and reconcile before a user-directed recovery; do not automatically repeat an uncertain call.

## Record and report

Keep all score responses unchanged. Append this assessment's source, actual arguments excluding secrets and full response/error to the matching result record when available; otherwise retain actual tool evidence in the conversation without creating a fictitious score-result file. Keep content-write and binding outcomes separate. Workspace association is confirmed only by a successful `markdownReview` result; generic success or a Ticket Key alone is insufficient. Numeric `status=400` is a business failure even if MCP `isError=false`.

After scoring, keep routine trigger details in the record. For an explicit writeback request, acknowledge a successful invocation as “Update request submitted for <actual Ticket Key>” and report actual errors/uncertainty accurately. Neither variant's success proves that a project's Jira score fields were written. Do not add a project-support check, rescore or automatically retry.
