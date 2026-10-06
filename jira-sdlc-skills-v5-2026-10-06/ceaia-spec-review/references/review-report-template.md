# CEAIA Current Review: [Candidate]

Internal report only; parent saves this at the next unused numbered reviewPath and never overwrites an earlier completed report. Replace placeholders with observed values. Structured conclusion is valid JSON, not prose masquerading as booleans.

## Context
- Mode / candidate / source / ticket: [identity]
- Evidence revision / repair round / phase: [current values]
- Sources actually read: [precise paths/locations]
- Planning / state / Story / score / Test Case / SPEC / manifest paths: [complete workspace-relative paths]
- Original ticket baseline and attachment inventory: [update only]

## Gate Results
| Gate | Result | Current evidence / finding IDs |
|---|---|---|
| SCORE | [PASS/FAIL] | [response path, currentness, provenance, actual ok/finalScore] |
| STRUCTURE | [PASS/FAIL] | [files/template/names/action] |
| COVERAGE | [PASS/FAIL] | [SR coverage, original evidence and layers] |
| STORY | [PASS/FAIL] | [AC/BDD/support] |
| TESTS | [PASS/FAIL] | [coverage/executability/manual title validation] |
| SPEC | [PASS/FAIL] | [embedding/flow/FR/SC] |
| UPDATE | [PASS/FAIL/N/A] | [baseline/preservation/attachmentAction/mapping] |

## Findings
| ID | Severity | Gate / owner | Full path and line | Evidence / impact | Targeted repair | Story change required |
|---|---|---|---|---|---|---|
| [ID or None] | [severity] | [gate/owner] | [path:line] | [observed facts] | [specific action] | [true/false] |

## Coverage and Repair Decision
- Candidate SR→AC→FR→TC coverage: [mapped IDs and gaps]
- Cross-candidate omissions/contradictions for parent: [items or None]
- Inherited update scope limitation: [update only or None]
- Affected files/gates: [explicit set]
- User input or technical recovery needed: [exact information/decision or None]
- Budget/stagnation decision: [current state and next action]

## Sizing and Split Decision
- Decision: [NO SPLIT / SPLIT RECOMMENDED / NOT ASSESSABLE]
- INVEST and ownership rationale: [evidence-backed assessment]
- Proposed split, if needed: [titles, business value, SR allocation, dependencies]
- Update preservation risk and change summary: [actual values or N/A]

## SPEC and Attachment Register
| Current SPEC path | Original attachment ID / filename | Preserved obligations | Body binding | Review result | Intended upload |
|---|---|---|---|---|---|
| [each primary/additional SPEC] | [reader evidence or N/A] | [trace] | [observed bodyRevision and read-back finding] | [PASS/FAIL] | [yes/no] |

## Structured Conclusion
Required fields; use actual values and arrays/objects. This illustration is a non-PASS report, not a default to copy unchanged.
```json
{
  "contractVersion": 4,
  "candidateId": "example-story",
  "mode": "new_story",
  "source": null,
  "ticketKey": null,
  "reviewResult": "NEEDS_REVISION",
  "reviewerNotes": "Replace with evidence-backed conclusion.",
  "reviewPhase": "initial-review",
  "reviewAttempt": 1,
  "aiSelfRepairCount": 0,
  "repairRound": 0,
  "aiSelfRepairAllowed": true,
  "storyPath": "outputs/ceaia/example-story/STORY.md",
  "testCasePath": "outputs/ceaia/example-story/TEST_CASE.md",
  "specPath": "outputs/ceaia/example-story/example-story-spec.md",
  "specFileName": "example-story-spec.md",
  "gateResults": {
    "SCORE": "FAIL", "STRUCTURE": "FAIL", "COVERAGE": "FAIL",
    "STORY": "FAIL", "TESTS": "FAIL", "SPEC": "FAIL", "UPDATE": "N/A"
  },
  "scoreGate": {
    "scoreRecordPath": ".ceaia-work/stories/example-story/story-quality-score-attempt-001.md",
    "recordComplete": false, "recordCurrent": false,
    "provenanceVerified": false, "ok": null, "finalScore": null,
    "passed": false
  },
  "reviewedBindings": {
    "bindingMethod": "readback-and-revision",
    "storyRevision": null, "testCaseRevision": null, "specBodyRevision": null,
    "contentReadBack": false
  },
  "issues": [],
  "validatedIssues": [],
  "selfFixableIssues": [],
  "platformOwnedIssues": [],
  "needsHumanClarification": [],
  "sourceCoverage": {"covered": 0, "total": 0, "gaps": []},
  "coverageCapability": {"confirmedLayers": [], "gaps": []},
  "sizingSplitRecommendation": {"decision": "NOT ASSESSABLE", "rationale": "", "proposedStories": []},
  "changeSummary": null,
  "preservationRisks": [],
  "reviewedSpecArtifacts": [],
  "affectedArtifacts": [],
  "storyChangeRequired": false,
  "testCaseTitleValidation": "failed",
  "attachmentAction": "add",
  "attachmentReplacementMapping": [],
  "jiraReady": false,
  "updateReady": false,
  "userActionRequired": false,
  "userRequestedEvidence": [],
  "reviewStatusSynchronizationRequired": true,
  "specHeaderSync": {
    "status": "pending",
    "reviewAttempt": 1,
    "verifiedSpecPaths": [],
    "failedSpecPaths": []
  },
  "automaticPostReviewJiraHandoffRequired": false,
  "recommendedNextAction": "classify-and-repair"
}
```

Each issues entry includes id, severity, gate, owner, path, line (null if unknown), evidence, impact, action and storyChangeRequired. Never invent line numbers or optional audit IDs.

Compatibility fields are required, not optional prose replacements. validatedIssues/selfFixableIssues/platformOwnedIssues contain applicable issue IDs; needsHumanClarification contains concrete questions. sourceCoverage gives actual counts and gaps; coverageCapability states supported layers; sizingSplitRecommendation always has a reason, even for NO SPLIT.

reviewAttempt is the monotonic review count. aiSelfRepairCount is cumulative actual repair batches; it may exceed three only across legitimate evidence revisions or explicitly bounded extensions. It is not the current revision's allowance. repairRound remains the active budget counter.

reviewedSpecArtifacts lists every state.specArtifacts path with the observed bodyRevision, contentReadBack, result and preservation conclusion. Header synchronization happens later; parent records final fileRevision and the final export-preflight read-back. Revision numbers describe observed edits, not computed fingerprints.

automaticPostReviewJiraHandoffRequired is true only when the parent supplies verified all-target PASS, current global coverage and export availability. Otherwise false for this per-candidate review; parent sets the aggregate handoff flag after all reviews. False does not authorize an unnecessary "continue?" prompt.

PASS requires all gates PASS except UPDATE=N/A for new_story, scoreGate.passed=true with verified completeness/currentness/provenance, all reviewed revisions recorded with actual current-content checks, manual-pass titles and zero unresolved material issues. The reviewer returns substantive PASS with `reviewStatusSynchronizationRequired=true`; the parent sets jiraReady/updateReady only after every intended SPEC header is synchronized and verified. Post-repair-verification always returns aiSelfRepairAllowed=false for that same round. UserActionRequired covers missing evidence and required recovery/budget decisions.

Parent copies verified gate state, not merely a PASS string, into current state and synchronizes only the SPEC review header. jiraReady becomes true only after every intended SPEC header has been read back and verified against this numbered review record.
