# Flow Test Case Source Export

Use this text-only flow when the user exports Test Cases from a selected file, pasted table, spreadsheet-like source, or generated Test Case list. It is separate from the validated SDLC Story/SPEC export. Keep the generic route's preview and explicit confirmation, and do not apply the non-Test Markdown-to-Jira renderer.

Read the relevant sections of `common-tools-and-inputs.md`, `common-state-and-user-info.md`, `common-project-issue-type-validation.md`, `common-field-assembly.md`, `common-preview-confirm-export.md`, and `common-guardrails.md`. The rules below control the two source-based Test Case payload shapes; generic batch examples do not replace them.

## 1. Select source, mode, and Test Cases

1. Identify the current source from the user's explicit selection or unambiguous current conversation/workspace content. If several sources are possible, ask the user to select one. Read the complete source; do not substitute an earlier preview or assume a filename.
2. Parse Test Case IDs, titles/descriptions, Preconditions, Test Data, Test Steps and Expected Results in source order. Preserve source wording and every selected row. Resolve ambiguous or missing IDs and any selected subset before payload assembly.
3. For every export request, collect one constrained `uploadType` choice: **Create individual tickets** maps to `multiple`; **Group into a single ticket** maps to `single`. Do not reuse a previous choice or send `uploadType` to Jira.
4. Resolve real `staffId`, supported `almType`, project and Jira Test issue type using the current tool contracts and metadata. Do not infer hidden field IDs or required values.

## 2. Preserve executable content

Pair each source Test Step with its corresponding Expected Result by item number and order. Preserve Test Data with that pair. When Test Data is absent and the Test Details schema requires `data`, use an empty string for that one value. A mismatch or uncertain pairing blocks export until corrected.

Keep numbered steps and results on separate lines. Normalize source HTML break markers to actual newline characters in Jira-visible fields; in serialized JSON these appear as escaped `\n`. Do not leave literal or escaped `<br>` variants, collapse numbered steps into one line, paraphrase content, or replace content with a source-file reference. Never ask the user to retype execution fields already present in the source.

## 3. Forced final payload shapes

Both modes call `java-base-mcp.exportJiraByDynamicFields` with direct named arguments, never a request wrapper. JSON-string arguments are serialized exactly once and checked by parsing them back before preview and submission. Use actual metadata-confirmed values, not example IDs or placeholders.

### Create individual tickets: `multiple`

Make one `issuesJson` argument containing a JSON-encoded non-empty array with one item per selected Test Case, in source order. Each item has its own actual `projectKey`, `summary`, `issueType` (Test), and `description` containing only its source Test Case title. Do not build a long description from Requirement, Priority, Preconditions, Test Steps, Expected Results or Comments.

Put other validated Jira fields in that item's JSON-encoded `dynamicFieldsJson` object only when needed; the backend adds `CEAIA_GEN`. When the source contains steps/results, the item's `testDetailsJson` is a JSON-encoded string containing its ordered array of `step`, `data` and `result` objects. It is a string inside the decoded `issuesJson` item, not a native array under the `testDetailsJson` key. Keep all selected cases in this one batch call. Never use an unsupported top-level `issues` argument.

### Group into a single ticket: `single`

Make one Test issue using top-level `projectKey`, `summary`, `issueType`, and `description`; include JSON-encoded `dynamicFieldsJson` only for other validated fields. The backend adds `CEAIA_GEN`. Do not include `issuesJson`.

The entire `description` is a clean table of the selected source Test Cases in source order, with exactly these five columns: `Test Case Id`, `Test Case Description`, `Preconditions`, `Test Steps`, `Expected Results`. Omit the Markdown separator row and all extra prose or source-only columns, including Test Data. Preserve each cell's full source content and required newline characters; source Test Data remains in the Test Details payload when applicable.

When steps/results are present, include top-level `testDetailsJson` as a JSON-encoded string containing the ordered step/data/result objects for the selected cases. Preserve the source case and step order; use the Test Case IDs in the description table to make the grouping inspectable. If the selected source cannot be represented without losing case boundaries or execution content, block this shape and ask for a corrected mapping or a new explicit mode choice. Never silently switch to `multiple`.

## 4. Preview, confirmation, and result

Write the single current `jira-preview.md` before asking for the generic explicit confirmation. Show the selected source and mode, selected/omitted IDs, source-to-payload mapping, the exact direct tool arguments, decoded `issuesJson` items when applicable, the table or per-item descriptions, Test Details, and line-break/integrity checks. Confirm that every Jira-visible Test Case value is self-contained and that no unsupported `issues` or `uploadType` argument is present.

After explicit confirmation, re-read the final payload and submit exactly the previewed arguments in one tool call. A changed payload needs a new preview and confirmation. Report each Test Case ID to returned Jira key/URL mapping for `multiple`, or the selected ID list and one Jira key/URL for `single`. Preserve partial results and sanitised errors; do not infer sequential ticket keys, replay an uncertain write, or apply the SDLC second binding call.
