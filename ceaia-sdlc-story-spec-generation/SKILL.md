---
name: ceaia-sdlc-story-spec-generation
description: Generate final Jira-ready Story JSON and Markdown SPECs using shared CEAIA quality criteria. Resolve Jira fields before authoring, independently review the JSON and SPECs, score the saved JSON with scoreUserStory, and hand the unchanged package to the path-only exporter.
---

# CEAIA Story JSON and SPEC Generation

Version 6.1.0-preview.1. Text-only. Platform runtimes: GPT-5.6 Luna or GPT-6 Luna.

## Entry and resources

1. Read [workflow contract](references/workflow-contract.md) for the fixed sequence, state and resume rules. Default is complete SDLC including Jira unless explicitly preparation-only/no-Jira or stopped. An assigned stage executes only that stage.
2. Read [shared quality standard](references/quality-contract.md) before planning or writing. Reviewer uses the identical packaged copy. Do not create a local numeric evaluator.
3. Read only the current step's resources:

| Step | Resource | Result |
| --- | --- | --- |
| Sources | [source enrichment](references/source-enrichment.md); [existing update](references/existing-jira-update.md) for updates | Readable original evidence, selected targets and exact gaps |
| Jira field assembly, before scoring | [JSON assembly and profiles](references/jira-json-assembly.md) | Actual project/Story type/field schema, resolved values and complete Workspace JSON |
| Authoring | [production steps](references/artifact-generation-rules.md), [plan](templates/planning-template.md), [JSON](templates/story-json-template.json), [Story projection](templates/story-projection-template.md), [tests](templates/test-case-template.md), [SPEC](templates/spec-template.md) | Canonical story.json4jira, executable TEST_CASE.md and all named SPECs |
| Metadata/examples | [worked examples](references/metadata-and-examples.md), when needed | Supplied facts retained; unknown optional values omitted |
| Independent review | Invoke ceaia-spec-review with original evidence and actual JSON/tests/SPECs | Saved content report and smallest supported repair |
| Scoring | [scoreUserStory contract](references/scoring-tool-contract.md), after verified review headers | Actual three-argument score invocation and complete response |
| Missing fact | [clarification/resume](references/user-clarification-and-resume.md), only if blocked | Specific question bound to the affected item |
| Final preview/export | Hand current state to ceaia-sdlc-jira-export | User-confirmed unchanged JSON path; backend creation/linkage/queue results |

## Execution

- Classify new_story or existing_ticket_update. A cited evidence ticket is not an update target. Confirm each Jira source before reading it; do not infer from prefixes. UAT-only work uses its dedicated workflow.
- Move project/type/metadata queries and final field assembly into this generation stage. Do not leave missing required fields or format conversion for export. Reuse verified profile components and actual upstream baseline reads.
- Process candidates serially: source plan → resolve Jira fields → complete JSON → tests/SPEC → precheck → independent review → synchronize SPEC Result/Notes → score. Keep blocked selected candidates pending while preparing unrelated candidates.
- The saved JSON is the authoritative Story and eventual upload input. SPEC contains its faithful Markdown business projection. No standalone STORY.md or independently authored upload payload is required. TEST_CASE.md remains a Workspace deliverable.
- Use existing platform read/write, agent, question, query and scoring tools only; no local scripts. Read actual files, record observed revisions and preserve complete tool responses.
- Scoring uses nodejs-base-mcp.scoreUserStory with story_json_workspace_path, specFileStatus and specFilePath. Export uses java-base-mcp.exportJiraByDynamicFields with only the identical story_json_workspace_path. Never substitute a result/report path.
- Story scores stay in the independent score record/UI, never in SPEC. Freeze JSON and scored SPEC contents after assessment. There is no display-driven S2 or separate five-field binding call in this new route; the creation backend owns linkage and queueing, whose actual results must still be checked.

Return actual file paths, candidate status, blockers and one next action. Missing facts/tools cause a resumable wait. An actual score, current content confirmation and backend persistence evidence are separate requirements, not inferred from a PASS label.
