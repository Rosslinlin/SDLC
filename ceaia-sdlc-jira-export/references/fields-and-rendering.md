# Story fields and Jira transport

Use the current applicable field profile from `workspace-profile.md` or the metadata just retrieved for a genuine miss. A complete profile is valid field evidence across sessions; this step does not require another query. Work item by item and never inherit another Story's AC, metadata values or attachment plan.

## Required and optional values

For updates, first enforce the reviewed manifest's authorised field scope. A settings selection/confirmation does not expand it. Preserve or omit every unapproved field; a newly requested business-bearing field change returns upstream for affected authorisation/review before export. Only then apply the following value-resolution order to fields allowed to change.

For each required field, resolve the actual value in this order:

1. Current explicit user selection and the reviewed handoff's direct mapping. User input cannot silently alter reviewed business content; return a semantic content change upstream.
2. For updates, the preserved actual baseline/current value and authorised update scope. Omit unchanged fields. Create defaults never overwrite an existing field or relationship implicitly.
3. An applicable user-confirmed reusable default, then a visible schema-compatible Jira default, where neither conflicts with current evidence.
4. A directly evidenced valid option from this reviewed handoff. Do not guess assignees, components, versions, dates or business decisions.
5. A focused `ask_user_question` identifying the missing required field, purpose and known valid options. Ask only for unresolved values; never ask users to retype Summary, Story Description or AC, or decide which fields Jira requires.

For each value record provenance (`user-provided`, `reviewed-handoff`, `current-value`, `profile-default`, `metadata-selected`, or `inferred`). Missing required values block submission. Unknown optional fields are omitted. Add an optional field only when requested, reviewed as relevant or explicitly adopted as a reusable preference; do not dump the entire metadata catalog into the payload or popup.

Preserve the field's type, `operations`, required status and visible option identifiers. Display labels are not automatically transport IDs. Use the exact cached/returned representation for single/multi-select, user, date, array/object and text fields. If a new selected option is outside the cached verified subset, resolve it under the profile's refresh rule; do not fabricate its ID or load irrelevant option catalogs. Do not treat create-time required fields as permission to edit unchanged fields on update.

Before submission the user confirms the current settings once: source/project/Story type, create/update operation, exact update Ticket Number(s), Epic/Parent values or preservation/no-Epic decision, and actual supplied/defaulted metadata field values. Show retained/unavailable untouched update fields as such, without inventing values. Allow Use/Change; changing a value invalidates only affected confirmation/preview bindings. This settings confirmation is required on first and later runs; final preflight after it has no additional approval popup.

Every create must have an explicit Epic decision even when Jira marks Epic optional: a confirmed applicable saved Epic, a newly validated Epic key, or the user's explicit `no-epic` choice. If none exists, collect it before settings confirmation. An empty/unknown Epic is not an implicit no-Epic choice. A saved confirmed Epic is reused without a remote re-read. For a newly selected Epic, use `java-base-mcp.getJiraInfos` once with the supported source/key arguments from the live schema; confirm exact returned key, existence and suitability for the selected Story project. If returned evidence cannot establish suitability, resolve that specific limitation without inventing response fields. Never submit `none`/`N/A` as an Epic key. Update relationships remain preserved unless their change is explicitly authorised. Use direct `epicLink` and `parentLink` arguments when applicable, distinct from `linkedIssueKeys`/`linkType`; do not hide these dedicated fields in `dynamicFieldsJson`.

## Canonical Story partition

Read the exact reviewed Story once for mapping. It must contain exactly one standalone `## Acceptance Criteria` heading outside code. That section ends before the next `# ` or `## ` heading outside code, or EOF. Lower-level headings inside it remain part of AC.

- Description source = every other Story section in its original order, including all sections after AC.
- AC source = only that section's body; the Jira field label represents the removed heading.
- Summary for create = reviewed title or an explicitly reviewed supported Summary. For update, preserve Summary unless a reviewed replacement is authorised.

Render Description and AC independently. Description must contain none of the extracted AC heading/declarations/Given-When-Then bodies. Put every extracted `AC-###` declaration and its original Given/When/Then wording exactly once in the profile-confirmed writable text-compatible AC field via `dynamicFieldsJson`; never put AC in `description`. Other Story references to an AC ID are not declarations to delete.

On update, omit AC replacement only when the separate AC value in the upstream baseline actually matches the reviewed IDs, wording and order after allowed presentation/line-ending normalization. Empty, unavailable or uncertain AC is not a match; include the reviewed AC replacement when needed and authorised by the handoff. Never query Jira again solely to make this comparison.

If the canonical boundary, declarations, field identity, text schema or writable `set` operation is uncertain, block the affected mapping. Preserve the source; do not invent a field ID or put AC back into Description to bypass the problem.

## Markdown to Jira wiki

Only transform Markdown-authored rich text in the fields being created or intentionally replaced. Keep the saved Workspace Story unchanged.

| Markdown | Jira wiki |
| --- | --- |
| `#` through `######` headings | `h1.` through `h6.` |
| `- item` or `* item` | `* item`; repeat `*` for nested unordered levels |
| `1. item` | `# item`; repeat `#` for nested ordered levels |
| fenced code block | `{code}` block, exact body |
| inline code | double-brace Jira inline code, exact body |
| `[label](https://example)` | `[label|https://example]` |
| Markdown block quote | `bq. ` line |
| table header/body | `|| header ||` and `| cell |`; remove only Markdown separator row |

Preserve actual newlines and blank lines; do not introduce `<br>`, its variants or escaped equivalents as line breaks. Do not transform code bodies, AC IDs, dates, decimal numbers or prose punctuation into structure. Existing `h1.`–`h6.`, Jira lists and `{code}` blocks remain idempotent. Preserve mixed list nesting and every link/table cell; block a construct if its lossless mapping cannot be established.

Do not render Summary, keys, type names/IDs, labels, dates, option values, user IDs, plain URLs, filenames, attachment/link controls or untouched update fields. Punctuation alone does not establish Markdown authorship.

Compare each rendered field with its complete source section-by-section. Description plus AC must account for the entire reviewed Story, with only the AC heading represented by the Jira label and supported presentation syntax changed. Never summarize, paraphrase, omit, reorder or replace business text with a file pointer. Check the decoded effective payload, not only prose preview claims: no unmapped Markdown headings/fences/links/table separators outside code, no duplicated/lost AC and no lost post-AC sections.

## Argument and empty-value discipline

Direct named tool arguments are defined in `jira-write-and-recovery.md`. Keep fields with dedicated top-level parameters out of `dynamicFieldsJson`; AC and other supported metadata fields belong inside it. `linkedIssueKeys`/`linkType` and attachment controls never go inside dynamic fields. De-duplicate only explicitly supplied/required labels; omit `CEAIA_GEN` because the backend adds it.

Omit unused optional arguments. Use `""` only when the live tool explicitly accepts an inactive optional string; preserve supported array/object/null types. Never use `.`, `-`, `N/A`, `undefined`, whitespace or the string `"null"` to satisfy missing values. An update omission preserves a field; it is not permission to clear it. Resolve real required values or stop.

`dynamicFieldsJson`, `attachmentsJson` and `issuesJson` are strings containing one encoded JSON value at their own level. After reading each value once, it must reconstruct the authorised object/array and preserve all Story text. The skill never sends `testDetailsJson`, unsupported `issues`, `requestJson` wrappers or internal handoff/score/state/profile metadata.
