# CEAIA Current Review: [Candidate]

Internal report only; parent saves this at the next unused numbered reviewPath and never overwrites an earlier completed report. Replace placeholders with observed values. Structured conclusion is valid JSON, not prose masquerading as booleans. Keep PASS gate evidence concise and link it to actual source paths/IDs. Describe each real defect once in Findings and its structured issue entry; do not repeat the full defect narrative in every section. Preserve all required fields.

## Context
- Mode / candidate / source / ticket: [identity]
- Evidence revision / repair round / phase: [current values]
- Sources actually read: [precise paths/locations]
- Planning / state / Story / Test Case / SPEC / manifest paths: [complete workspace-relative paths; score record pending until after content PASS]
- Original ticket baseline and attachment inventory: [update only]

## Gate Results
| Gate | Result | Current evidence / finding IDs |
|---|---|---|
| SCORE | DEFERRED | Final Workspace Story score is submitted after content PASS and verified SPEC Result/path |
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
- Repair allowance decision: [active round, one-round default, any recorded authorized extras and next action]

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
  "contractVersion": 5,
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
    "SCORE": "DEFERRED", "STRUCTURE": "FAIL", "COVERAGE": "FAIL",
    "STORY": "FAIL", "TESTS": "FAIL", "SPEC": "FAIL", "UPDATE": "N/A"
  },
  "scoreGate": {
    "scoreRecordPath": null,
    "status": "DEFERRED",
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

reviewAttempt is the monotonic review count. aiSelfRepairCount is cumulative actual repair batches; it may exceed one only across legitimate evidence revisions or explicitly bounded extensions. It never grants extra edits in the current revision. repairRound is the active counter against the shared default limit of one plus recorded authorized extra rounds; preserve all counters on resume.

reviewedSpecArtifacts lists every state.specArtifacts path with the observed bodyRevision, contentReadBack, result and preservation conclusion. Header synchronization happens later; parent records final fileRevision and the final export-preflight read-back. Revision numbers describe observed edits, not computed fingerprints.

automaticPostReviewJiraHandoffRequired remains false for this per-candidate content review. The parent may set the aggregate handoff only after every candidate has content-review PASS, verified SPEC status/path, a current passing final Story score, global coverage and export availability. False does not authorize an unnecessary "continue?" prompt.

Content-review PASS requires all content gates PASS except UPDATE=N/A for new_story, SCORE=DEFERRED, all reviewed revisions recorded with actual current-content checks, manual-pass titles and zero unresolved material issues. `scoreGate.status=DEFERRED` and `scoreGate.passed=false` are accurate at this stage and do not negate content PASS. The reviewer returns substantive PASS with `reviewStatusSynchronizationRequired=true`; the parent synchronizes every intended SPEC header, then submits the final Story for scoring with the primary SPEC Result/path. Post-repair-verification always returns aiSelfRepairAllowed=false for that same round. UserActionRequired covers missing evidence and required recovery/budget decisions.

Parent copies verified content-gate state, not merely a PASS string, into current state and synchronizes only the SPEC review header. jiraReady becomes true only after every intended SPEC header has been read back, the final workspace Story score passes with current primary SPEC status/path, and global coverage passes.
For content PASS, set recommendedNextAction to synchronize-spec-and-score; for non-PASS, identify the smallest repair or clarification action. Never recommend immediate Jira export from the independent report.
