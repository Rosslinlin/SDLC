---
name: ceaia-sdlc-story-spec-generation
description: Generate or update evidence-backed CEAIA Stories, Test Cases and SPECs using the same content standard as the independent reviewer. Own local repairs and final Workspace scoring; start the bundled SDLC workflow or perform an explicitly assigned stage.
---

# CEAIA Story and SPEC Generation

Version 6.0.0. Text-only. Supported platform runtimes: GPT-5.6 Luna and GPT-6 Luna.

## Entry

1. Read [workflow contract](references/workflow-contract.md) for the fixed sequence and current state. Ordinary chat and the Task use this same sequence. Default is the complete SDLC workflow, including Jira; an explicit preparation-only/no-Jira request stops before export.
2. Read [shared quality standard](references/quality-contract.md) **before writing**. The independent reviewer uses the identical standard. Do not invent another rubric or a local numeric score.
3. Resume the saved phase. If the caller assigns intake-only, generation-only, review/repair or scoring, execute only that stage and return its next action. An internal stage boundary does not cancel the later Jira stage.

## Load only what this step needs

| Step | Read | Produce / next action |
| --- | --- | --- |
| Intake | [source enrichment](references/source-enrichment.md); [existing Jira update](references/existing-jira-update.md) for updates | Source locations, selected targets and precise missing facts |
| Plan and generate | [authoring procedure](references/artifact-generation-rules.md), [planning template](templates/planning-template.md), active [Story](templates/story-template.md), [Test Case](templates/test-case-template.md), [SPEC](templates/spec-template.md) templates | Current files and one mechanical precheck; next `independent-review` |
| Examples / metadata | [metadata and examples](references/metadata-and-examples.md), when needed | Supplied metadata retained; unknown optional values omitted |
| Review | Invoke `ceaia-spec-review` with readable original sources and all current artifacts | Parent saves the independent report; repair only supported defects |
| Score | [scoring tool contract](references/scoring-tool-contract.md), then [SPEC score display](references/spec-score-display.md) | Actual score and complete saved response; required final SPEC metadata call preserved |
| Clarify | [clarification and resume](references/user-clarification-and-resume.md), only for a blocker | A specific question bound to the affected item |
| Export | Hand ready state to `ceaia-sdlc-jira-export` | Jira A write followed by each ticket's separate five-field database binding B |

## Execution rules

- Classify `new_story` or `existing_ticket_update`. A cited evidence ticket is not an update target. UAT-only work uses its dedicated workflow.
- Before any Jira read, confirm `WPB`, `ALM`, `DATA`, `FCR` or `GO` for each target. Use `ask_user_question` for a missing source; never infer it from a key. Resolve Jira destination fields later, at export.
- Process one intake-ready candidate at a time: plan → Story → tests → SPEC → precheck → independent review → verified SPEC header → real score → score display / final metadata. Keep blocked candidates selected; continue independent preparation for other candidates.
- The generator checks the quality standard before handing off. Reviewer independently reads the sources; a generator checklist is not evidence for its verdict.
- Use only existing platform file, reviewer, scoring and Jira tools. Do not generate or execute scripts. Read actual saved files, reuse unchanged reads within a step and record observed revisions; do not invent hashes or service IDs.
- Preserve actual tool calls and their persistence effects. Content PASS never replaces scoring. A cached score never replaces its required metadata persistence. A successful Jira write never replaces database binding B.

## Return

Return actual saved artifact paths, candidate status, remaining blockers and one next action. Preparation is complete only after the relevant review/score checks; whole-workflow success requires every intended Jira/attachment/binding operation confirmed. Missing tools or facts produce a resumable waiting state, not a success claim.
