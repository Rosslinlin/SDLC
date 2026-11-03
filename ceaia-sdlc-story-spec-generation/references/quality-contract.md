# CEAIA shared content standard — Q1–Q7, version 6.1.0-preview.5

Generation reads this before planning/writing; independent review uses the same rules. This is the authoritative source in the repository. `ceaia-spec-review/references/quality-contract.md` is an identical packaged copy so either independently installed Skill can read it locally. Update both together. Task instructions and templates do not introduce additional content gates.

## Fixed criteria

| ID | Check | Pass condition | Material blocker |
| --- | --- | --- | --- |
| Q1 | Source fidelity and completeness | Every explicit in-scope requirement is represented in its assigned canonical JSON/AC/tests, or has an evidenced user-approved disposition. Business claims have source support. | A source obligation is omitted, contradicted, hidden as an exclusion or replaced by an invented rule. |
| Q2 | Value, clarity and scope | Actor, trigger, action, outcome and scope can be identified. A new Story has a cohesive testable value outcome; material dependencies are clear. | A missing fact or conflicting interpretations prevent understanding or acceptance; unrelated goals prevent coherent delivery. |
| Q3 | Verifiable acceptance | Each AC has stable identity, determinable conditions/actions and observable results. Given/When/Then expresses the behavior without adding facts. | Acceptance cannot distinguish success from failure, criteria conflict or required behavior is missing. |
| Q4 | Relevant scenario coverage | Source-required success, rejection, boundary, permission and dependency behavior is covered where relevant. | An evidenced material alternative or failure rule is missing, or its required outcome is unresolved. |
| Q5 | Executable tests | Each AC and declared FR has corresponding tests with supported preconditions/data, actions, observation and expected results. Tests assert the required behavior. | A test cannot be executed or cannot verify the AC; it changes the expected behavior or invents an endpoint, log, table or business rule. |
| Q6 | SPEC consistency | Canonical JSON business fields, their SPEC projection, tests, FR/SC and any flow diagram describe the same supported scope. References resolve; every intended SPEC is current and preserves its relevant context. | Contradictory/stale content, missing requirement coverage, unresolved references or an invented flow. |
| Q7 | Existing-ticket preservation | For updates, baseline obligations remain unless explicitly changed; delta and attachment mappings stay within approved scope. New Stories use N/A. | Wrong target, unauthorized changes/deletions or lost baseline/attachment obligations. |

## Evidence and missing facts

1. Use supplied requirements, relevant Jira baseline/attachments, approved designs and explicit user decisions. A user's explicit approved change overrides only the corresponding old requirement. Conflicting sources require a focused clarification; do not choose by length or apparent recency alone.
2. Screenshots prove the behavior they show. They do not prove backend storage, delivery, audit persistence or end-to-end integration. Templates, examples and scoring diagnostics are not business evidence.
3. Missing actor, core outcome or a material rule that changes acceptance blocks only the affected candidate. Ask for that exact fact. Omit unknown optional Author/Branch/external IDs. Do not demand unrelated endpoints, SLAs, security, notification or audit behavior merely to fill categories.
4. An irrelevant category can be omitted or marked N/A briefly. No universal requirement to add every positive/negative/boundary type. An AC defining denial can be tested by a Permission/Negative case without an invented successful-access companion.

## Scope and splitting

Assess INVEST through value cohesion, independent verification, actual workflows and dependencies. AC count can prompt evaluation but never forces a split. Keep related error/validation behavior with the outcome it qualifies. Do not invent team capacity or estimates. Existing broad ticket scope alone does not authorize new tickets or removal of obligations.

## Findings and verdicts

- **Content blocker:** Existing evidence establishes an error, omission, contradiction or untestable requirement. Give its location, evidence, concrete impact and smallest supported repair.
- **Business clarification:** A missing/conflicting fact changes scope or acceptance. Ask only the fact/decision needed.
- **Advice:** Current content is sufficient; an improvement is optional. Advice does not block PASS.
- **Technical / mechanical issue:** File access, parser structure, path/name, table layout or tool schema belongs to the owning production/integration step. Report it separately; do not label it a business defect. If it also causes a real ambiguity, missing reference or content mismatch, explain that impact under Q1–Q7.

Report high-confidence, material findings only. Do not fail sufficient content because more detail is possible, a common technical term appears, wording differs, a title contains `or`, or a negative-coverage label appears on a different row. Check actual test conditions and outcomes. Reject unsupported candidate findings; verify disputed material findings independently when needed instead of turning uncertainty into a new gate.

## Generation and independent verification

Generation assembles final Story JSON before scoring and derives tests and the SPEC business projection from its supported ACs, then self-checks Q1–Q7. Review independently reads original sources and current files, checking source → artifact omissions and artifact → source unsupported additions. Prior PASS, generated summaries and self-check marks do not substitute for that read.

This standard does not calculate the external score or guarantee 76 points. Real scoring, JSON/schema/transport requirements, repair limits, SPEC metadata persistence and Jira/database calls are owned by their respective procedures. Neither role may weaken source obligations to satisfy a score.

The current Story artifact is story.json4jira; there is no independent STORY.md input. Review fields.summary, the fields.description Jira wiki string, and actual AC Jira wiki obtained by resolving the root acceptanceCriteria field-ID string to fields[that ID], plus approved business values under actual field IDs. Compare their business meaning with the Markdown SPEC projection, allowing equivalent presentation syntax. acceptanceCriteria is a mapping, not business text; root staffId/customFields and fields.reporter are omitted, and no separate bddScenarios is required. Check that content against original evidence. Missing mapping/field/type or incorrect rich-text encoding is a technical production issue, not an extra Q1–Q7 criterion. Pure routing/schema identifiers are not business prose. Scores stay outside JSON and SPEC; header synchronization completes before scoring.
