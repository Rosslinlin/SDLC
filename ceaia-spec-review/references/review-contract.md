# Decision contract

Use exactly one overall result:

- `PASS`: all hard gates pass, no unresolved blocker remains, and artifacts are Jira-ready.
- `NEEDS_REVISION`: issue is material and self-fixable from existing evidence.
- `NEEDS_HUMAN_CLARIFICATION`: required evidence is missing, conflicting, or impossible to infer.
- `FINAL_WITH_UNRESOLVED`: only when user explicitly stops before clean pass.

`FAIL` is a per-gate or issue status, not a normal terminal workflow outcome.

Parent maintains two counters per artifact set:

- `reviewAttempt`: starts at 1 and has no maximum.
- `aiSelfRepairCount`: starts at 0, has a lifetime maximum of 3, and never resets after user input.

Review attempts 1, 2, and 3 are AI-self-repair eligible. Each may contain at most one consolidated parent repair followed by a fresh independent post-repair verification stored for same attempt. If third post-repair verification is not PASS, parent asks user before starting attempt 4.

Review attempt 4 and every later attempt are user-driven. Parent does not AI-self-repair review findings. It invokes `ask_user_question`, provides exact unresolved findings and requested correction, information, file, or decision, waits for response, regenerates affected artifacts, and starts next attempt. If human evidence is required during attempts 1 through 3, ask immediately rather than waiting for third attempt. A passing set remains current unless changed.

## Required review inputs

Read:

- original requirement, UX/UI, FSD, API, and source materials available in workspace or conversation;
- `planning.md` when present;
- `STORY.md`;
- `TEST_CASE.md`;
- exactly one matching story-named SPEC;
- active generation templates at:
  - `../ceaia-sdlc-story-spec-generation/templates/planning-template.md`;
  - `../ceaia-sdlc-story-spec-generation/templates/story-template.md`;
  - `../ceaia-sdlc-story-spec-generation/templates/test-case-template.md`;
  - `../ceaia-sdlc-story-spec-generation/templates/spec-template.md`;
- `references/review-checklist.md` and `references/review-report-template.md`.

For an existing Jira update, additionally read:

- original Jira description and separate Acceptance Criteria retrieval result for target ticket;
- user-provided incremental requirements;
- `.ceaia-work/updates/<TICKET-KEY>/update-manifest.md`;
- every imported readable Jira attachment that is used as source evidence;
- selected imported attachment(s) and their attachment IDs for replacement;
- proposed updated `STORY.md`, `TEST_CASE.md`, and CEAIA SPEC.

Review actual file content. Do not pass based on generator summary or prior review result.

When separate Acceptance Criteria result is empty, null, unavailable, not found, or field-level error but description is non-empty, treat description as authoritative baseline and inspect it for embedded acceptance statements. This state is valid when recorded as `empty-or-unavailable`; do not fail solely because separate field has no value.

## Required output

Return one per-story review report using `references/review-report-template.md`. When reviewer is task's read-only independent subagent, do not write workspace files; parent workflow persists returned report to `.ceaia-work/stories/<story-slug>/review-attempt-<nn>.md` or `.ceaia-work/updates/<TICKET-KEY>/review-attempt-<nn>.md` and updates `.ceaia-work/review-summary.md`. In writable standalone review context, write those same paths directly.

Return structured conclusions with:

```text
reviewResult:
reviewerNotes:
reviewAttempt:
reviewPhase:
aiSelfRepairCount:
aiSelfRepairAllowed:
issues:
validatedIssues:
selfFixableIssues:
platformOwnedIssues:
needsHumanClarification:
userActionRequired:
userRequestedEvidence:
sourceCoverage:
coverageCapability:
sizingSplitRecommendation:
storyPath:
testCasePath:
specPath:
specFileName:
jiraReady:
updateReady:
ticketKey:
source:
attachmentReplacementMapping:
changeSummary:
preservationRisks:
automaticPostReviewJiraHandoffRequired:
reviewStatusSynchronizationRequired:
testCaseTitleValidation:
recommendedNextAction:
```

Every `issues` entry and human-readable finding must use complete workspace-relative artifact path. When line is known, format location as `<workspace-relative-path>:<line>` or `<workspace-relative-path>:<start>-<end>`. Bare filename, section-only location, or unqualified line number is invalid. Path must agree with `storyPath`, `testCasePath`, or `specPath`.

Use ownership rules:

- `platformOwnedIssues`: workspace-path leakage, stale review status, template mismatch, file-name or embedding synchronization, and other deterministic generation hygiene defects.
- `selfFixableIssues`: all findings parent can repair without new user evidence, including every platform-owned issue.
- `userRequestedEvidence`: during attempts 1 through 3, only missing or conflicting evidence that cannot be derived; from attempt 4 onward, exact user correction, decision, information, or file needed for every non-PASS finding.
- `userActionRequired: true` whenever workflow must pause for user input, including every non-PASS result from attempt 4 onward.

Set:

- `reviewPhase`: `initial-review` or `post-repair-verification`;
- `aiSelfRepairAllowed: true` only when `reviewAttempt <= 3` and `aiSelfRepairCount < 3`;
- `aiSelfRepairCount`: lifetime number of AI repair batches already applied to artifact set.

Deterministic platform hygiene must run before review and does not consume three AI self-repair slots. Do not present preventable workspace-path or stale-header defect as missing business information.

For clean PASS:

```text
reviewResult: PASS
jiraReady: true
updateReady: true for an existing-ticket update with a resolved attachment mapping; otherwise false
automaticPostReviewJiraHandoffRequired: true only when the parent confirms every Jira-targeted artifact set has PASS and ceaia-sdlc-only-jira-push-content is available; otherwise false
reviewStatusSynchronizationRequired: true
testCaseTitleValidation: manual-pass
recommendedNextAction: synchronize this SPEC review result and mark this artifact set Jira-ready. The parent invokes ceaia-sdlc-only-jira-push-content only after every Jira-targeted artifact set has PASS
```

Reviewer must not ask whether to continue, export, or push to Jira.

Reviewer returns exact user-requested evidence to parent only when human evidence is genuinely required. Parent owns all user interaction and must use `ask_user_question`; any non-PASS attempt is recoverable and attempt count alone never ends workflow.
