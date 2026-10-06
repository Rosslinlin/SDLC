# Common Jira Text Rendering

## Scope

Use this module for Jira-visible rich-text fields in generic create, generic update and generic non-Test batch routes. The SDLC gateway applies the same transport principles with its stricter reviewed-Story rules.

This is a transport transformation only. Keep the workspace source unchanged, derive the Jira value from the complete current source, and preserve wording, section order, identifiers, links and meaning. The preview must show the exact rendered value that will be sent.

## Hard Test Case Exclusion

Do not apply this module to:

- `flow-testcase-source-export.md`;
- an item whose Jira issue type is `Test` or the platform's equivalent Test Case type;
- Test Case table descriptions, execution fields, `testDetailsJson`, `testDetails`, `testSteps`, `step`, `data`, `result`, Preconditions, Test Data, Test Steps or Expected Results; or
- a mixed batch item's Test Case payload.

The dedicated Test Case modules and their newline/table rules remain the sole authority for those values. Do not load this module as a replacement for them. In a mixed generic batch, render eligible non-Test items independently and leave every Test Case item byte-for-byte under its existing Test Case assembly rules.

## Eligible Values

Render only Jira-visible text that is being created or intentionally changed:

- top-level `description` for non-Test issues;
- non-Test item descriptions inside `issuesJson`;
- Acceptance Criteria or another rich-text custom field when Jira metadata identifies it as writable text and its source is Markdown; and
- other explicitly Markdown-authored multiline Jira text fields when metadata confirms a text-compatible schema.

Do not render Summary, project/type keys, issue keys, labels, option values, dates, numbers, user IDs, URLs, filenames, attachment controls, link controls or untouched fields on an update. Do not infer that an opaque custom string is Markdown merely because it contains punctuation.

## Markdown-To-Jira-Wiki Mapping

Outside fenced code blocks, convert recognized Markdown presentation syntax once:

| Workspace Markdown | Jira wiki payload |
| --- | --- |
| `# Title` through `###### Title` | `h1. Title` through `h6. Title` |
| `- item` or `* item` | `* item` |
| nested unordered list | repeat `*` for the nesting level |
| `1. item` | `# item` |
| nested ordered list | repeat `#` for the nesting level |
| fenced code block | `{code}` block with the body preserved exactly |
| inline code `` `value` `` | `{{value}}` |
| `[label](https://example)` | `[label|https://example]` |
| Markdown block quote | `bq. ` line |
| Markdown table header/body | Jira `|| header ||` and `| cell |` rows; omit the Markdown separator row |

Preserve blank lines and intentional line breaks as newline characters. Never use literal `<br>`, `<br/>`, `<br />` or escaped equivalents as a line-break substitute. Do not interpret dates, AC IDs, decimal numbers or prose punctuation as list markers. Do not convert Markdown-like characters inside fenced or inline code.

Existing Jira wiki syntax is idempotent: do not prefix `h1.` through `h6.` again, duplicate Jira list markers, or re-render an existing `{code}` block. Plain text without recognized Markdown structure remains unchanged.

## Validation Before Preview And Export

For every rendered field:

1. retain the complete source value and the transport-rendered Jira value in current flow state until submission;
2. compare them section-by-section and confirm that only presentation syntax changed;
3. verify no raw Markdown heading, fenced-code delimiter, Markdown link, or Markdown table separator remains outside code where a supported conversion applies;
4. verify no text, list item, table cell, identifier or link was added, removed, summarized, reordered or paraphrased;
5. show the rendered value and a concise conversion-status summary in `jira-preview.md`; and
6. submit exactly that previewed rendered value.

If a construct cannot be converted without changing meaning, block the affected item and show its field and source location. Do not guess, silently drop it, or rewrite the workspace document.

For updates, rendering a requested replacement field does not authorize formatting or changing any existing untouched field. Any payload-affecting correction after generic confirmation or SDLC final read-back invalidates the binding and requires the route's normal fresh preview procedure.
