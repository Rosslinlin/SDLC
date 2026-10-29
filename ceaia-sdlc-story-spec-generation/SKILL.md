---
name: ceaia-sdlc-story-spec-generation
description: Generate or update evidence-backed CEAIA Stories, executable Test Cases and matching SPECs; independently review the content, score the final Story with its SPEC review status, and hand validated artifacts to the Jira write gate.
---

# CEAIA Story and SPEC Generation

Bundle revision: 5.3 text-only — 2026-10-27. Target runtime: GPT-6 Luna.

Own intake, planning, generation, current-state persistence and evidence-supported repairs. The independent reviewer is `ceaia-spec-review`; Jira writes belong to `ceaia-sdlc-jira-export`.

## Bundled SDLC orchestration

This Skill is the mandatory first Skill for the bundled SDLC workflow. At entry, lock the route and planned Skill sequence before performing Jira-export discovery:

1. `ceaia-sdlc-story-spec-generation` owns intake, planning, Story/Test Case/SPEC generation, targeted repairs, and the final workspace Story scoring call after review.
2. `ceaia-spec-review` performs the independent read-only content review after the candidate artifact set is complete and before the final scoring call.
3. `ceaia-sdlc-jira-export` runs only after every selected candidate has current content-review PASS, verified SPEC-header synchronization and a passing final Story score. It exports Story only and preserves the separate post-write database binding call.

The bundled SDLC workflow is end-to-end by default. Whether or not the user's initial input says “push to Jira”, “export to Jira” or equivalent, set `jiraExportRequested: true` and continue through Jira create/update after every selected candidate passes content review, SPEC-header synchronization and final Story scoring. Do not load the exporter, search Jira projects, query issue types, ask for Epic Link, or ask other destination-field questions during initial intake; defer all Jira destination resolution until the scored handoff reaches step 3. Stop after workspace artefacts only when the user explicitly requests preparation-only/no-Jira output, explicitly stops, or the caller explicitly declares the whole workflow preparation-only. A task's internal generation-only stage partition does not change `jiraExportRequested` and does not opt the whole workflow out of its later Jira stage.

## Load for the current phase

1. Always read [workflow-contract](references/workflow-contract.md), the single authority for paths, current versions, invalidation, repair budget and handoff.
2. Intake: [source-enrichment](references/source-enrichment.md); for updates also [existing-jira-update](references/existing-jira-update.md).
3. Planning: [planning-and-scoring](references/planning-and-scoring.md), [business preservation checklist](references/business-planning-checklist.md) and [planning template](templates/planning-template.md). Read the [scoring-tool contract](references/scoring-tool-contract.md) at the post-review scoring step, not during intake.
4. Generation/repair: [artifact-generation-rules](references/artifact-generation-rules.md), [metadata and worked examples](references/metadata-and-examples.md), plus the relevant [Story](templates/story-template.md), [Test Case](templates/test-case-template.md) or [SPEC](templates/spec-template.md) template.
5. Text-only checks: read and perform the [verification checklist](references/validation.md) using existing platform tools. Do not generate or execute scripts.

Resource links resolve from this Skill directory. Artifact paths resolve from the conversation workspace, never the Skill installation directory. Read one candidate's evidence and artifacts at a time; retain the selected candidate list in compact batch state.

## Entry and sequence

Classify `new_story` or `existing_ticket_update`. A ticket used only as evidence is not automatically an update target.

If a ticket to be read lacks an explicitly confirmed source, the next interaction is `ask_user_question`: one independent selector per unresolved ticket, options `WPB | ALM | DATA | FCR | GO`. Wait before Jira access. Reuse explicit per-ticket confirmation within this request; never infer another ticket's source.

For each candidate:
1. Inventory evidence, resolve material gaps and create planning trace. Updates preserve original ticket baseline and complete attachment intake.
2. Determine identity, exact paths, a compliant `<name>-spec.md` filename for every intended SPEC and attachment action before writing Story.
3. Write the complete Story with exactly `## Acceptance Criteria` and stable AC IDs containing Given/When/Then. Perform source-backed completeness and parser-shape checks without inventing a score or calling the scoring tool yet.
4. Generate Test Case, validate titles and executable coverage, then assemble SPEC from current sibling content. Updates retain every distinct reviewed replacement and explicit attachment mapping.
5. Run one same-stage mechanical check and independent read-only `ceaia-spec-review` against actual evidence. A fresh read-only subagent or task-provided independent reviewer satisfies this; do not substitute generator self-approval. If unavailable, return WAITING_TOOL.
6. Persist each review in a numbered `.ceaia-work` record. Use at most one consolidated automatic repair round, shared with later score-driven findings, under the [repair policy](references/workflow-contract.md#repair-budget-and-transitions). Repair only affected files; a Story repair refreshes dependent Test Case/SPEC and requires independent re-review. SPEC-only and test-only repairs do not rewrite Story. If correction is still needed after that round, return the blockers and wait rather than editing or launching another reviewer.
7. After current content-review PASS, synchronize every intended SPEC `Review Result` header and read back the complete files. Check global source coverage. Map the verified primary SPEC `Result: PASS` to the scorer's exact `specFileStatus: Pass`; its current workspace-relative path is `specFilePath`; a failed synchronization blocks scoring and jiraReady.
8. Read back the unchanged final `STORY.md` and primary SPEC, then call `nodejs-base-mcp.score_requirement_markdown` in workspace mode with `relativePath` for Story, case-sensitive `specFileStatus: Pass` mapped from the verified SPEC `Result: PASS`, and that SPEC's `specFilePath`. This applies to both new Stories and existing-ticket updates; a known Jira key is not the scoring input. Omit optional force_review/metadata. Save the complete response in a numbered record. A cache hit may reuse the score while storing this invocation's SPEC metadata. Require boolean `ok: true`, numeric `finalScore >= 76` and intact AC/parser recognition. A supported score-driven defect may use only the remaining shared repair allowance, followed by independent re-review before scoring the revised Story; an exhausted allowance means wait, not another edit. Before readiness, apply [SPEC score display](references/spec-score-display.md) to write the actual total and bind the final primary SPEC revision through the normal cached metadata call only when needed.
9. Set jiraReady for all candidates, and updateReady only for valid updates, when the current review, synchronized SPEC, final score metadata and global coverage all agree. Then continue automatically to `ceaia-sdlc-jira-export` for Jira-wiki rendering, user-workspace profile reuse, settings confirmation, final preview/preflight, direct export and separate database binding. Supply the completed intake baseline/attachment pointers for updates; export does not routinely retrieve them again. Absence of Jira-export wording is not a stopping condition.

## Invocation boundary

Default is bundled generation, review and validated Jira handoff. If the caller explicitly assigns only generation and separate later review/push steps, return `nextAction: independent-review` after generation and prechecks. Do not also execute those later steps. No task JSON redesign is required.

Resume from validated current state on every entry; do not unconditionally repeat scoring, review or writes. A pre-change score without the verified final SPEC metadata is not a complete final-score handoff; invoke the tool once with current metadata and allow its normal cache. Generation owns content repair even when a later stage reports the problem.

## Output and waiting

Return current artifact paths, candidate statuses, blockers and one concrete next action. For every SPEC mentioned in the final conversation, read back the actual saved file and use its exact basename and Workspace path; the displayed name must match the saved SPEC, its internal File Name field and current state. Do not show a planned, abbreviated or old name as the final artifact. Findings use complete workspace-relative paths and known lines. Only the actual numeric Story Score may appear in the designated SPEC header; source paths, raw scoring/audit details and workflow state stay internal and never enter Jira issue fields.

Use `ask_user_question` only for missing business facts, unresolved Jira selections, the per-export SDLC settings confirmation or supported recovery choices. If that tool is unavailable, explain once and return the exact question with a waiting state; do not loop. The validated SDLC gateway has no separate approval-tool step: after its final preview, payload/read-back checks and attachment preflight pass, it calls the Jira export tool directly.

Preparation completion is distinct from whole-workflow success. Whole-workflow success requires all intended writes confirmed, or explicit user stop. Waiting is a resumable pause.
