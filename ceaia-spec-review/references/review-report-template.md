# CEAIA SDLC Review Report: [FEATURE / STORY]

## Template Contents

- Artifact register and review summary
- Existing Jira update context
- Gate results and source coverage audit
- Sizing decision and validated issues
- Required structured conclusion

**Review Date:** YYYY-MM-DD

**Artifact Set:** [Story folder or title]

**Attempt:** [Positive integer; continue 4, 5, 6, ... as required]

**Review Phase:** [initial-review / post-repair-verification]

**AI Self-Repair Count:** [0-3; lifetime count for this artifact set]

**AI Self-Repair Allowed:** [true only for attempts 1-3 while count is below 3 / false]

**Overall Status:** [PASS / NEEDS_REVISION / NEEDS_HUMAN_CLARIFICATION / FINAL_WITH_UNRESOLVED]

## Artifact Register

| Artifact | Path | Review status | Jira export use |
|---|---|---|---|
| Planning | `.ceaia-work/planning.md` | [PASS / Limited / Not available] | Internal only |
| Story | [workspace-relative STORY.md] | [PASS / FAIL] | Jira description |
| Test Case | [workspace-relative TEST_CASE.md] | [PASS / FAIL] | Workspace deliverable; not exported to Jira |
| SPEC | [workspace-relative story-named SPEC] | [PASS / FAIL] | Jira attachment |
| Update Manifest | `.ceaia-work/updates/<TICKET-KEY>/update-manifest.md` or N/A | [PASS / N/A] | Internal only |
| Imported Jira Attachments | [attachment IDs and workspace-relative paths or N/A] | [Reviewed / N/A] | Source evidence |

## Review Summary

**Reviewer Notes:** [Concise evidence-backed conclusion.]

**Jira Ready:** [true / false]

**Coverage Capability:** [E2E confirmed / Frontend behavior only / Mixed - state confirmed layers]

**Review Status Synchronization Required:** [true / false]

**Test Case Title Validation:** [manual-pass / failed]

**Recommended Next Action:** [Automatic self-fix and rerun / Ask focused clarification / Synchronize PASS and hand off when export capability is available]

**User Action Required:** [true / false]

**User Requested Evidence:** [Exact missing evidence or None]

## Existing Jira Update Context

Use this section only for an update to an existing Jira ticket. Use N/A for new Story creation.

**Jira Source:** [WPB / ALM / DATA / FCR / GO / N/A]

**Ticket Key:** [TICKET-123 / N/A]

**Attachment Read Status:** [Completed / Completed - no attachments / Blocked / N/A]

**Acceptance Criteria Field Status:** [retrieved / empty-or-unavailable / blocked / N/A]

**Incremental Requirement Summary:** [Concise summary / N/A]

**Summary Update:** [Unchanged / Explicit replacement summary / N/A]

**Description Update:** [Replace with reviewed STORY.md / N/A]

**Preservation Risk:** [None / Concise risk and disposition]

| Attachment ID | Original filename | Imported path | Proposed action | Replacement filename / path | User selection confirmed |
|---|---|---|---|---|---|
| [ID] | [filename] | [workspace-relative path] | [Evidence only / Replace / N/A] | [filename and path / N/A] | [Yes / No / N/A] |

## Gate Results

| Gate | Result | Evidence and conclusion |
|---|---|---|
| Artifact structure | [PASS / FAIL] | [Template/file-name/table-header result] |
| Source coverage and decomposition | [PASS / FAIL / LIMITED] | [SR coverage, split decision, coverage capability] |
| Story and AC quality | [PASS / FAIL] | [Actor/value/AC/BDD/traceability result] |
| Test design and executability | [PASS / FAIL] | [Positive/negative/priority/steps/deduplication result] |
| SPEC alignment and Jira readiness | [PASS / FAIL] | [Embedding/diagram/format/alignment result] |
| Existing Jira update integrity | [PASS / FAIL / N/A] | [Baseline preservation, delta coverage, attachment mapping, and supported-field result] |

## Source Coverage Audit

| SR ID | Requirement summary | Story / AC / FR / Test Case coverage | Status |
|---|---|---|---|
| SR-001 | [Requirement] | [ST-001 / AC-001 / FR-001 / TC-001] | [Covered / Gap / Clarification needed] |

## Sizing and Split Decision

**Decision:** [NO SPLIT / SPLIT RECOMMENDED / NOT ASSESSABLE]

**Rationale:** [Evidence-based explanation.]

| Proposed Story when split is required | Business value | Scope / SR IDs | Dependency |
|---|---|---|---|
| [Title] | [Value] | [Scope] | [Dependency] |

## Validated Issues

| Issue ID | Severity | Category | Complete workspace-relative path and line | Evidence | Material impact | Recommended fix | Disposition |
|---|---|---|---|---|---|---|---|
| ISS-001 | [Critical / Major / Minor] | [Category] | `[outputs/ceaia/<story-slug>/STORY.md:42 or complete path plus heading/ID]` | [Evidence] | [Impact] | [Fix] | [Self-fixable / Needs human clarification] |

Use `None` when no validated issues exist.

Never use bare location to `STORY.md`, `TEST_CASE.md`, a SPEC filename, or line N. For a range, use `<complete-workspace-relative-path>:<start>-<end>`.

## Required Structured Conclusion

```text
reviewResult: [PASS / NEEDS_REVISION / NEEDS_HUMAN_CLARIFICATION / FINAL_WITH_UNRESOLVED]
reviewerNotes: [summary]
reviewAttempt: [positive integer]
reviewPhase: [initial-review / post-repair-verification]
aiSelfRepairCount: [0-3]
aiSelfRepairAllowed: [true/false]
issues: [issue IDs or None]
validatedIssues: [issue IDs or None]
selfFixableIssues: [issue IDs or None]
platformOwnedIssues: [issue IDs or None]
needsHumanClarification: [questions or None]
userActionRequired: [true only when new user evidence is required]
userRequestedEvidence: [exact information/files required or None]
sourceCoverage: [covered/total and limiting evidence]
coverageCapability: [confirmed layers and gaps]
sizingSplitRecommendation: [none or proposal]
storyPath: [path]
testCasePath: [path]
specPath: [path]
specFileName: [file name]
jiraReady: [true/false]
updateReady: [true for a reviewed existing-ticket update with resolved mapping; otherwise false]
ticketKey: [ticket key or N/A]
source: [WPB / ALM / DATA / FCR / GO / N/A]
attachmentReplacementMapping: [one-to-one mapping or N/A]
changeSummary: [baseline-to-proposed summary or N/A]
preservationRisks: [None or risks]
automaticPostReviewJiraHandoffRequired: [true only when this set has PASS, every Jira-targeted set has PASS, and export capability is available; otherwise false]
reviewStatusSynchronizationRequired: [true after every verdict; false only when no SPEC exists]
testCaseTitleValidation: [manual-pass / failed]
recommendedNextAction: [action]
```
