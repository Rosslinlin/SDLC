# User Story Reviewer Output Format

## Mandatory requirements

The final response MUST contain only the User Story title and the three sections `1. Evaluation`, `2. Recommendations`, and `3. Clarifying questions` in that exact order, and MUST NOT include any other sections or content, including rule hits, retained AC assessments, evidence gaps, diagnostics, acceptance-criteria counts, structured JSON, validation details, or internal processing notes.

- Return the final response as user-readable Markdown.
- Do not return JSON unless the user explicitly requests JSON.
- Do not provide a general Jira export summary.
- Do not include sections named `Rule hits`, `Retained AC assessments`, `Evidence gaps`, `Diagnostics`, `Validation`, or similar.
- Do not expose internal rule codes, diagnostic field names, ledger values or validator output.
- Replace every placeholder in the template with actual content before returning the response.
- Never return angle-bracket placeholders such as `<score>` or `<recommendation>`.
- Use English (UK spelling).
- Keep the notes concise, evidence-based and understandable to a business reader.

## Required final response template

# User Story – [actual JIRA ID] - [actual user story title]

## 1. Evaluation

| Section | Score | Max | Notes |
|---|---:|---:|---|
| Description format | [score] | [max] | [notes] |
| Acceptance criteria format | [score] | [max] | [notes] |
| Description understandability | [score] | [max] | [notes] |
| Acceptance criteria specificity | [score] | [max] | [notes] |
| Acceptance criteria testability and coverage | [score] | [max] | [notes] |
| Scope | [score] | [max] | [notes] |
| Overall | [overall score] | [overall max] | [quality band] – [overall notes] |

## 2. Recommendations

- [Evidence-based recommendation 1]
- [Evidence-based recommendation 2]

## 3. Clarifying questions

1. [Clarifying question 1]
2. [Clarifying question 2]

## Rendering instructions

- Render the Evaluation section as exactly one Markdown table with the columns `Section`, `Score`, `Max` and `Notes`.
- Include the six section rows and the Overall row shown in the required template.
- Populate every Score and Max value from the validated evaluation result.
- Do not calculate, infer, alter or hard-code score values during rendering.
- Preserve the section names and order defined in the required template.
- The section maximums are supplied by the validated evaluation result. The format file is only a presentation template.
- Render the Overall row using the calculated overall score, overall maximum, quality band and overall notes.
- Write recommendations as concise action statements based on material quality gaps.
- Write clarifying questions as direct questions that address unresolved business, scope, behavioural or testability issues.
- If there are no material recommendations or clarifying questions, write `None identified` under the relevant required section. Do not omit either section.

## Internal rendering workflow

1. Complete the evaluation using the authoritative Rubric.
2. Generate the structured evaluation result.
3. Validate the structured result against the output schema and runtime validator when available.
4. Render the validated result using the Required final response template.
5. Before returning the response, remove all content other than the User Story title, Evaluation, Recommendations and Clarifying questions sections.
