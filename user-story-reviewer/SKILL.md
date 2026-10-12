---
name: user-story-reviewer
description: Score one or more user stories with the platform Markdown scoring tool, show the complete scoring results, and bind each score to its own existing Jira ticket when known. Use for standalone scoring or after a story-generation skill; do not create or edit Jira Story content.
---

# User Story Reviewer

Read the original Story, normalize presentation without rewriting requirements, score the saved input, then save and explain the tool result. Bind a completed score to the Story's own existing Jira ticket when known. A low score does not block binding or impose an export threshold.

## Tools

- `nodejs-base-mcp.score_requirement_markdown`: reads a saved workspace Markdown path and saves the service score/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: use only the five-argument ticket-binding call in the binding reference.
- Workspace read/write tools for the conversation workspace and user workspace; a Jira read tool such as `java-base-mcp.getJiraInfos` for ticket-only input or ambiguous ticket ownership.
- `ask_user_question`, or the platform's available input UI, to collect genuinely missing identity/source information or resolve a failure.

Expose the scoring and binding tools when installing this skill. Instructions alone do not grant access. Follow live tool schemas; if a capability is unavailable, finish the available work and report the remaining step.

## Workspace layout

Use a flat folder in the active conversation workspace:

```text
outputs/user-story-reviews/
  AIWPB-123-score-input.md
  AIWPB-123-score-result.md
  story-001-score-input.md
  story-001-score-result.md
```

Choose the prefix before writing input: the full own Jira ticket key when already known; otherwise the next unused `story-001`, `story-002`, etc. A referenced parent/dependency key is not the Story's own key. Keep each Story's source identity, input path, score and ticket together in batch state.

For a changed Story or an explicitly requested fresh assessment, use the next unused suffix in the prefix, such as `AIWPB-123-v2-score-input.md` / `AIWPB-123-v2-score-result.md`. Check both names before allocating a pair. Do not reuse a different Story's files merely because ticket keys match across Jira sources.

Do not add review, Story or revision subfolders. Keep at most two files per assessment: input first, result only after the scoring call returns or fails. Keep preparation notes in conversation state until then. Do not create an empty result, original copy, manifest or handoff file.

A scored input path and its bytes stay fixed. If a ticket becomes known later, bind the original input path and record the new key in its result; do not rename the scored files. Existing records in older layouts remain usable at their original paths.

## Follow this sequence for each Story

1. **Read and identify.** Read the original file, pasted text or retrieved Jira fields. Determine the Story's own ticket from its explicit target or own-ticket field. Use Jira lookup for ambiguity; never select a dependency/example key as the target.
2. **Prepare input.** Read [input format and conversion](references/input-format.md). Map fields, keep each complete AC together, write only the input file, and read it back against the source. Every field has a `## Field name` heading; there is no catch-all field section.
3. **Score and save.** Read [scoring and results](references/scoring-and-results.md). Call the score tool with that actual relative input path. Only after the call completes, create the matching result file with the full return, source mapping and recognition checks.
4. **Bind when a ticket is known.** Read [Jira binding and reusable identity](references/jira-binding.md). Reuse current identity/source information or user-workspace `jira-user-info.md`; ask once for genuinely missing values. With the five arguments available, call directly without a settings confirmation or a binding approval popup. Ask for correction/recovery only if needed.
5. **Report.** Explain the returned score and all substantive scoring details, provide the complete result, and state the actual ticket-binding outcome. Read the saved result back before reporting success.

No own ticket: finish after saving/reporting the score and provide the same input/result paths for any later export. Failed scoring or no usable numeric score: report the failure and skip binding. An unchanged Story with an existing complete result does not need another score call merely to bind or report it.

## Boundaries

Preserve original business wording, missing information, identifiers and criterion membership. Formatting conversion must not invent Given/When/Then steps, add business value, interpret an unseen image or repair requirements to raise a score.

The result must distinguish a completed numerical assessment from recognition reliability. Expose AC-count mismatches and returned parser diagnostics; never replace the tool's score with an estimate.

This standalone route has no 75/76-point export gate. It does not invoke the SDLC export workflow, change Jira content, or write future Jira score/spec custom fields. It reuses the existing user-info file through its own binding reference; SDLC and generic export workflows retain their own behavior.
