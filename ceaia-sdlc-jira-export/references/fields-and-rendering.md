# Field values and Jira transport

Use the applicable complete profile or genuinely needed fresh metadata. Keep every item's content, baseline, values and attachment plan separate.

## Resolve and confirm allowed values

For updates, the reviewed manifest first limits which fields may change. Preserve/omit all other fields. Settings confirmation never expands that scope; a new business change returns upstream. Resolve allowed required values in this order:

1. Current explicit user choice and reviewed direct mapping, without silently changing reviewed business content.
2. Actual update baseline/current value; omit unchanged fields. Create defaults never overwrite it.
3. Applicable user-confirmed reusable default, then an observed schema-compatible Jira default.
4. A directly evidenced valid option from this handoff.
5. A focused ask_user_question for the unresolved required value, its purpose and actual valid options.

Record provenance as user-provided, reviewed-handoff, current-value, profile-default, metadata-selected or inferred. Do not guess identities, dates, versions or business choices. Unknown optional values are omitted; include optional fields only when requested, reviewed as relevant or explicitly adopted as reusable preferences. Preserve actual schema types, required flags, operations and option IDs/encoding; labels are not transport IDs. Never ask the user to retype generated Story/AC or decide which fields Jira requires.

Show source/project/Story type, operation, exact update key(s), Epic/Parent decision and actual supplied/defaulted field values in one Use/Change settings confirmation on every export, including first use. Identify preserved/unavailable untouched update fields honestly. Resolve missing values before confirmation; changed settings invalidate affected confirmation and preview only. Final preflight has no additional approval popup.

Every create resolves an explicit applicable saved Epic, a newly validated Epic, or no-Epic choice even if Jira marks Epic optional. Unknown/empty is not no-Epic. Verify a newly supplied Epic once through live getJiraInfos evidence of exact key/existence/suitability; resolve limitations without invented response fields. Reuse saved confirmed Epics without rereading. Never submit none/N/A as a key. Relationships change on update only when reviewed authorization allows it.

## Partition the reviewed Story

Read the saved Story. Its one standalone `## Acceptance Criteria` heading outside code begins AC; the next `# ` or `## ` heading outside code, or EOF, ends it. Lower-level headings stay in AC.

- Description source is every other Story section in original order, including sections after AC.
- AC source is only that section's body; the Jira field label represents the removed heading.
- Create Summary uses the reviewed title/supported Summary. Update Summary is preserved unless its reviewed replacement is authorized.

Render Description/AC separately. Every AC declaration and original Given/When/Then appears exactly once in the metadata-confirmed writable text-compatible AC field through dynamicFieldsJson, never in description. References to an AC ID elsewhere are not declarations to remove. On update omit AC replacement only when the separately retrieved baseline actually matches reviewed IDs/wording/order under allowed presentation/line-ending normalization; unavailable or empty is not a match. If replacement is needed but unauthorized, return upstream. Do not reread Jira solely for this comparison.

An uncertain section boundary, field identity/type or writable operation blocks mapping; do not guess an ID or move AC into Description.

## Render Markdown to Jira wiki

Transform only authored rich text intentionally written by this operation; keep Workspace files unchanged.

| Markdown | Jira wiki |
| --- | --- |
| Headings # through ###### | h1. through h6. |
| Unordered/ordered lists | * / # with repeated markers for nested levels |
| Fenced code / inline code | {code} block / double-brace inline code; exact body |
| [label](https://example) | [label\|https://example] |
| Block quote | bq. line |
| Table | double-pipe header, single-pipe body; remove only Markdown separator row |

Preserve mixed nesting, links, table cells, actual newlines and blank lines. No HTML break tags as transport line breaks. Never transform code bodies, AC IDs, dates, decimal numbers or prose punctuation into structure. Existing Jira headings/lists/code remain idempotent. Do not render Summary, keys, IDs/options, labels, dates, URLs, filenames, controls or untouched fields.

Compare decoded rendered fields with their complete source sections. Together Description and AC must preserve all wording/order, including post-AC sections, with only presentation syntax and the removed AC heading changed. No paraphrase, omitted text, file-pointer substitution, duplicate/lost AC or unmapped Markdown structure outside code. Block an unsupported construct rather than changing its meaning.

## Arguments and empty values

Use direct arguments defined in jira-write-and-recovery.md. epicLink/parentLink and linkedIssueKeys/linkType remain dedicated top-level fields, not dynamicFieldsJson. Pass linkedIssueKeys as its native string array. Omit CEAIA_GEN because the backend adds it; de-duplicate only supplied/required labels.

Omit unused optional arguments. An empty string is allowed only for a live-contract accepted inactive optional string. Preserve supported array/object/null types. Missing required values block; never use fabricated placeholders. Omission on update preserves a field and never authorizes clearing it.

Each dynamicFieldsJson, attachmentsJson and issuesJson string encodes its own object/array once; parse once to verify the exact effective values. No testDetailsJson, unsupported issues argument, requestJson wrapper or internal workflow metadata.
