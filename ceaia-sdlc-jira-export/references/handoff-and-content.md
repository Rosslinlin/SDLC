# Handoff and current-content gate

The generation workflow owns business evidence, intake, planning, content repair, review and scoring. This skill validates that handoff; it does not repeat those workflows. Resolve artifacts from the current conversation workspace and persistent profiles from the real user workspace. They are different scopes.

## Selected items and identity

Accept one create, multiple creates, one update or multiple updates. An explicitly requested mixed create/update batch remains supported under the write reference. Preserve selected candidates, their order, source and individual contents. A blocked selected item cannot silently disappear from an integrated batch; ask for a scope decision or return the affected blocker.

Each item carries these actual facts or pointers to readable current state:

| Data | Required evidence |
| --- | --- |
| Routing | Internal `handoffType: ceaia-sdlc-validated`, candidate ID and `create`/`update`; remove this discriminator before every Jira call. |
| Destination | Supported confirmed source (`WPB`, `ALM`, `DATA`, `FCR`, `GO`; tool `almType` is lowercase), project and project's Story type from intake/current confirmed context or the applicable user profile. Never infer source/project from a Ticket prefix. |
| Content | Current `STORY.md`, `TEST_CASE.md` and primary `<name>-spec.md` paths; every additional distinct SPEC in `specArtifacts`. Read saved content, not a generator's prose summary. A supplied content copy must match the saved reviewed Story after line-ending normalization only. |
| Review | Latest independent report path/attempt, clean content PASS, observed Story/Test Case/SPEC body revisions, `testCaseTitleValidation: manual-pass`, all intended SPECs reviewed, no unresolved material findings. |
| SPEC sync | Every intended SPEC's first two Review Result fields match that PASS/attempt, body unchanged after header synchronization and complete read-back recorded. |
| Final score | Current numbered score record and its complete raw response; actual inputs bind this `STORY.md`, exact `specFileStatus: Pass`, primary SPEC path/file revision and current review attempt. |
| Readiness | Current parent `jiraReady: true`, global source coverage PASS with zero pending obligations; `updateReady: true` for an update. The earlier review report's SCORE=DEFERRED and jiraReady=false are expected; use the later parent state. |
| Update | Exact Ticket Key, source-confirmation evidence, update manifest, preserved original Description/AC and retrieval statuses, complete attachment inventory and selected original-ID-to-output mappings. Carry the actual target project/type and known current Epic/Parent/metadata values with their retrieval or explicit-confirmation evidence; mark unavailable facts explicitly. |

Destination values marked unresolved at intake may be resolved at export; this does not require regenerating the Story. Match an update profile against the target's actual project and Story type from upstream evidence or explicit user confirmation of the target's facts. A project-scoped cached Story type alone proves neither the type nor the project of this ticket and never authorizes changing them. If target type or project is unknown, obtain the missing supported upstream fact or a focused user clarification, not a guessed key or a generic-type fallback. Unavailable unchanged relationships/metadata may remain untouched; do not demand unsupported remote fields just to prove they were omitted.

## Reuse upstream ticket reads

For an update, use the baseline Description and separate AC result already retrieved during generation. The original attachment inventory may be empty; it must be a completed inventory. Keep imported source attachments unchanged. Source evidence records the original attachment ID, name, scope and selected handling.

Do not re-fetch Jira Description, AC or attachment inventory merely because export is a new skill/stage/session. If the handoff's source evidence is inaccessible, mismatched to this target, incomplete, or known to have changed externally, return the missing/affected evidence to intake or perform an explicitly necessary read-only reconciliation. Reconcile known external changes before overwriting; rerun affected preservation/content gates when the approved business baseline changes. Lack of an automatic remote re-read does not establish remote atomicity.

Use baseline/current values for update preservation. Record current Epic/Parent and metadata values only when actually returned or explicitly confirmed; absent means unavailable, not empty. Do not fabricate unsupported `getJiraInfos` fields. An unknown unchanged field stays untouched; if a proposed change needs its value, resolve that fact first. A saved last-used Ticket Number is historical information, never the next update target.

## Passing score and currentness

The raw score response must have boolean `ok === true`, a finite numeric `finalScore` from 76 through 100, no nonempty `parseErrors` or unresolved missing-AC recognition warning, and available AC counts consistent with the declared criteria. Missing optional counts alone do not fail. Never derive or coerce a total score from labels/subscores.

The scoring input is this item's final Workspace Story for creates and updates, not a Jira-mode score or score-record path. A cache hit is valid when the actual invocation saved this current SPEC status/path. Do not call the scorer or set `force_review` during export; stale score/review bindings return upstream.

Use the generation-owned observed revisions/read-back sequence. A counter or PASS label alone is insufficient evidence of unchanged content. Reuse the just-verified complete reads when no write intervened; do not recheck the same gate solely because another reference restates it. An external/local edit or interrupted write invalidates affected readiness and preview bindings. Read-back is not an immutable or cryptographic lock.

## Files and output preservation

- One create has exactly one reviewed Story, Test Case and SPEC. An update has one Story/Test Case set, its primary SPEC and any distinct mapped additional SPECs. A local SPEC is still required for a no-upload update's final score binding.
- Upload only selected reviewed SPECs. Test Case, planning, baseline, score/review records, state, manifests and user profiles are never Jira attachments or substitutes for Jira-visible business text.
- Every generated/uploaded basename matches case-sensitive `^[a-z0-9]+(?:-[a-z0-9]+)*-spec\.md$`, names the reviewed business function and agrees across the actual file, state, review, sibling references and upload mapping. Examples: `registration-entry-spec.md` passes; `SPEC.md`, `Registration-spec.md`, `entry.md` do not.
- Distinct replacement contents keep distinct files and mappings. An explicitly reviewed many-to-one mapping may be preserved; never merge them for brevity. Existing retained/deleted legacy attachments keep their original names and IDs.
- A path/name/mapping defect returns upstream; do not rename at export, use an upload-only alias or reuse PASS for a changed path.
- Keep original business wording, IDs, section order, tables and approved links. Exclude internal source registers, workspace paths, scoring/review diagnostics and unresolved promises from Jira-visible text. Preserve specifically approved architecture/general-specification commit links where the upstream contract allows them.

After these gates pass, continue through profile resolution, field rendering and exact export preflight. Missing export tools yields WAITING_TOOL with prepared artifacts, never fictional Jira success.
