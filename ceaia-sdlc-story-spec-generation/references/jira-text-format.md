# Jira text in the canonical Story package

Read during [JSON assembly](jira-json-assembly.md), before independent SPEC review and scoring. The two `.jirawiki` files contain the Description and AC bodies Jira will receive; the JSON contains their paths. SPEC and test-case.md stay Markdown. This restores the former exporter's presentation conversion at generation time; export never renders or rewrites the scored package.

## Fields and examples

Write Description to the file at root descPath and AC to a distinct file at root acPath, each as Jira wiki text with actual line breaks. Apply the same encoding to other intentionally authored rich-text fields only when metadata confirms a writable text-compatible field. Plain text needs no added markup. Do not format Summary, routing, IDs, option values, labels, dates, numbers, URLs, attachment or wiki paths, opaque custom strings or unchanged update fields.

The wiki file contents look like this (these are text, not tool arguments):

```text
h2. User Story
As a permitted reviewer, I want to open an existing application, so that I can see its supplied details.

h2. Business Context
<supported non-AC context>
```

```text
* *AC-001*: Given a permitted reviewer and an existing application, when the reviewer opens the application, then the details panel displays the supplied application details.
```

In the AC example, the first asterisk and space form a list item; the paired asterisks around AC-001 mean bold. Do not add literal backslashes before these markup characters or use Markdown `**AC-001**`. Keep each AC declaration and its Given/When/Then exactly once in the acPath file. The Jira field label supplies the Acceptance Criteria heading; no duplicate heading is needed in this file. This example does not establish an additional exact-heading parser gate.

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

Preserve actual newlines and blank lines in each `.jirawiki` file; the JSON paths are ordinary strings. Read back both files to check the content: never double-encode a JSON document or replace line breaks with literal `\n` or HTML break tags. Existing Jira wiki must remain idempotent; identify the input format before converting, and do not reinterpret `*bold*` as Markdown italics or `#` list markers as Markdown headings. Leave code, literal syntax examples, AC IDs, dates, decimals and prose punctuation intact. An unsupported construct needs a focused technical correction, never omitted or paraphrased content.

Compare both complete wiki files against the source sections: no lost/repeated AC, section, list item, table cell, identifier or link. No unintended raw Markdown presentation remains outside code/literal examples. This is an assembly/transport precheck, not a new Q1–Q7 business criterion.

## Markdown view and existing candidates

Derive the SPEC projection from the two referenced Jira wiki files by expressing the same business content in readable Markdown: `*bold*` becomes `**bold**`, and Jira lists/links/code/tables become their Markdown equivalents. Embed it under `## Jira Story` using the [projection template](../templates/story-projection-template.md): the Story title comes from fields.summary and is `###`; source top-level business headings become `####`, with their grouping nested below. Do not mechanically turn every `h2.` into a peer `##` section. Preserve wording, IDs, section order, code bodies and link targets; rebase only authored headings, not heading-like code/literals. Include one Acceptance Criteria heading in this projection. Do not copy wiki markup blindly or write the Markdown view back into the canonical files.

For an unexported older JSON, migrate its AC field-ID mapping to root acceptanceCriteria, move its Description and AC bodies into two distinct `.jirawiki` files, and replace the inline fields with root descPath/acPath. Remove the old customFields wrapper and root staffId, and encode affected authored rich text before a new score. Retain an older fields.reporter only when current applicable metadata requires it or the user explicitly requests it and its native value is resolved; otherwise omit it without treating it as a user-default preference. Preserve trusted platform-user scope, actual mapping ID, business text, unrelated approved values and attachment intent; do not reconstruct a staff-ID setup or fallback profile key. Record new JSON and wiki revisions. Recheck affected projections/tests for unchanged meaning; formatting/schema-only changes do not require a business rewrite or full new content review. The changed package needs a current score and fresh final confirmation. Preserve prior score/attempt/remote-outcome evidence; never replay a successful or unknown export to migrate formatting.
