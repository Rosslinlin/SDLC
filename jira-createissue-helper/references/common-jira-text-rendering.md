# Render authored rich text during issue preparation

Use only for newly prepared or explicitly changed non-Test rich text whose source is known to be Markdown. Complete conversion before any requested/upstream assessment and final preview. For a new Story, write Description and AC as Jira Wiki using the one- or two-file layout in [Story field assembly](common-field-assembly.md#story-specific-mapping). Keep other applicable rich-text fields as native JSON values. Export does not trigger another rendering pass. Keep original source documents unchanged.

## Eligible values and exclusions

Eligible values are the newly prepared Story Description/AC Wiki files, intentionally created/replaced rich-text fields for other issue types or supported inline legacy Story files, and other metadata-confirmed rich-text strings. Do not infer Markdown from punctuation in an opaque custom string. Do not render root Wiki paths as content.

Never render Summary, routing/type/issue IDs, labels, options, dates, numbers, account references, URLs, filenames, attachment/link controls or untouched fields. All Test Case descriptions/tables, Preconditions, Test Data, Steps, Expected Results and Test Details belong exclusively to [the Test Case flow](flow-testcase-source-export.md). In mixed selections, prepare non-Test fields independently without applying their formatting/defaults to Test files.

## Convert once, preserving meaning

Outside code/literal examples, convert known Markdown presentation syntax:

| Source Markdown | Jira wiki value |
| --- | --- |
| `# Title` through `###### Title` | `h1. Title` through `h6. Title` |
| `**bold**`, Markdown italics | `*bold*`, `_italic_` |
| Unordered / ordered list | `*` / `#`, preserving nested and mixed levels |
| Fenced code / inline code | `{code}` block / `{{inline code}}`, exact body |
| `[label](https://example)` | `[label\|https://example]` |
| Block quote | `bq.` line |
| Markdown table | Jira double-pipe header/single-pipe body; remove only the separator row |

Preserve wording, substantive list structure, cell values, section order, link targets, identifiers and examples. Source numbers that are identifiers or criterion labels stay in content; do not turn dates, decimals, AC IDs or prose punctuation into list markers.

Preserve exact fenced/inline code and literal Markdown examples. Existing Jira wiki is idempotent: do not turn `*bold*` into italics, duplicate h1./list markers, wrap existing code again or change Jira links. Plain text stays plain text. Ambiguous format or unsupported constructs need a mechanical mapping decision, not guessed or dropped content.

Keep actual newlines and intentional blank lines in `.jirawiki` files and decoded JSON strings; JSON serialization escapes line breaks as `\n` only for inline fields. Do not replace file line breaks with literal backslash-n content, doubled escaping or HTML break tags; normalize real break surrogates where meaning survives while preserving literal code examples.

## Verify, save and freeze

Compare the complete source and rendered value once after assembly. For a new Story, read back the referenced Wiki file or files and verify full Description and AC coverage in their assigned locations. Confirm presentation-only changes and full content coverage, and record a concise conversion basis in existing preview/context. Do not create another business-content copy.

Save and read back native JSON and any referenced Story Wiki files under [the file contract](common-json4jira-contract.md); preview the resolved content. Preserve any [assessment evidence](story-assessment-evidence.md) against its actual input. No late rendering, partition or reserialization is permitted. A user-authorized file change makes an earlier score historical and needs fresh [confirmation](common-preview-confirm-export.md); obtain another assessment upstream only where the actual project/backend/user requires it. This helper does not call the scorer or require every Story to be scored. An unchanged final read-back reuses verified conversion evidence instead of repeating the procedure.
