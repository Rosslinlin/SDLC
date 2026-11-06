### [fields.summary]

[Render all supported non-AC business sections from fields.description in their original order. Place their top-level headings at #### beneath the Story title and preserve their internal grouping below that. Change presentation syntax only; preserve wording, code, link targets, actor/goal/value, scope and material dependencies. Do not add facts to make a section longer.]

#### Acceptance Criteria
[Read acceptanceCriteria as a field ID, then project the actual Jira wiki from fields[that ID] as Markdown, e.g. * *AC-001*: to - **AC-001**:. Keep each AC's stable ID and Given/When/Then and any supported BDD detail. Never render the mapping ID as AC text or look for an old top-level AC/BDD array.]

#### Attachments
[Name only the business SPEC files actually selected for upload, or state no attachment changes for an authorized none update.]

<!-- This projection lives inside SPEC, not a standalone STORY.md file.
JSON is authoritative. Preserve all business sections including those after AC and approved business values from fields[actual field ID]; root acceptanceCriteria is a field-ID mapping only. Omit routing/staff/session/schema IDs and transport controls.
Use exactly one Acceptance Criteria heading in this readable view. Never change canonical JSON to imitate an unrelated Markdown example.
This fragment is embedded under SPEC's ## Jira Story: no additional # document title or ## peer section belongs inside it. Rebase authored headings only; do not alter heading-like text inside code/literal examples.
No score number or raw diagnostics belongs here. -->
