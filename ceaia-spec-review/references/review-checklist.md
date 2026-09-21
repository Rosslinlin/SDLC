# Review completion checklist

Use core-review-gates.md as the authority for definitions. Every row must have current evidence in the report, not a copied tick.

- [ ] SCORE: complete genuine persisted response, correct current Story/response binding, boolean ok=true and numeric finalScore>=76; no AC recognition/parser blocker; optional service checksum interpreted correctly.
- [ ] STRUCTURE: mode-specific planning and current files present; templates, names, attachment action and metadata valid.
- [ ] COVERAGE: original evidence checked, candidate SR trace complete, supported decomposition/preservation, honest coverage layers, global omissions surfaced.
- [ ] STORY: exactly ## Acceptance Criteria with inline BDD, supported actor/value/scope, stable AC IDs, no invented rules or internal leakage.
- [ ] TESTS: every AC/FR executable, accurate types/priorities, one negative disposition per AC, no unresolved To Confirm, no material duplicates.
- [ ] TESTS: manual semantic title review plus standalone-or scan completed; real alternatives split without invented combinations.
- [ ] SPEC: every primary/additional file reviewed; exact current Story/table embedding, supplied metadata and approved design references retained, supported flow/FR/SC, no unresolved placeholders/promises.
- [ ] Compatibility fields: sourceCoverage, coverageCapability, sizingSplitRecommendation, attempt/cumulative counters and parent-controlled automatic handoff field are populated consistently.
- [ ] UPDATE: baseline, AC status, attachment inventory and preservation/mapping valid; N/A only for new Story.
- [ ] Every finding identifies full path, evidence, owner, impact and smallest supported repair.
- [ ] storyChangeRequired reflects actual cause, not blanket restart.
- [ ] Repair eligibility accounts for phase, consumed rounds, stagnation and user evidence; no second edit within the same round.
- [ ] Reviewed content revisions and actual read-back findings and SCORE gate appear in structured conclusion.
- [ ] Verdict/header synchronization and final-file read-back are assigned to parent.
- [ ] No Jira readiness on non-PASS, no export/approval claims from review.
