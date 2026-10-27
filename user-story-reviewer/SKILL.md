---
name: user-story-reviewer
description: Review and score current Jira or workspace Stories, retaining local input for continued edits after Jira export. Trigger existing Jira score updates directly on explicit sync-only requests, or automatically after Jira-mode scoring, without a sync-confirmation popup.
---

# User Story Reviewer

**Assessment requests:** invoke `nodejs-base-mcp.score_requirement_markdown` for every Story requested for review before reporting a new score. A saved result or model judgment never replaces this call. An explicit update-only request uses the four-field tool directly and does not request a new assessment.

## Select the action and content source

Resolve intent before choosing a scoring mode:

1. **Update only:** an explicit request such as “sync this score to Jira” or “trigger the existing ticket's score update”, without new scoring or Story content changes, enters [Jira update trigger](references/jira-score-sync.md) directly. Resolve the existing ticket/source and required IDs from the request or confirmed context; ask only for missing inputs. Do not score, require a local score record, or ask whether to sync again.
2. **Assess current Jira:** when the user explicitly requests Jira's current version, or supplies a ticket alone/in uploaded JSON without selecting a current local revision, score with `jira_number` and `source`.
3. **Assess current Workspace:** score the actual local Markdown when the user selects that content, including a Story generated and exported earlier in this conversation that is now being edited locally. Its known Ticket Key remains the export/update target; it does not switch scoring to Jira. A request to revise that local Story and update Jira follows this route.
4. **Assess ticketless input:** convert the supplied Story to Workspace Markdown and score it as before.

Explicit content-source instructions take priority. A historical local file alone does not override a request to review Jira's current version. If the selected local file, target ticket or their mapping is ambiguous/unavailable, ask a focused question; do not substitute old Jira content for the local revision. Default Jira input with multiple distinct target keys produces separate reviews; references inside the selected local Story do not add review targets. Keep batch items, sources and calls separate, and deduplicate identical targets within the request.

## Tools

- `nodejs-base-mcp.score_requirement_markdown`: scores either a workspace Markdown file or one Jira issue and persists the service result/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: executes explicit update-only requests or automatically triggers an update after completed Jira-mode scoring, using the four-field call in [Jira update trigger](references/jira-score-sync.md).
- Workspace read/write tools for selected local inputs, conversions and result records.
- `ask_user_question`, or the platform's available input UI, to collect missing Jira sources or required IDs, or recover from an actual scoring failure. Collecting a missing ID does not ask whether to sync.

Expose both named tools when installing this skill. Direct Jira review retrieves the issue and records its Jira association in the scoring service. The subsequent four-field call automatically triggers backend update handling; generic tool success does not establish that a project's Jira score fields were written. Do not add a project-availability query, a separate Jira read or a sync confirmation popup. Workspace review does not run this trigger.

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

For Jira-mode scoring, use the lowercase ticket key as the folder and the actual key as the filename prefix. If the same key already exists for a different Jira source, prefix the new folder with its lowercase source to prevent a collision. Do not create a score-input file for this mode.

For a ticketless Story, use the next unused `story-001`, `story-002`, etc. folder and prefix. Create its input first and its result only after the scoring call returns or fails.

For a continued local Story, retain its established file identity and candidate folder even after Jira export. Reuse the selected current Markdown path when suitable for scoring; do not create a Jira-mode input merely because a ticket now exists.

For every scoring mode, save a later assessment or explicitly requested fresh assessment at the next unused `-v2`, `-v3`, etc. result name in the same candidate folder. A conversion input gets a matching version only when conversion is needed. Do not overwrite prior assessments or add source copies, manifests or handoff files. Update-only calls do not create a new score-result file.

## Review sequence

1. **Inventory and route.** Select update-only or assessment, then select the assessment's actual content source using the rules above. Update-only goes directly to its trigger reference and bypasses the scoring steps.
2. **Resolve sources once.** For Jira-mode scoring or update-only, reuse the request's or this conversation's explicitly confirmed ticket-to-source mapping. Ask once for all unresolved targets: `WPB`, `ALM`, `DATA`, `FCR`, or `GO`. Do not infer from ticket prefixes or read `jira-user-info.md`. Workspace scoring needs no Jira source argument; carry a known target/source mapping for later export.
3. **Prepare the selected Workspace content.** Read [workspace input conversion](references/input-format.md), read the actual current local file and preserve its wording. Reuse a suitable Markdown file directly; convert only when needed. Jira-mode input is not converted.
4. **Score each assessment item with the tool.** Read [scoring modes and result records](references/scoring-and-results.md). Call `nodejs-base-mcp.score_requirement_markdown` once per assessment target with only the parameters for that route, even when a prior score file or cached result exists. Omit SPEC parameters in this version. Never substitute the model's own score or a prior file for this call; if the tool cannot be called or returns no valid score, report that scoring could not be completed. Update-only items bypass this step.
5. **Preserve and show every score.** Create the result only after scoring finishes, preserve the complete return, and read the saved result back. Show the Story or ticket/source, exact `finalScore`, status, returned score breakdown, key diagnostics and full-result link. Present batch scores together. A generation caller must also receive the Workspace score. For completed Jira assessments, keep the run active for the automatic trigger below.
6. **Trigger the existing-ticket update automatically.** For each Jira-route result with `ok === true` and a finite numeric `finalScore`, including 0 or a cache hit, follow [Jira update trigger](references/jira-score-sync.md) once without asking whether to sync/export. Resolve its four actual arguments; if a required ID is missing, ask a focused input question and wait, then continue automatically without a sync confirmation. Record the returned outcome in the same result file. Final conversation output focuses on the assessment; omit routine trigger messages and do not claim Jira score fields were updated. Workspace assessments finish without this call and do not collect IDs for it.

For a batch, collect every missing source before starting Jira calls so the user is not questioned one ticket at a time. A failure for one independent target does not block the others.

## Continue a local Story after Jira export

Preserve the verified mapping between the local Story/current score input, latest result, actual exported Ticket Key and confirmed source in existing result records and conversation context. A new local edit requires a new Workspace scoring call; keep its actual `relativePath` and result bound to the edited content.

When the user/caller requests export or update, return the current scored content/path, result path and known target/source to `jira-createissue-helper`. For an already exported Story, the target remains an update of that same Ticket. The helper's existing flow writes the current content, then makes its separate five-field binding call with this assessment's actual input path. Workspace scoring does not automatically trigger a four-field call. Keep normal helper preview/confirmation rules; its Jira baseline/metadata reads must not become the content scored by this reviewer.

## Boundaries

Do not create Jira issues, edit Story business content, or execute the helper's five-field Markdown-review binding. Four-field calls apply to explicit existing-score update requests and completed Jira-mode assessments. Local Story content changes/exports follow the Workspace handoff above; they are not update-only requests. Respect an explicit user instruction prohibiting Jira writes; otherwise no sync confirmation is required. Explain actual calls and known outcomes accurately if asked.

Preserve original business wording in ticketless conversion. Do not invent Given/When/Then steps, add business value, interpret unseen images or repair requirements to raise a score.

Report the returned score separately from recognition reliability. Expose AC-count concerns and parser diagnostics. Never replace the tool's score with an estimate or force a fresh review merely because a cached or low score was returned.
