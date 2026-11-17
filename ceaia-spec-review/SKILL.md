---
name: ceaia-spec-review
description: Independently review every current CEAIA Markdown SPEC against original evidence and Q1-Q7, using Story and Test Case content only as alignment references; return SPEC findings and update-preservation conclusions without scoring or writing artifacts.
---

# CEAIA Independent SPEC Review

Bundle revision: 6.1.0-preview.7 text-only. Supported platform runtimes: GPT-5.6 Luna and GPT-6 Luna. The packaged Q1-Q7 quality standard is unchanged.

Review independently from the generator, using actual original sources and current artifacts. Use platform read and agent capabilities; run no scripts or local code. Return the report to the caller. Do not edit artifacts/state, discover Jira fields, invoke scoring or export, prepare replacement payloads or write Jira. The parent owns schema/field preparation, repair, persistence, user interaction and workflow progression.

## Resources

Read the local [shared quality contract](references/quality-contract.md), [review contract](references/review-contract.md) and [report template](references/review-report-template.md). All required review instructions are inside this Skill package; no sibling package or local script is required. Generation ships the same version of the quality contract and reads it before authoring.

## Execution

1. Identify the candidate, new/update mode, original sources and complete current Markdown SPEC list. Use the parent's actual recorded Story JSON, Test Case and SPEC paths on resume, including legacy test-case basenames; naming alone is not a review defect. Updates also need the saved baseline, approved delta, readJiraAttachments inventory and current SPEC add/replace or explicit none plan. Independently read each selected original SPEC for preservation; evidence attachments are not automatically SPEC deletion targets. The parent owns JSON schema and attachment-operation validation.
2. Read original sources first, then every current SPEC. Complete current Story JSON, the two `.jirawiki` files selected by its root `descPath` and `acPath`, and the Test Case may be reused or read only as needed to check a SPEC's projection and test alignment. Do not issue an independent Story, JSON, Wiki-file or Test Case quality verdict. Complete tool-returned contents with actual path/currentness evidence can satisfy a read; missing, abbreviated, changed or uncertain reference content must be read when it is needed. A generator summary, prior PASS, SPEC header or self-check is not content evidence.
3. Apply Q1-Q7 to the SPECs once. Distinguish a supported SPEC content defect, a material missing business fact, a technical read blocker and a non-blocking suggestion. Verify each proposed finding against the relevant full context; discard cosmetic or low-confidence complaints.
4. Return one SPEC-only report with current reference bindings, all SPEC results, evidence for every criterion and one next action. The parent saves exactly this returned report under its internal historyRoot in the current **session Workspace**, never user Workspace, only for this actual invocation. Do not write another report or decide repair budgets, create additional review loops or calculate export readiness. Do not treat internal logs/baselines/previews as user deliverables or attach them to Jira.

For a repaired candidate or a front-end edit to a SPEC or its Story/Test Case references, read the affected SPEC and reference content needed to verify its current meaning. Story business changes, including either Wiki file, require synchronized local SPECs and affected tests whether or not the user says "update SPEC"; none governs remote attachments only. Retain prior SPEC conclusions only where current source/content bindings are established. A review of one SPEC cannot cover an unread additional SPEC. Standalone `STORY.md` and raw-JSON embedding in SPEC are not required.

Verdicts remain PASS, NEEDS_REVISION, NEEDS_HUMAN_CLARIFICATION and FINAL_WITH_UNRESOLVED. The last requires an explicit user stop. PASS reports that the SPECs meet the shared quality standard; it does not certify the Story JSON or Wiki files. The parent and scoring tool handle Story validation, score and Jira operation.
