---
name: ceaia-sdlc-story-spec-generation
description: Generate or update evidence-backed CEAIA Stories, apply the Story scoring tool, produce executable Test Cases and matching SPECs, repair affected content, and hand independently reviewed artifacts to the Jira write gate.
---

# CEAIA Story and SPEC Generation

Bundle revision: 4.1.0 text-only — 2026-09-21.

Own intake, planning, generation, current-state persistence and evidence-supported repairs. The independent reviewer is `ceaia-spec-review`; Jira writes belong to the dedicated SDLC gateway in `jira-createissue-helper`.

## Bundled SDLC orchestration

This Skill is the mandatory first Skill for the bundled SDLC workflow. At entry, lock the route and planned Skill sequence before performing Jira-export discovery:

1. `ceaia-sdlc-story-spec-generation` owns intake, planning, Story generation, scoring, Test Case/SPEC generation and repairs.
2. `ceaia-spec-review` performs the independent read-only review only after the candidate artifact set is complete.
3. `jira-createissue-helper` runs only after every selected candidate has current PASS and verified SPEC-header synchronization, and only through its dedicated SDLC gateway.

If the user's initial request ends with “push to Jira”, “export to Jira” or equivalent, treat that as the requested final outcome of this sequence. Do not load the helper, search Jira projects, query issue types, or ask destination-field questions during initial intake. Record `jiraExportRequested: true` in workflow state and defer Jira destination resolution until the reviewed handoff reaches step 3. A preparation-only caller may still stop before Jira as described below.

## Load for the current phase

1. Always read [workflow-contract](references/workflow-contract.md), the single authority for paths, current versions, invalidation, repair budget and handoff.
2. Intake: [source-enrichment](references/source-enrichment.md); for updates also [existing-jira-update](references/existing-jira-update.md).
3. Planning: [planning-and-scoring](references/planning-and-scoring.md), [business preservation checklist](references/business-planning-checklist.md) and [planning template](templates/planning-template.md). Before scoring additionally read the [supplied scoring-tool contract](references/scoring-tool-contract.md).
4. Generation/repair: [artifact-generation-rules](references/artifact-generation-rules.md), [metadata and worked examples](references/metadata-and-examples.md), plus the relevant [Story](templates/story-template.md), [Test Case](templates/test-case-template.md) or [SPEC](templates/spec-template.md) template.
5. Text-only checks: read and perform the [verification checklist](references/validation.md) using existing platform tools. Do not generate or execute scripts.

Resource links resolve from this Skill directory. Artifact paths resolve from the conversation workspace, never the Skill installation directory. Read one candidate's evidence and artifacts at a time; retain the selected candidate list in compact batch state.

## Entry and sequence

Classify `new_story` or `existing_ticket_update`. A ticket used only as evidence is not automatically an update target.

If a ticket to be read lacks an explicitly confirmed source, the next interaction is `ask_user_question`: one independent selector per unresolved ticket, options `WPB | ALM | DATA | FCR | GO`. Wait before Jira access. Reuse explicit per-ticket confirmation within this request; never infer another ticket's source.

For each candidate:
1. Inventory evidence, resolve material gaps and create planning trace. Updates preserve original ticket baseline and complete attachment intake.
2. Determine identity, exact paths, final SPEC filename and attachment action before writing Story. Naming a future file is allowed; creating Test Case/SPEC before the first passing Story score is not.
3. Write the complete Story with exactly `## Acceptance Criteria` and stable AC IDs containing Given/When/Then. Invoke `nodejs-base-mcp.score_requirement_markdown` with its workspace-relative path; omit optional force_review for documented cache behavior.
4. Only a complete current score with boolean `ok: true`, numeric `finalScore >= 76` and no unresolved parser/AC-recognition blocker opens Test Case generation. A 36 score with ok=true is not PASS.
5. Generate Test Case, validate titles and executable coverage, then assemble SPEC from current sibling content. Updates may require multiple distinctly reviewed replacement SPECs; preserve each explicit attachment mapping.
6. Run mechanical checks and independent read-only `ceaia-spec-review` against actual evidence. A fresh read-only subagent or task-provided independent reviewer satisfies this; do not duplicate it or substitute generator self-approval. If no independent review capability is available, return WAITING_TOOL.
7. Persist every review in the next unused numbered `.ceaia-work` record, point state to the latest review, classify repairs by affected file, and apply the shared budget. Story changes require a new numbered score record; unchanged Story does not.
8. Synchronize every intended SPEC review header after the verdict, immediately read back and verify the header against the latest PASS, then check global source coverage across the selected batch. A failed header sync blocks jiraReady.
9. When all selected targets have current PASS, continue to `jira-createissue-helper`'s dedicated SDLC gateway for Jira-wiki rendering, final preview/preflight and direct Jira export, unless assigned a preparation-only stage.

## Invocation boundary

Default is bundled generation, review and validated Jira handoff. If the caller explicitly assigns only generation and separate later review/push steps, return `nextAction: independent-review` after generation and prechecks. Do not also execute those later steps. No task JSON redesign is required.

Resume from validated current state on every entry; do not unconditionally repeat scoring, review or writes. Generation owns content repair even when a later stage reports the problem.

## Output and waiting

Return current artifact paths, candidate statuses, blockers and one concrete next action. Findings use complete workspace-relative paths and known lines. Source paths, scores and workflow state stay out of Jira-facing content.

Use `ask_user_question` only for missing business facts, unresolved Jira selections or supported recovery choices. If that tool is unavailable, explain once and return the exact question with a waiting state; do not loop. The validated SDLC gateway has no separate approval-tool step: after its final preview, payload/read-back checks and attachment preflight pass, it calls the Jira export tool directly.

Preparation completion is distinct from whole-workflow success. Whole-workflow success requires all intended writes confirmed, or explicit user stop. Waiting is a resumable pause.
