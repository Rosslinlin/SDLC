# v5.3 Functional Coverage

Active SDLC: generation → independent review → SPEC sync → final Workspace score → `ceaia-sdlc-jira-export`. Target runtime: GPT-6 Luna. The original `jira-createissue-helper/` remains byte-for-byte preserved for its existing generic and legacy routes.

| Capability | Owner / authoritative reference | Preserved behavior or approved change |
| --- | --- | --- |
| New Story / existing Story intake | generation | Per-ticket confirmed source, original Description and separate AC, complete attachment inventory; persist exportContext pointers and available target facts. |
| Planning / decomposition / coverage | generation | Evidence-backed INVEST, complete original-scope coverage; this change does not alter batch generation scheduling. |
| Story / Test Case / SPEC | generation | Current latest deliverables, distinct replacement SPECs, executable tests, exact embedding and source preservation unchanged. |
| Independent review / repair | review + generation | Read-only content PASS, one consolidated automatic repair round shared with score findings, affected re-review then wait if unresolved; manual title validation and SCORE=DEFERRED preserved. |
| SPEC review header / score | generation + scoring tool | Verified headers before final Workspace score; exact specFileStatus=Pass and primary SPEC path; ok=true, numeric 76–100, parser/AC integrity; complete numbered raw evidence. |
| Story-only export | exporter SKILL + handoff-and-content | Single/batch create and updates; no other issue type, no generic fallback, no re-generation at export. Existing Epic/Parent relationships remain supported. |
| Cross-session configuration | exporter workspace-profile | Persist compact all-required/AC/selected-optional field schema, IDs, selected-option formats and confirmed defaults in real user workspace; matching profile skips three queries, no TTL refresh. |
| Initial lookup / cache miss | exporter workspace-profile | Real project/type/metadata parameters; Story-only selection; ambiguous choices use popup; fresh lookup only for affected missing/changed facts. |
| Update baseline reuse | generation exportContext + exporter handoff | Use upstream original Description/AC/inventory, target identity and approved mappings. No routine repeat Jira/attachment read; reconcile only actual evidence gaps/known changes or exception outcomes. |
| Settings / Epic | exporter fields-and-rendering | Every export confirms destination, exact update key, Epic/Parent and actual field values. Every create resolves explicit Epic/no-Epic; new key verified once, saved confirmed key not reread. Defaults never expand update authorisation. |
| Description / AC partition | exporter fields-and-rendering | Preserve all non-AC sections in order, including post-AC sections; only AC body in writable mapped AC field, unchanged Workspace Story. Verified matching baseline AC may be left untouched on update. |
| Jira transport / empty values | exporter fields-and-rendering | Lossless Markdown→wiki including tables/code/links; exact IDs/options/types, no fabricated placeholders, no unintended update field clearing. |
| SPEC attachment operations | exporter attachments | Create exactly one add; update none/add/replace/delete only as reviewed, multi-SPEC mapping retained, actual kebab-name-spec.md, local exact-path preflight and once-serialized plan. Replace uploads before deleting old target. |
| Jira first write / batch | exporter jira-write-and-recovery | Direct arguments, no requestJson or handoffType, first call omits markdownReviewRelativePath; multiple creates use issuesJson, ordinary updates sequential with failure stop; explicit mixed-batch branch preserved. |
| Database / score record binding | exporter jira-write-and-recovery | After each confirmed issue+required attachment success, separately call same export tool with exactly staffId/almType/returned issueIdOrKey/conversationId/scored STORY.md markdownReviewRelativePath. Every successful batch item binds separately. |
| Preview / retained evidence | exporter jira-write-and-recovery | One current jira-preview.md, exact payload/local attachment read-back, numbered immutable attempts; B preview replaces current A preview while A audit is retained. No additional final-approval popup after settings confirmation. |
| Failure / recovery | exporter jira-write-and-recovery | Separate issue/attachment/binding states, numeric status=400 is failure even if isError=false, reconcile unknown outcomes, no automatic write replay, binding failure never recreates issue or uploads again. |
| Persistence after issue/attachments success | exporter workspace-profile | Save only confirmed successful-item configuration, preserve older profiles on failed/partial/unknown operations. Binding pending can coexist with a valid saved profile; persistence failure is reported separately. |
| Generic and Test Case exports | original jira-createissue-helper | Entire original directory unchanged; its routes, confirmation, special formats, optional generic score binding and legacy SDLC behavior remain available independently. |
| Standalone scoring | original user-story-reviewer | Jira-mode scoring and separately confirmed four-parameter score synchronization unchanged; never substituted for the SDLC five-field binding. |

## Active package

- `ceaia-sdlc-story-spec-generation/`
- `ceaia-spec-review/`
- `ceaia-sdlc-jira-export/`
- `SDLC-workflow-v5.task.json`

Preserved independent packages: `jira-createissue-helper/`, `user-story-reviewer/`. Upload the three active skills, replace the new export skill ID placeholder in the Task, and select GPT-6 Luna in the platform runtime. No local executable script is required by the delivered workflow.
