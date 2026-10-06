---
name: ceaia-spec-review
description: Independently review current CEAIA Story, Test Case and SPEC artifacts against original evidence, current tool scoring, traceability and existing-ticket preservation; return structured findings for targeted repair and Jira readiness.
---

# CEAIA Independent Review

Bundle revision: 4.0.0 text-only — 2026-09-21.

Review actual files read-only. Do not edit artifacts/state, recalculate scores, invoke the score tool, prepare Jira payloads, run final export preflight or write Jira. Parent generation owns repair, state persistence and user interaction.

## Required resources

Read:
1. [shared workflow contract](../ceaia-sdlc-story-spec-generation/references/workflow-contract.md).
2. [review contract](references/review-contract.md).
3. [core gates](references/core-review-gates.md).
4. [checklist](references/review-checklist.md).
5. [report template](references/review-report-template.md).
6. For updates only, [update review](references/existing-jira-update-review.md).
7. Active [planning](../ceaia-sdlc-story-spec-generation/templates/planning-template.md), [Story](../ceaia-sdlc-story-spec-generation/templates/story-template.md), [Test Case](../ceaia-sdlc-story-spec-generation/templates/test-case-template.md), [SPEC](../ceaia-sdlc-story-spec-generation/templates/spec-template.md) templates when validating structure; [scoring tool contract](../ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) for score evidence and parser integrity.

Skill links are relative to this Skill. Artifact paths are workspace-relative and come from current state. No hard-coded skill_read_resource tool or workspace root name is assumed.

## Execution

1. Identify candidate, mode, source/ticket, repair phase and current state.
2. Read original sources and planning, then original Jira/attachment evidence for updates.
3. Read current Story, complete score response, Test Case, SPEC and manifest where applicable.
4. Verify every gate, including SCORE. A generator summary, prior PASS or SPEC header is not evidence.
5. Return one complete report with gate findings, content bindings and one next action. Parent saves it at the next unused numbered reviewPath and points state to that record; it never overwrites a completed prior review.

Score verification applies only to the stored response: complete response, correct candidate/current Story binding, documented provenance, true boolean ok, numeric finalScore>=76 and no unresolved AC/parser recognition issue. Check canonical Acceptance Criteria heading and available top-level/legacy AC counts. Do not ask for another assessment of unchanged Story. Missing service checksum is not automatic failure when valid invocation provenance and workflow binding exist; do not invent a service checksum.

Review failure does not automatically mean rewriting Story. Classify exact affected files and whether Story must change. The shared contract controls all repair counts and waiting states; this reviewer does not run its own loop.

On substantive PASS return the evidence needed for candidate readiness, but keep jiraReady=false until the parent synchronizes and re-reads every intended SPEC header. Header synchronization alone needs no new score or substantive review. After verified synchronization, parent may set jiraReady=true (and updateReady=true for a valid update, including attachmentAction=none). All selected candidates and global source coverage must pass before the parent enters `jira-createissue-helper`'s SDLC gateway.

Normal verdicts: PASS, NEEDS_REVISION, NEEDS_HUMAN_CLARIFICATION; FINAL_WITH_UNRESOLVED only after explicit stop. Technical blockers use NEEDS_REVISION with technical ownership and nextAction=technical-recovery, not invented business questions. Return WAITING actions to parent rather than spinning.
