---
name: ceaia-spec-review
description: Independently review the authoritative CEAIA story.json4jira business content, Test Case and every Markdown SPEC against original evidence and Q1-Q7; return high-signal findings and update-preservation conclusions without scoring or writing artifacts.
---

# CEAIA Independent Content Review

Bundle revision: 6.1.0-preview.1 text-only. Supported platform runtimes: GPT-5.6 Luna and GPT-6 Luna.

Review independently from the generator, using actual original sources and current artifacts. Use platform read and agent capabilities; run no scripts or local code. Return the report to the caller. Do not edit artifacts/state, discover Jira fields, invoke scoring or export, prepare replacement payloads or write Jira. The parent owns schema/field preparation, repair, persistence, user interaction and workflow progression.

## Resources

Read the local [shared quality contract](references/quality-contract.md), [review contract](references/review-contract.md) and [report template](references/review-report-template.md). All required review instructions are inside this Skill package; no sibling package or local script is required. Generation ships the same version of the quality contract and reads it before authoring.

## Execution

1. Identify the candidate, new/update mode, original sources, final Workspace `story.json4jira`, `TEST_CASE.md` and the complete current Markdown SPEC list. Obtain the parent's confirmed schema/field meanings; updates also need the saved baseline, approved delta, attachment inventory and resolved none/replace mapping.
2. Read original sources first, then the current JSON and all Markdown artifacts. Treat the JSON business content as authoritative; compare the SPEC's readable business projection and tests with it. Check source-to-output omissions and output-to-source unsupported promises. A generator summary, prior PASS, SPEC header or self-check is not content evidence.
3. Apply Q1-Q7 once. Distinguish a supported content defect, a material missing business fact, a technical read blocker and a non-blocking suggestion. Verify each proposed finding against the relevant full context; discard cosmetic or low-confidence complaints.
4. Return one report with current bindings, all SPEC results, evidence for every criterion and one next action. The caller saves it. Do not decide repair budgets, create additional review loops or calculate export readiness.

For a repaired candidate or a front-end user edit to JSON/SPEC/tests, read changed content and its dependencies; verify affected criteria and cross-artifact consistency. Retain prior conclusions only where current source/content bindings are established. A review of one SPEC cannot cover an unread additional SPEC. Standalone `STORY.md` and raw-JSON embedding in SPEC are not required.

Content verdicts remain PASS, NEEDS_REVISION, NEEDS_HUMAN_CLARIFICATION and FINAL_WITH_UNRESOLVED. The last requires an explicit user stop. Content PASS reports that the shared quality standard is met; the parent separately handles the external score and Jira operation.
