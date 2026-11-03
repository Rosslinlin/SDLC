# Produce JSON, tests and SPEC from the same requirements

Read [Q1–Q7](quality-contract.md) before writing; [workflow](workflow-contract.md) owns sequence/repair and [assembly](jira-json-assembly.md) owns early Jira queries/field encoding.

## Plan and assemble

Use [planning template](../templates/planning-template.md): exact source → explicit obligation → candidate → AC/FR/TC. Assign batch-unique SR IDs and retain them through global coverage. Preserve the original source, not merely a generated summary. Include supplied reporting/integration/notification/audit/fraud/retention/MI/testing obligations; lack of implementation detail is not exclusion.

New Stories are cohesive independently testable value slices; AC count does not force splitting. Preserve inherited update scope. Resolve final names, source/project/type/required fields and attachment intent through assembly before final scoring. Simple inputs need only the compact coverage rows; expand UX/API/async/layer details only when actually supported.

## JSON → tests → Markdown SPEC

1. Save the complete canonical JSON using [JSON template](../templates/story-json-template.json), [assembly](jira-json-assembly.md) and [Jira text encoding](jira-text-format.md). Business content is fields.summary, the Jira wiki fields.description and AC string at fields[acceptanceCriteria], plus approved business values under actual field IDs. Root acceptanceCriteria is the actual AC field-ID string, not content; no customFields wrapper or fields.reporter is submitted. Unique AC-### entries contain observable Given/When/Then; preserve any supported BDD detail there without an independent bddScenarios object. Finish presentation encoding now, before review/score. No filler, invented backend rules or control/score diagnostics enter business fields.
2. Read the saved JSON and assign supported FR identifiers in planning. Derive [tests](../templates/test-case-template.md) from its actual ACs and source evidence. Every AC/FR has executable coverage with supported entry/data, ordered actions, observation and expected results. Keep 11 columns, supported scenario types and risk-based P0/P1/P2.
3. Consider meaningful negative behavior. Covered identifies a real rejection case; N/A has a reason; material missing behavior uses draft-only To Confirm and waits. No mandatory positive companion for a denial AC, no word-level or ban and no combinations invented from word counts. Distinct conditions/results must be independently verifiable; equivalent supported data may be parameterized explicitly.
4. Derive a readable Markdown [Story projection](../templates/story-projection-template.md) from the JSON's Jira wiki business fields and place it inside [SPEC](../templates/spec-template.md). Convert presentation syntax only; preserve wording, order, IDs, code bodies and link targets. Include every business section/AC/BDD and approved field meaning, without routing, staff identity, custom-field IDs or JSON transport controls. This is a faithful view, not a separately authored requirement source. Do not create STORY.md, paste raw wiki into SPEC or embed raw JSON bytes as the business Story.
5. Embed the saved executable test table exactly in every SPEC, declare the same FRs and supported measurable SCs, and retain relevant entities/flows/context. Use supported Mermaid when meaningful; a trivial flow may be a short description. Preserve supplied metadata under [examples](metadata-and-examples.md), never invented SLAs/endpoints/logs/author/IDs.
6. Different replacement contents retain distinct files and source-specific obligations; shared JSON/Story/test meaning must agree in every SPEC. A local score-only SPEC is not described as uploaded. Standalone Test Case metadata remains outside embedded tables and is not a Jira attachment.

## One production precheck

Read saved files and reuse those reads for this pass:
- JSON uses a real fields object: project.key agrees with root projectKey and issuetype.id matches the actual Story type. Omit fields.reporter and root staffId/customFields in both modes. fields.summary/description are strings. Resolve the nonempty root acceptanceCriteria field-ID string to actual metadata, then verify fields[that ID] exists as nonempty Jira wiki with unique AC IDs. Description/AC and applicable authored rich text already satisfy the encoding reference; decode once to verify actual newlines and lossless content. No old root summary/description/bddScenarios or AC text/array, mapping-as-content, description arrays, placeholders, double encoding or old direct-call wrapper. Check every submitted fields key against applicable writable metadata; do not copy unapproved baseline/profile fields. Updates preserve approved omission semantics rather than copying create defaults.
- Mode is fixed by the request: create omits issueIdOrKey entirely; update's nonempty root issueIdOrKey matches state.ticketKey, exportContext.target.ticketKey and the actual source/baseline. Use the corresponding template; never inherit a target from a profile/evidence-only ticket or remove it to bypass an update failure.
- The actual JSON path is identical in state, assembly evidence and later call inputs. All intended attachment paths exist, match approved scope and actual -spec.md names/mappings; excluded/score-only files are absent from uploads.
- SPEC business projection is lossless against JSON; its tests equal TEST_CASE.md's executable table. AC/FR/TC references resolve. Required metadata and 11 test columns are present; table pipes/newlines are valid.
- Self-check Q1–Q7 against original sources, recording a concise result in the plan. Unknown optional values are omitted; real gaps stay pending.

Correct bounded mechanical defects, then give the independent reviewer actual originals, JSON, tests, all SPECs, field interpretation evidence and observed revisions. Do not give self-check marks as review proof.

## Repair, synchronize and freeze

Fix only the affected layer: JSON business error → JSON + dependent tests/projection/SPEC; correct AC but wrong test → test/table; SPEC-only error → affected SPEC. A technical schema/transport issue stays in assembly; a business fact gap waits. Apply the workflow's two-content-batch/per-cause mechanical policy and independently re-review affected business content.

After content PASS, parent writes Result/Reviewer Notes in every SPEC, then reads complete files and verifies business bodies unchanged. First header mismatch allows one header-only retry, another waits. All header writes finish before [scoreUserStory](scoring-tool-contract.md). **No Story Score field is created or updated in SPEC.** Keep the returned score in the independent record/UI. Freeze JSON and scored SPECs through final preview/export; any later edit needs a current package assessment and fresh confirmation.
