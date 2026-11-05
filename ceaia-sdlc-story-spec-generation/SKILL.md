---
name: ceaia-sdlc-story-spec-generation
description: Generate final Jira-ready Story JSON and Markdown SPECs using shared CEAIA quality criteria. Resolve Jira fields before authoring, independently review the JSON and SPECs, score the saved JSON with scoreUserStory, and hand the unchanged package to the path-only exporter.
---

# CEAIA Story JSON and SPEC Generation

Version 6.1.0-preview.7. Text-only. Platform runtimes: GPT-5.6 Luna or GPT-6 Luna.

## Entry and resources

1. Read [workflow contract](references/workflow-contract.md) for the fixed sequence, state and resume rules. Default is Jira-backed complete SDLC. Do not ask users to choose preparation-only versus Jira-backed at entry. Read existing configuration first and ask only for missing inputs under [first-entry assembly](references/jira-json-assembly.md). Only the user's explicit preparation-only/no-Jira or stop request changes intent; an assigned stage executes only that stage.
2. Read [shared quality standard](references/quality-contract.md) before planning or writing. Reviewer uses the identical packaged copy. Do not create a local numeric evaluator.
3. Read only the current step's resources:

| Step | Resource | Result |
| --- | --- | --- |
| Sources | [source enrichment](references/source-enrichment.md); [existing update](references/existing-jira-update.md) for updates | Readable original evidence, selected targets and exact gaps |
| Jira field assembly, before scoring | [JSON assembly and profiles](references/jira-json-assembly.md); [Jira text encoding](references/jira-text-format.md) | Actual project/Story type/field schema, resolved values and complete Workspace JSON with Jira wiki rich text |
| Authoring | [production steps](references/artifact-generation-rules.md), [plan](templates/planning-template.md), [create JSON](templates/story-json-template.json) or [update JSON](templates/story-update-json-template.json), [Story projection](templates/story-projection-template.md), [tests](templates/test-case-template.md), [SPEC](templates/spec-template.md) | Canonical story.json4jira, executable TEST_CASE.md and all named SPECs |
| Metadata/examples | [worked examples](references/metadata-and-examples.md), when needed | Supplied facts retained; unknown optional values omitted |
| Independent review | Invoke ceaia-spec-review with original evidence and actual JSON/tests/SPECs | Saved content report and smallest supported repair |
| Scoring | [scoreUserStory contract](references/scoring-tool-contract.md), after verified review headers | Actual three-argument score invocation and complete response |
| Missing fact | [clarification/resume](references/user-clarification-and-resume.md), only if blocked | Specific question bound to the affected item |
| Final preview/export | Hand current state to ceaia-sdlc-jira-export | User-confirmed unchanged JSON path; backend creation/linkage/queue results |

## Execution

- Classify new_story or existing_ticket_update. A cited evidence ticket is not an update target. Confirm each Jira source before reading it; do not infer from prefixes. UAT-only work uses its dedicated workflow.
- Both modes first read real user-workspace profiles.json. Reuse same-user/source/project/type components, including stable facts from a create profile during update; do not require a new update entry to reuse them. Existing baseline reads remain necessary. Query only missing/inapplicable components, explicit refresh/changed destination or actual schema-error recovery; never query create metadata as an edit-permission check.
- Move project/type/metadata queries and final field assembly into this generation stage. Root acceptanceCriteria is the actual AC field-ID string; its Jira wiki text lives at fields[acceptanceCriteria]. Omit customFields, root staffId and fields.reporter. Creates omit issueIdOrKey entirely; updates put the exact confirmed existing target key in root issueIdOrKey after issueType and preserve the approved baseline/delta. Description and AC already contain Jira wiki (h2. headings, * *AC-001*: entries). Resolve required metadata before scoring; reuse verified profiles and actual upstream baseline reads. No older flat/Markdown template, description/AC arrays or independent BDD objects.
- Process candidates serially: source plan → resolve Jira fields → complete JSON → tests/SPEC → precheck → independent review → synchronize SPEC Result/Notes → score. Keep blocked selected candidates pending while preparing unrelated candidates.
- The saved JSON is the authoritative Story and eventual upload input. SPEC contains its faithful Markdown business projection. No standalone STORY.md or independently authored upload payload is required. TEST_CASE.md remains a Workspace deliverable.
- A Story business change automatically synchronizes every affected local SPEC, projection and test before review/score, even when the user says only change Story/AC. For existing Jira delivery, [update rules](references/existing-jira-update.md) default related SPECs to replacement and a missing SPEC to first upload. Only an explicit remote-preservation request selects none; local synchronization still occurs. Show the actual plan in final confirmation, preserve unrelated originals and report unsupported replacement precisely instead of skipping SPEC.
- Use existing platform read/write, agent, question, query and scoring tools only; no local scripts. Read actual files, record observed revisions and preserve complete tool responses.
- Scoring uses nodejs-base-mcp.scoreUserStory with story_json_workspace_path, specFileStatus and specFilePath. Export uses java-base-mcp.exportjirabydynamicfield with only the identical story_json_workspace_path. Never substitute a result/report path.
- Story scores stay in the independent score record/UI, never in SPEC. Freeze JSON and scored SPEC contents after assessment. There is no display-driven S2 or separate five-field binding call in this new route; the creation backend owns linkage and queueing, whose actual results must still be checked.

Return only current deliverable links with useful labels, candidate score/status, actual Jira result or precise blocker and one next action. Keep internal preparation/state/attempt/preview links out of the default deliverable list; provide them only for requested history/diagnosis. Resolve and verify process-file writes under historyRoot, never outputRoot. Missing facts/tools cause a resumable wait. An actual score, current content confirmation and backend persistence evidence are separate requirements, not inferred from a PASS label.
