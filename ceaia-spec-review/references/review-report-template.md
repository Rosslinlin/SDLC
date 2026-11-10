# CEAIA content review: [actual candidate]

Return one JSON conclusion using the fields below. Replace examples with observed values; preserve the full current SPEC list. This is an incomplete illustration, not a verdict to copy. Keep criterion evidence concise and describe each defect once in findings. No duplicate prose report is required.

```json
{
  "identity": {
    "candidateId": "example-story",
    "mode": "new_story",
    "source": null,
    "ticketKey": null,
    "qualityContractVersion": "6.1.0-preview.6"
  },
  "reviewResult": "NEEDS_REVISION",
  "artifactPaths": {
    "storyJsonPath": "outputs/ceaia/example-story/story.json4jira",
    "testCasePath": "outputs/ceaia/example-story/test-case.md",
    "sourcesRead": []
  },
  "reviewedBindings": {
    "storyJsonRevision": null,
    "testCaseRevision": null,
    "evidenceRevision": null,
    "contentReadBack": false
  },
  "reviewedSpecArtifacts": [
    {
      "path": "outputs/ceaia/example-story/example-story-spec.md",
      "bodyRevision": null,
      "contentReadBack": false,
      "result": "NOT_ASSESSED",
      "preservation": {
        "result": "N/A",
        "originalAttachmentIds": [],
        "preservedObligations": [],
        "gaps": []
      }
    }
  ],
  "criteriaResults": {
    "Q1": {"result": "NOT_ASSESSED", "evidence": ""},
    "Q2": {"result": "NOT_ASSESSED", "evidence": ""},
    "Q3": {"result": "NOT_ASSESSED", "evidence": ""},
    "Q4": {"result": "NOT_ASSESSED", "evidence": ""},
    "Q5": {"result": "NOT_ASSESSED", "evidence": ""},
    "Q6": {"result": "NOT_ASSESSED", "evidence": ""},
    "Q7": {"result": "N/A", "evidence": "New Story; no inherited update baseline."}
  },
  "findings": [],
  "coverage": {
    "coveredRequirementRefs": [],
    "gaps": [],
    "approvedExclusions": [],
    "crossCandidateIssues": []
  },
  "sizingSplitRecommendation": {
    "decision": "NOT_ASSESSABLE",
    "rationale": "",
    "proposedStories": []
  },
  "preservationConclusion": {
    "result": "N/A",
    "attachmentAction": null,
    "authorizedChanges": [],
    "gaps": []
  },
  "clarifications": [],
  "nextAction": "complete-review"
}
```

Use real workspace-relative artifact paths; each sourcesRead entry has path and location identifying an original source and its precise section/item. No invented source IDs, attachment IDs, revision numbers or line numbers. contentReadBack is true only after the reviewer actually reads the corresponding current content; reviewedBindings.contentReadBack covers the authoritative current JSON and Test Case. A supplied counter alone cannot set it true. Bind JSON edits through storyJsonRevision, not a retired Markdown Story revision.

Each findings entry has id, criterion (Q1-Q7), classification (CONTENT_BLOCKER/CLARIFICATION/TECHNICAL_BLOCKER/ADVISORY), path, location (JSON field/item, Markdown section or known line), sourceEvidence, problem, impact, action, affectedPaths and storyJsonChangeRequired. Use actual evidence and the smallest affected scope. A criterion's evidence may link finding IDs rather than repeat their narrative. A JSON-field locator identifies the actual supplied field/element; do not fabricate schema properties to locate a gap.

Each clarifications entry has findingId and question. Name the actual item and precise missing fact/decision. Do not ask the user to repeat already clear requirements. Technical read failures stay in findings and lead to technical-recovery, not a business clarification question.

Spec result and preservation result use PASS/FAIL/NOT_ASSESSED; preservation may use N/A only for a new Story. For an update, every SPEC's preservation lists the original attachment IDs applicable to it, preserved obligations and any gaps; a first add or explicit none may have no selected originals but must still preserve applicable baseline obligations. All related local SPECs must reflect current Story business content even when remote attachments stay unchanged. preservationConclusion covers the complete target, not merely the primary SPEC. attachmentAction is add, replace or explicit none for an update, null for a new Story. These are internal review conclusions, not invented JSON transport fields or upload instructions.

For replacement originals, sourcesRead and existing criterion/preservation evidence identify the actual original content locations and readJiraAttachments ID/source+ticket bindings, matching each saved attachmentsJson.replace[].attachmentId and its relativePath/fileName. Record a wrong, unread or ambiguous original as the specific finding/gap; never select other evidence attachments merely because they were read or contain spec in a filename. Normal use of the user-tested add/replace object needs no new capability proof.

coverage uses actual source requirement references/locations, not invented counts. Record supported exclusions and cross-candidate gaps separately. Sizing decisions are NO_SPLIT, SPLIT_RECOMMENDED or NOT_ASSESSABLE, always with a reason; unknown capacity is not an estimate. Preserve inherited scope when proposing any update decision.

nextAction is one concrete instruction to the parent: continue-parent-workflow, repair-content, request-business-facts, technical-recovery, complete-review or stop-with-unresolved. PASS uses continue-parent-workflow; explicit stop uses stop-with-unresolved. The parent determines score eligibility, repair allowance, persistence and the Jira handoff.
