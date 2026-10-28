---
name: user-story-reviewer
description: Review and score Jira or locally authored/revised Stories. Keep raw Jira imports in Jira mode even when long input is saved to Workspace; retain Workspace scoring for actual local revisions after export. Route writeback by the actual assessment source without a sync-confirmation popup.
---

# User Story Reviewer

**Assessment requests:** invoke `nodejs-base-mcp.score_requirement_markdown` for every Story requested for review before reporting a new score. A saved result or model judgment never replaces this call. A writeback-only request skips new scoring and selects four or five fields from the target assessment's source.

## Select the action and content source

Resolve intent before choosing a scoring mode:

1. **Writeback only:** an explicit request such as “sync this score to Jira” or “finish the missing writeback”, without new scoring or Story content changes, enters [score writeback](references/jira-score-sync.md). First identify the assessment referred to: Workspace `relativePath` means five-field path binding after its content write succeeded; Jira `jira_number/source` means four fields. A pending current Workspace binding takes priority over classifying the request from an existing Ticket alone. Resolve from actual calls, result records or confirmed context; a local score-record file is not mandatory. Ask for ambiguous source/target or missing inputs. Do not rescore, replay a successful content write, or ask whether to sync again.
2. **Assess current Jira:** use `jira_number` and `source` for a selected Jira target, including a ticket alone, pasted/exported Jira data or long crawler output saved as a Workspace attachment/file. Saving, truncating into a file or presentation-only conversion does not turn Jira input into a local Story revision. Read the carrier file as needed to identify its primary Issue; never pass its path as scoring `relativePath` merely because it exists or is Markdown.
3. **Assess current Workspace:** use the current Markdown for a locally authored Story or an explicitly selected local business revision, including a Story generated and exported earlier in this conversation that is now being edited locally. Use observed authoring/edit history or the user's clear selection of a local draft, not file existence. A generic request to score an uploaded Jira dump does not select a local revision. The known Ticket remains its export/update target.
4. **Assess ticketless input:** convert the supplied Story to Workspace Markdown and score it as before.

Explicit content-source instructions take priority. A historical or auto-saved file alone does not override Jira input; a clear request to assess a local revision must not fall back to older Jira content. If provenance, primary target or mapping is unclear, ask. Select primary Issue keys from the original request/crawl target or each Issue record's own identity; Epic, Parent, linked tickets and keys inside descriptions/comments are references, not additional targets. Separate multiple primary Issue records actually requested for review, keep their sources/calls separate, and deduplicate identical targets.

## Tools

- `nodejs-base-mcp.score_requirement_markdown`: scores either a workspace Markdown file or one Jira issue and persists the service result/audit record.
- `java-base-mcp.exportJiraByDynamicFields`: automatically uses four fields after Jira-mode scoring; for explicit writeback requests, uses the source-dependent four/five-field rules in [score writeback](references/jira-score-sync.md).
- Workspace read/write tools for selected local inputs, conversions and result records.
- `ask_user_question`, or the platform's available input UI, to collect missing Jira sources or required IDs, or recover from an actual scoring failure. Collecting a missing ID does not ask whether to sync.

Expose both named tools when installing this skill. Direct Jira review retrieves the issue and records its Jira association in the scoring service. Its subsequent four-field call triggers backend handling. Workspace assessment itself does not invoke that four-field call; content export/update and recovery use path binding. Do not add a project-availability query, a separate Jira read or a sync confirmation popup. Generic tool success never proves Jira score fields were written.

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

For every scoring mode, save a later assessment or explicitly requested fresh assessment at the next unused `-v2`, `-v3`, etc. result name in the same candidate folder. A conversion input gets a matching version only when conversion is needed. Do not overwrite prior assessments or add source copies, manifests or handoff files. Writeback-only calls do not create a new score-result file.

## Review sequence

1. **Inventory and route.** Select writeback-only or assessment. For assessment, classify raw Jira import versus actual local authoring/revision before converting or reusing any file. Inspect the original request and Issue-identity header/fields with ordinary file reads as needed; long input, truncated chat previews or auto-saving cannot justify a Workspace fallback. Writeback-only follows its reference and bypasses scoring.
2. **Resolve sources once.** For Jira-mode scoring or writeback-only, reuse the explicitly confirmed ticket-to-source mapping. Ask once for unresolved targets: `WPB`, `ALM`, `DATA`, `FCR`, or `GO`. Do not infer from ticket prefixes or read `jira-user-info.md`. Workspace scoring needs no Jira source argument; carry a known target/source mapping for later export.
3. **Prepare only confirmed Workspace content.** After provenance routing, read [workspace input conversion](references/input-format.md) and the current authored/revised local Story, preserving its wording. Reuse a suitable file or convert when needed. Do not normalize a raw Jira dump into a score-input file or let the carrier path replace its primary Ticket.
4. **Score each assessment item with the tool.** Read [scoring modes and result records](references/scoring-and-results.md). Call `nodejs-base-mcp.score_requirement_markdown` once per assessment target with only the parameters for that route, even when a prior score file or cached result exists. Omit SPEC parameters in this version. Never substitute the model's own score or a prior file for this call; if the tool cannot be called or returns no valid score, report that scoring could not be completed. Writeback-only items bypass this step.
5. **Preserve and show every score.** Create the result only after scoring finishes, preserve the complete return, and read the saved result back. Show the Story or ticket/source, exact `finalScore`, status, returned score breakdown, key diagnostics and full-result link. Present batch scores together. A generation caller must also receive the Workspace score. For completed Jira assessments, keep the run active for the automatic trigger below.
6. **Trigger the existing-ticket update automatically.** For each Jira-route result with `ok === true` and a finite numeric `finalScore`, including 0 or a cache hit, follow [Jira update trigger](references/jira-score-sync.md) once without asking whether to sync/export. Resolve its four actual arguments; if a required ID is missing, ask a focused input question and wait, then continue automatically without a sync confirmation. Record the returned outcome in the same result file. Final conversation output focuses on the assessment; omit routine trigger messages and do not claim Jira score fields were updated. Workspace assessments finish without this call and do not collect IDs for it.

For a batch, collect every missing source before starting Jira calls so the user is not questioned one ticket at a time. A failure for one independent target does not block the others.

## Continue a local Story after Jira export

Preserve the verified mapping between the local Story/current score input, latest result, actual exported Ticket Key and confirmed source in existing result records and conversation context. A new local edit requires a new Workspace scoring call. For each Workspace assessment intended for export/update, record its actual `relativePath`, result version/invocation reference when available, target/source, content-write outcome and binding status. Each new assessment starts with binding pending, even if the path and Ticket match an earlier successfully bound assessment. Do not add another state file.

When the user/caller requests export or update, return that current assessment mapping and scored content to `jira-createissue-helper`. For an already exported Story, update the same Ticket. The helper writes the current content and then performs the mandatory five-field binding with this assessment's actual input path. Keep normal helper preview/confirmation rules; its Jira baseline/metadata reads must not replace the local scoring source. Record a missing binding as pending. If the user subsequently asks to finish writeback after this content write succeeded, recover only the five-field binding under [score writeback](references/jira-score-sync.md); do not change the source to Jira mode.

## Boundaries

Do not create Jira issues or edit Story business content. The helper owns normal content writes and their subsequent Workspace bindings; reviewer may recover a verified missing Workspace binding on an explicit writeback request. Four-field calls apply to Jira-mode assessments, not every existing Ticket. Local content changes still follow the Workspace handoff. Respect an explicit no-Jira-write instruction; otherwise no sync confirmation is required. Report actual calls and known outcomes accurately.

Preserve original business wording in ticketless conversion. Do not invent Given/When/Then steps, add business value, interpret unseen images or repair requirements to raise a score.

Report the returned score separately from recognition reliability. Expose AC-count concerns and parser diagnostics. Never replace the tool's score with an estimate or force a fresh review merely because a cached or low score was returned.
