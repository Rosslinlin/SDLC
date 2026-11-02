---
name: ceaia-spec-review
description: Independently review CEAIA Story, Test Case and every SPEC against original evidence and the shared Q1-Q7 content standard; return high-signal findings and update-preservation conclusions without scoring or writing artifacts.
---

# CEAIA Independent Content Review

Bundle revision: 6.0.0 text-only. Supported platform runtimes: GPT-5.6 Luna and GPT-6 Luna.

Review independently from the generator, using actual original sources and current artifacts. Use platform read and agent capabilities; run no scripts or local code. Return the report to the caller. Do not edit artifacts/state, invoke scoring, prepare Jira payloads or write Jira. The parent owns repair, persistence, user interaction and workflow progression.

## Resources

Read the local [shared quality contract](references/quality-contract.md), [review contract](references/review-contract.md) and [report template](references/review-report-template.md). All required review instructions are inside this Skill package; no sibling package or local script is required. Generation ships the same version of the quality contract and reads it before authoring.

## Execution

1. Identify the candidate, new/update mode, original sources, current Story/Test Case and the complete current SPEC list. For updates also obtain the saved baseline, approved delta, attachment inventory and resolved none/replace mapping from the parent.
2. Read original sources first, then all current artifacts. Check source-to-output omissions and output-to-source unsupported promises. A generator summary, prior PASS, SPEC header or self-check is not content evidence.
3. Apply Q1-Q7 once. Distinguish a supported content defect, a material missing business fact, a technical read blocker and a non-blocking suggestion. Verify each proposed finding against the relevant full context; discard cosmetic or low-confidence complaints.
4. Return one report with current bindings, all SPEC results, evidence for every criterion and one next action. The caller saves it. Do not decide repair budgets, create additional review loops or calculate export readiness.

For a repaired candidate, read changed artifacts and their dependencies; verify affected criteria and cross-artifact consistency. Retain prior conclusions only where current source/content bindings are still established. A review of one SPEC cannot cover an unread additional SPEC.

Content verdicts remain PASS, NEEDS_REVISION, NEEDS_HUMAN_CLARIFICATION and FINAL_WITH_UNRESOLVED. The last requires an explicit user stop. Content PASS reports that the shared quality standard is met; the parent separately handles the external score and Jira operation.
