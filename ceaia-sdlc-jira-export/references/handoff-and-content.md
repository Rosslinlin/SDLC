# Current JSON handoff

Consume state contract 7 from Skill ceaia-sdlc-story-spec-generation, resource references/workflow-contract.md. Configuration/assembly belongs to its references/jira-json-assembly.md. Separately installed Skills need supported Skill-name/resource reading or the parent's full applicable text; do not assume sibling filesystem access, invent a reader or continue from a summary when the contract is unavailable.

## Check each selected candidate

Keep internal handoffType=ceaia-sdlc-validated, candidate identity and create/update mode out of tool arguments. Preserve all selected items and their order; a blocked item cannot silently disappear from a batch.

| Evidence | Required current association |
| --- | --- |
| JSON | paths.storyJsonPath is the real fixed Workspace-relative story.json4jira file. artifactRevisions.storyJsonRevision and actual saved content match the passing review/score. This is valid JSON with the dedicated .json4jira extension, not a Markdown Story or a score-record path. |
| Review | review.result=PASS and review.recordPath/review.reviewAttempt identify the saved independent report. report.artifactPaths.storyJsonPath and reviewedBindings.storyJsonRevision match this JSON; Test Case/evidence bindings and reviewedSpecArtifacts cover all current SPEC bodies with actual reads and no material blocker. |
| SPECs | specArtifacts and paths.specPath identify every current SPEC and the primary score input. review.specHeaderSync/read-back records show Result/Reviewer Notes were finalized before scoring without changing reviewed bodies. The score's specFilePath/revision still matches the primary SPEC and verified PASS maps to exact specFileStatus=Pass. Numeric Story scores are not written into SPEC. |
| Score | paths.scoreRecordPath and score point to the complete actual scoreUserStory response and actual three inputs: story_json_workspace_path, specFileStatus and specFilePath. The JSON path/revision, SPEC path/revision and review attempt are current. Require the parent's passing numeric 76–100 and parser/validation gate under its scoring contract; do not invent missing response fields or rerun scoring here. |
| Coverage/readiness | jiraReady=true, plus updateReady=true for updates. Read supplied batchStatePath (canonical .ceaia-work/batch-state.json); selected identities/state pointers and globalCoverage.evidenceRevisionBindings match the current selection, coverage passes and no obligation is pending. A detached PASS label is insufficient. |
| Assembly | exportContext supplies the current complete assembly snapshot/record and profile pointer: authenticated identity scope, source/project/actual Story type, operation, confirmed allowed values, schema/mapping evidence, settings decision and approved upload/update plan. All match this JSON revision; settings confirmation is not frontend content confirmation. |
| Update | Exact confirmed target/source, saved Description/separate AC result, completed inventory and reviewed authorized delta/none-or-replace mapping. The live JSON tool contract supports the encoded target and requested operation. No filename, key prefix or create default substitutes for target evidence. |

Read actual files and records, not generator summaries or counters alone. Validate provenance/currentness without repeating decomposition, business quality review or scoring diagnostics. Do not edit, rename, render, normalize or reserialize the scored JSON/SPECs at export. An affected edit returns to generation and invalidates its review/score/readiness/preview bindings under the parent contract.

The confirmed file has real root staffId/routing and a fields object; AC content is the Markdown at fields[customFields.acceptanceCriteria], not the mapping ID or an old top-level array. The parent's assembly record establishes the actual type/field IDs and identity/target consistency. A legacy flat file, missing mapped value or mismatched structure returns upstream before submission; exporter never repairs it in place. File staffId is not an additional tool argument.

## Scope and file boundaries

- One candidate has one fixed story.json4jira, one Test Case and primary SPEC; distinct reviewed replacement contents retain distinct SPECs/mappings. specArtifacts.uploaded describes planned upload only; actual remote outcomes live in writeResults.
- The JSON attachments list contains only actual authorized uploads. Every listed file will be uploaded; do not insert a local score-only SPEC or workflow file to satisfy scoring. Test Case, planning, baselines, score/review/attempt records, profiles and state are not attachments.
- Every intended SPEC keeps its actual reviewed lowercase kebab-case <name>-spec.md basename and exact path. Preserve original attachment IDs/names for replacement targets. Paths, JSON entries and authorized mappings agree.
- Reuse saved update intake. Missing/inaccessible/mismatched intake or known external changes return upstream for supported reconciliation; no routine reread because a new Skill/session started. Unknown unchanged fields remain untouched.
- Bundled update intentions remain none/replace, conditional on live JSON-schema support. Missing target/replace encoding or unsupported independent score-only SPEC handling is WAITING_TOOL; never invent fields, append an upload, drop a replacement or switch update to create.

## Path and session identity

Copy the exact Workspace path from tools/state through scoring, frontend preview and export. Do not reconstruct it from a title, strip/add directories, rename the extension, substitute a score file or manufacture a conversation prefix. Reject absolute/URI/traversal paths; do not silently normalize them into another value. Runtime identity/session headers are platform-owned, never fabricated or added as exporter arguments. Preserve actual returned service identifiers only as observed.

Read-back and observed revisions are not a cryptographic lock. If a checksum is returned, preserve it exactly without claiming an unprovided hashing/normalization rule. Uncertain currentness or unavailable tools produce a precise WAITING_TOOL, not simulated success.
