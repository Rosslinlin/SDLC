# User Story Reviewer Output Format

Read this file when preparing the final user-facing response after an assessment or score-synchronization task. This is a presentation contract only; scoring, persistence and Jira calls remain governed by the other references.

## Mandatory requirements

The final response contains the actual User Story title followed by only these three sections, in order: **1. Evaluation**, **2. Recommendations**, **3. Clarifying questions**. For a batch, repeat this structure for each selected Story. Do not add a general Jira export summary or extra sections such as Rule hits, Retained AC assessments, Evidence gaps, Diagnostics or Validation.

- Return user-readable Markdown, not structured JSON unless the user explicitly requests JSON.
- Use UK English unless the user requests another language.
- Do not expose internal rule codes, diagnostic field names, ledger values, validator output, raw JSON, validation details, AC counts or internal processing notes. Keep the complete evidence in the saved result record.
- Replace every template placeholder with actual content. Never return a bracketed placeholder, invented Jira key, invented Story title or fabricated score.
- Keep Notes concise, evidence-based and understandable to a business reader. State a material score or Jira-synchronization limitation in plain language within Evaluation when it affects the result; do not add a fourth section.

## Required final response template

```markdown
# User Story — [actual Jira key when known] — [actual Story title]

## 1. Evaluation

| Section | Score | Max | Notes |
| --- | ---: | ---: | --- |
| Description format | [returned score] | [rubric max] | [brief supported note] |
| Acceptance criteria format | [returned score] | [rubric max] | [brief supported note] |
| Description understandability | [returned score] | [rubric max] | [brief supported note] |
| Acceptance criteria specificity | [returned score] | [rubric max] | [brief supported note] |
| Acceptance criteria testability and coverage | [returned score] | [rubric max] | [brief supported note] |
| Scope | [returned score] | [rubric max] | [brief supported note] |
| Overall | [actual finalScore] | [overall rubric max] | [quality band and concise overall note] |

## 2. Recommendations

- [Evidence-based action, or None identified.]

## 3. Clarifying questions

1. [Direct unresolved business question, or None identified.]
```

Omit the Jira key when no actual key exists. If a title is unavailable, identify the selected target using only a verified available identifier; never create a title to fill the template. Do not leave template labels or sample content in the delivered response.

## Rendering instructions

- For a completed numeric assessment, render Evaluation as exactly one Markdown table with `Section`, `Score`, `Max` and `Notes`, using the six section rows and Overall in the order above. Read each Score from the validated actual service response and Max from the supplied rubric under [calls and results](scoring-and-results.md#user-facing-score-presentation). The template supplies names and layout, not new scoring weights. Do not calculate, infer, alter, cap or invent a returned score; use `—` and a plain note for a missing section score as specified there.
- Use the returned `finalScore` for Overall and the actual quality band only when available. If the scoring response is failed, skipped or unknown, explain the actual state in Evaluation without a fabricated numeric table or Overall score. A sync-only response uses the retained real assessment and states the observed sync outcome within Evaluation.
- Write recommendations as concise actions based on material quality gaps. Write clarifying questions only for unresolved business scope, behaviour or testability decisions. Put `None identified.` in a section with no applicable items; do not omit the section.
- Keep score completion, score-update submission and confirmed Jira-field completion distinct. Include a result or deliverable link inside an existing section only when needed for the requested handoff or explicitly requested by the user; never use a link in place of the required score presentation.
- Before responding, check that the only headings are the Story title and the three numbered sections, in the required order, and that no placeholders or internal diagnostics remain.
