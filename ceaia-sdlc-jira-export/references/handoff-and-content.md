# Current validated handoff

Generation owns business evidence, content repair, review, scoring and readiness. Consume its v6 state contract: Skill `ceaia-sdlc-story-spec-generation`, resource `references/workflow-contract.md`. The [repository link](../../ceaia-sdlc-story-spec-generation/references/workflow-contract.md) works only when sibling files are accessible. On separately installed platforms use supported Skill-name/resource reading, or have the parent supply the full applicable contract text. Never invent a reader or continue from a summary when the contract is inaccessible. Do not repeat upstream workflows. Conversation artifacts and persistent user profiles are separate scopes.

## Validate each selected item

Accept internal handoffType=ceaia-sdlc-validated, candidate identity and create/update. Keep the discriminator out of all Jira arguments. Preserve selected items, order and individual mappings; a blocked item cannot disappear from a batch without a scope decision.

| Evidence | Check current saved facts, not a generator summary |
| --- | --- |
| Artifacts | paths and specArtifacts identify the actual Story, Test Case and every current SPEC. Saved content and observed revisions match the reviewed/scored candidate. A supplied content copy may differ only in line endings. |
| Review | review.result=PASS, review.recordPath and review.reviewAttempt identify the current independent report. Its reviewedBindings match current Story/Test Case/evidence revisions, and reviewedSpecArtifacts covers every current SPEC body with PASS and read-back evidence. No material finding remains. |
| SPEC finalization | review.specHeaderSync and actual read-back bind every SPEC Result/Reviewer Notes to that review. specScoreDisplay verifies the persisted total in every current SPEC and finalizes primary SPEC metadata. Header changes preserved reviewed bodies. |
| Score | score.status=pass points to the current numbered complete response and actual invocation inputs: this final Workspace Story, exact specFileStatus=Pass, primary SPEC path/revision and review attempt. Raw ok is boolean true, finalScore is a finite number in 76–100, and the parent's parser/AC gate is resolved. Validate saved evidence; do not rerun scoring or reinterpret quality diagnostics here. |
| Parent readiness | jiraReady=true and updateReady=true for updates. Read the supplied batchStatePath (canonical `.ceaia-work/batch-state.json`); its selectedCandidateIds/state pointers and globalCoverage evidenceRevisionBindings must match all current selected candidates. Require coverage PASS with zero pending obligations; a detached PASS label is insufficient. |
| Destination | Confirmed source, project and actual Story type from intake/current context or applicable profile. Sources are WPB/ALM/DATA/FCR/GO; tool almType is lowercase. Never infer source or project from a ticket prefix. |
| Update | Exact target key, confirmed source, reviewed update manifest, preserved Description/separate AC results, complete attachment inventory and original-ID-to-output mappings. Reuse exportContext pointers and available project/type/field/relationship facts; unavailable is not empty. |

The exporter checks provenance/currentness and authorized scope, not decomposition, test-writing style or business quality. Missing/stale evidence returns to its owner; never manufacture PASS from a label or counter.

## Update preservation and reads

Use generation's saved baseline and completed inventory, including a valid empty inventory. Normal export does not call getJiraInfos or readJiraAttachments again. An inaccessible, mismatched, incomplete or known externally changed baseline needs affected upstream reconciliation. If the approved business baseline changes, return for affected preservation/content review before overwriting.

An update profile does not prove this ticket's project/type. Resolve missing target facts through supported upstream evidence or focused user confirmation; never change them by using create defaults. Unavailable unchanged fields remain untouched. A last-used ticket is history, not the next target.

Current bundled updates support attachment none or replace. Exporter add/delete support is reserved for a separately supported upstream workflow with its own explicit reviewed authorization; it cannot extend the bundled update surface.

## Files and currentness

- A create has one Story/Test Case/primary SPEC. An update may have additional distinct mapped SPECs. Even a no-upload update retains its reviewed local SPEC for score metadata; specArtifacts.uploaded describes the intended upload, not confirmed remote success.
- Every generated/uploaded SPEC uses its actual reviewed lowercase kebab-case `<name>-spec.md` basename. Paths, filenames, references and manifest/upload mappings agree. Distinct contents keep distinct files; explicit reviewed many-to-one mappings remain per item. Retained/deleted legacy attachments keep original names/IDs.
- Upload only authorized reviewed SPECs. Test Case, plans, baselines, score/review records, state, manifests and profiles are never Jira attachments.
- Preserve business wording, IDs, section order, tables and approved links. Exclude internal registers, paths and diagnostics from Jira-visible text; preserve upstream-approved architecture/general-specification commit links.
- Reuse complete verified reads when no edit intervened. Local/external changes invalidate affected bindings; return upstream rather than renaming, repairing or substituting at export. Observed revisions/read-back are not an atomic or cryptographic lock.

If export tools are unavailable, retain prepared artifacts and report WAITING_TOOL; never claim Jira success.
