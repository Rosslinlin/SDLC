# Jira text in the canonical JSON

Read during [JSON assembly](jira-json-assembly.md), before independent review and scoring. The JSON already contains the values Jira will receive. SPEC and TEST_CASE.md stay Markdown. This restores the former exporter's presentation conversion at generation time; export never renders or rewrites the scored file.

## Fields and examples

Write fields.description and fields[acceptanceCriteria] as Jira wiki strings. Apply the same encoding to other intentionally authored rich-text fields only when metadata confirms a writable text-compatible field. Plain text needs no added markup. Do not format Summary, routing, IDs, option values, labels, dates, numbers, URLs, attachment paths, opaque custom strings or unchanged update fields.

Decoded field values look like this (these are text, not tool arguments):

```text
h2. User Story
As a permitted reviewer, I want to open an existing application, so that I can see its supplied details.

h2. Business Context
<supported non-AC context>
```

```text
* *AC-001*: Given a permitted reviewer and an existing application, when the reviewer opens the application, then the details panel displays the supplied application details.
```

In the AC example, the first asterisk and space form a list item; the paired asterisks around AC-001 mean bold. Do not add literal backslashes before these markup characters or use Markdown `**AC-001**`. Keep each AC declaration and its Given/When/Then exactly once in the mapped field. The field label supplies the Acceptance Criteria heading; no duplicate heading is needed in this value. This example does not establish an additional exact-heading parser gate.

## Presentation mapping

Convert authored Markdown presentation outside code once, without changing business wording or order:

| Markdown source | Jira wiki value |
| --- | --- |
| `#` through `######` headings | `h1.` through `h6.` |
| `**bold**` / `_italic_` or `*italic*` | `*bold*` / `_italic_` |
| Unordered / ordered lists | `*` / `#`; repeat markers for nested levels, preserving mixed nesting |
| Fenced code / inline code | `{code}` block / `{{inline code}}`; preserve the exact code body |
| `[label](https://example)` | `[label\|https://example]` |
| Block quote | `bq.` line |
| Markdown table | Double-pipe header and single-pipe body; remove only the Markdown separator row |

Preserve actual newlines and blank lines; JSON serialization represents newlines with `\n`, quotes with `\"` and a literal backslash with `\\`. Decode the saved JSON once to check actual strings: never double-encode a JSON document or replace line breaks with literal `\\n` or HTML break tags. Existing Jira wiki must remain idempotent; identify the input format before converting, and do not reinterpret `*bold*` as Markdown italics or `#` list markers as Markdown headings. Leave code, literal syntax examples, AC IDs, dates, decimals and prose punctuation intact. An unsupported construct needs a focused technical correction, never omitted or paraphrased content.

Compare complete field values against the source sections: no lost/repeated AC, section, list item, table cell, identifier or link. No unintended raw Markdown presentation remains outside code/literal examples. This is an assembly/transport precheck, not a new Q1–Q7 business criterion.

## Markdown view and existing candidates

Derive the SPEC projection by expressing the same Jira wiki business content in readable Markdown: `*bold*` becomes `**bold**`, and Jira lists/links/code/tables become their Markdown equivalents. Embed it under `## Jira Story` using the [projection template](../templates/story-projection-template.md): the Story title is `###`, source top-level business headings become `####`, with their grouping nested below. Do not mechanically turn every `h2.` into a peer `##` section. Preserve wording, IDs, section order, code bodies and link targets; rebase only authored headings, not heading-like code/literals. Include one Acceptance Criteria heading in this projection. Do not copy wiki markup blindly or write the Markdown view back into canonical JSON.

For an unexported older JSON, migrate its AC field-ID mapping to root acceptanceCriteria, remove the old customFields wrapper, root staffId and fields.reporter, and encode affected authored rich text before a new score. Preserve trusted platform-user scope, actual mapping ID, business text, unrelated approved values and attachment intent; do not reconstruct a staff-ID setup or fallback profile key. Record a new JSON revision. Recheck affected projections/tests for unchanged meaning; formatting/schema-only changes do not require a business rewrite or full new content review. The changed file needs a current score and fresh final confirmation. Preserve prior score/attempt/remote-outcome evidence; never replay a successful or unknown export to migrate formatting.
