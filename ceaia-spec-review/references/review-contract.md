# Review input, ownership and verdict contract

Shared workflow-contract.md owns current-only paths, read-back/revision checks, invalidation and repair budget. Do not duplicate or override that policy here.

## Inputs

Parent supplies statePath and actual:
- original requirement/design/FSD/API material, user clarifications and source locations;
- mode-specific planningPath (mandatory);
- storyPath, scoreRecordPath, testCasePath and specPath;
- updateManifestPath, original Jira baseline, separate AC result and every applicable imported attachment for updates;
- repair round/phase and latest unresolved issues for focused verification.

Missing evidence is reported precisely, not replaced by prior generator assertions. New Story review uses its assigned SR rows plus cross-candidate context when needed; global coverage is additionally verified across the batch, not by demanding every Story implement every SR.

## Verdict

PASS: all required gates pass and no material blocker remains.
NEEDS_REVISION: supported content/mechanical correction or technical execution problem.
NEEDS_HUMAN_CLARIFICATION: actual missing/conflicting business evidence/decision.
FINAL_WITH_UNRESOLVED: explicit user stop before PASS only.
FAIL is a gate result, never the overall reviewResult.

Classify each finding by owner: generation, technical or user; affected artifact; evidence; business impact; concrete fix; storyChangeRequired; invalidated gates. A SPEC-only defect must not prescribe Story rewrite/rescoring. Harmless preference changes are not blockers.

Parent persists only latest report and updates compact current state. Reviewer never writes even in a standalone review invocation; return the report to the caller.

## Repair eligibility

Initial review does not consume a repair. Parent applies the shared candidate budget. During post-repair-verification aiSelfRepairAllowed=false; a non-PASS result identifies the next action, not permission for a second batch in the same round. To start another round parent checks remaining budget/stagnation and updates state.

Missing business evidence sets userActionRequired immediately. Exhausted budget/stagnation also requires a decision unless the one bounded read-only diagnosis remains useful. Technical recovery needs technical information, not invented business requirements. UserActionRequired includes business input, correction, additional bounded authorization, defer or stop decisions; it is not limited to new requirements.

Do not recommend more identical retries, lower the score threshold, discard ACs or accept a conditional PASS.

## Handoff

Return reviewResult, mode, identity, artifact paths, gateResults, scoreGate, reviewedBindings, findings, repair scope, title validation, jiraReady/updateReady, attachmentAction/mapping, userActionRequired/userRequestedEvidence and recommendedNextAction as specified by report template.

Score facts stay in this internal report/state, not SPEC notes or update manifest. Parent synchronizes verdict header and reads back the final attachment and records its fileRevision. Candidate PASS does not claim Jira approval, export, or whole-batch completion.
