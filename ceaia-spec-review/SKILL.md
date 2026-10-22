---
name: ceaia-spec-review
description: Independently review current CEAIA Story, Test Case and SPEC content against original evidence, traceability and existing-ticket preservation before final Story scoring; return structured findings for targeted repair.
---

# CEAIA Independent Review

Bundle revision: 5.1 text-only — 2026-10-23.

Review actual files read-only. Do not edit artifacts/state, recalculate scores, invoke the score tool, prepare Jira payloads, run final export preflight or write Jira. Parent generation owns repair, state persistence and user interaction.

## Required resources

Read:
1. [shared workflow contract](../ceaia-sdlc-story-spec-generation/references/workflow-contract.md).
2. [review contract](references/review-contract.md).
3. [core gates](references/core-review-gates.md).
4. [checklist](references/review-checklist.md).
5. [report template](references/review-report-template.md).
6. For updates only, [update review](references/existing-jira-update-review.md).
7. Active [planning](../ceaia-sdlc-story-spec-generation/templates/planning-template.md), [Story](../ceaia-sdlc-story-spec-generation/templates/story-template.md), [Test Case](../ceaia-sdlc-story-spec-generation/templates/test-case-template.md), [SPEC](../ceaia-sdlc-story-spec-generation/templates/spec-template.md) templates when validating structure. The scoring contract belongs to the parent's later scoring step.

Skill links are relative to this Skill. Artifact paths are workspace-relative and come from current state. No hard-coded skill_read_resource tool or workspace root name is assumed.

## Execution

1. Identify candidate, mode, source/ticket, repair phase and current state.
2. Read original sources and planning, then original Jira/attachment evidence for updates.
3. Read current Story, Test Case, SPEC and manifest where applicable. No score response is required for a clean first review.
4. Verify every substantive content gate. Mark SCORE `DEFERRED` in the structured report; a generator summary, prior PASS or SPEC header is not evidence for content quality.
5. Return one complete report with gate findings, content bindings and one next action. Parent saves it at the next unused numbered reviewPath and points state to that record; it never overwrites a completed prior review.

The reviewer checks the canonical Acceptance Criteria heading and declared IDs as part of STORY/STRUCTURE. It does not inspect a pending score, calculate one, or demand a scoring call before its content verdict. SCORE=`DEFERRED` is not SCORE PASS; the parent later validates the complete tool response, parser integrity and final SPEC metadata.

Review failure does not automatically mean rewriting Story. Classify exact affected files and whether Story must change. The shared contract controls all repair counts and waiting states; this reviewer does not run its own loop.

On substantive content PASS return its evidence, but keep jiraReady=false and updateReady=false. Parent synchronizes and re-reads every intended SPEC header, then scores the unchanged final Workspace Story with the primary SPEC `Review Result` / `Result` string and workspace-relative path. Header synchronization alone needs no substantive review. Parent may set jiraReady=true (and updateReady=true for a valid update, including attachmentAction=none) only after a current passing score with that SPEC metadata and global coverage PASS. A post-score content repair requires affected independent re-verification before a new final score call.

Normal verdicts: PASS, NEEDS_REVISION, NEEDS_HUMAN_CLARIFICATION; FINAL_WITH_UNRESOLVED only after explicit stop. Technical blockers use NEEDS_REVISION with technical ownership and nextAction=technical-recovery, not invented business questions. Return WAITING actions to parent rather than spinning.
