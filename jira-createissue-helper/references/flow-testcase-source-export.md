# Test Case source export

Use this complete flow for Test Cases selected from a readable file, pasted table, structured source or generated Test Case list. Its two business shapes take priority over generic create/batch assembly. Read the [JSON contract](common-json4jira-contract.md), [profiles and missing inputs](common-state-and-user-info.md), and [preview/submission/recovery](common-preview-confirm-export.md) only as needed. Do not apply the ordinary Markdown-to-Jira renderer to Test Case content.

## 1. Resolve this export

1. Use the user's latest explicit source selection. Otherwise use a clearly identified attached/pasted source or unambiguous current content. Inspect Workspace for a source only when the user selected Workspace content. Ask for a choice when several sources remain; a filename, prior preview, Jira result or example does not establish the current source.
2. Read and parse the complete selected source. Identify Test Case ID, Title, Preconditions, Test Steps, Expected Results and Test Data by actual headers or unambiguous equivalents. Preserve source IDs, row order and mapped cell values. Unreadable/truncated input, unknown ID columns or ambiguous required mappings need correction before assembly.
3. Show all recognized candidates with ID, Title and available requirement reference. Include draft/review/risk/to-confirm rows by default; these labels do not exclude a case. Apply only the user's actual subset selection and retain selected source order.
4. Resolve one constrained choice for this export: **Create individual tickets** → `multiple`; **Group into a single ticket** → `single`. Reuse a choice already recorded for this request, never a previous export's choice. Keep `uploadType` in current flow context only, outside profiles, issue files and tool arguments. Never silently change modes.
5. Reuse confirmed source/project/actual Test issue type and matching user profiles; query only missing applicable facts. Backend source values are lowercase `wpb/alm/fcr/go/data`. Platform authentication supplies identity; never collect or serialize credentials, JWTs or Conversation-ID. Follow the live query schema without adding identity fields to the saved issue.

These are non-Story Test issues. They require neither Story scoring nor a SPEC. Do not relabel a Story as Test to place it in a non-Story manifest; Story files use individual calls even when scoring is not required.

## 2. Preserve the mapped source values

Normalize line-break surrogates such as `<br>`, `<br/>`, `<br />` and escaped equivalents back to actual newlines. JSON encodes these as `\n`. Keep numbered list items on separate lines with unchanged wording, numbers and order; compare against the source rather than renumbering it to make it contiguous. Do not shorten, paraphrase, merge, reorder or replace mapped execution content with a file/attachment/preview pointer.

Distinguish an intentionally empty source cell from a missing field or unresolved mapping. Preserve permitted intentional empties; do not invent missing execution content. A missing or ambiguous required source mapping blocks the affected export.

For `multiple`, identify independently numbered steps/results first. Matching numbers/order establish separate pairs even when several pairs occupy one spreadsheet cell or JSON string: create one native Test Detail per pair, preserving each source number and its complete wording. Do not combine two identifiable numbered pairs merely because the source stores them in one string.

Only when the source genuinely supplies indivisible step/result blocks with no safely identifiable separate pairing, preserve those complete blocks in one entry. An uncertain pairing needs clarification, not guessed alignment. Preserve corresponding per-step Test Data; clearly case-wide supplied Test Data remains the same exact shared block on its applicable entries, never invented per-step values. Use `data: ""` when absent; never move another column into it.

## 3. Save the selected mode's native JSON

Templates are shapes only; replace every placeholder with verified source/schema facts. Each issue file carries its own `jiraInstance`, `projectKey`, actual `issueType` and native `fields`. Add genuinely required metadata fields using their real types without rewriting the mapped source content. Both modes include `CEAIA_GEN` in native `fields.labels`.

Omit root `staffId`, session/credential overrides, workflow controls, `attachments`, inline batches, `issuesJson`, `dynamicFieldsJson` and Story AC mappings. Source Test Case IDs identify mapping records; they do not select existing Jira update targets. This flow creates new Test issues, so its files omit `issueIdOrKey`.

### `multiple`: independent Test issues plus one path manifest

Use the [individual template](../templates/testcase-individual-template.json).

- Save one actual `.json4jira` per selected case, in selected source order.
- `fields.summary` and `fields.description` both equal that case's source **Title**, not its longer Description or other metadata. Do not append Preconditions, Steps, Results, Test Data, Priority, Requirement references or Comments to Description.
- Root `testDetailsJson` is a **native array**, never a JSON string or `testDetails` alias. Each entry contains only `step`, `data`, `result`, all with string values holding the complete paired source content and necessary newlines.
- Save one [manifest](../templates/path-manifest-template.json) containing only `story_json_workspace_paths`: an ordered, nonempty array of the exact child Workspace-relative paths. No duplicates, nesting, inline issues or inherited defaults. Read every child, not just the manifest.
- The sole export input points to the manifest. Keep this approved batch in one tool invocation; do not split it into repeated creates.

### `single`: one aggregate Test issue

Use the [aggregate template](../templates/testcase-aggregate-template.json).

- Save one actual aggregate `.json4jira`, not an individual-case manifest.
- Use the user's aggregate title for `fields.summary`; if none is supplied, generate a concise title from the selected cases and include it in the final confirmation.
- `fields.description` contains only the clean table. Its exact first row is `|Test Case Id|Test Case Description|Preconditions|Test Steps|Expected Results|`; the next logical row is the first selected case.
- Add exactly one logical data row per selected case, with these five mapped cells: source ID, source Title, Preconditions, Test Steps, Expected Results. Keep original order and complete mapped cell values, including in-cell newlines.
- Do not add a Markdown separator/alignment row, blank rows, headings, introductions, summaries, notes, references or footer text. Do not insert source-only columns such as Test Data, User Story, Requirement/AC references, Priority, Status or Comments.
- Omit both `testDetailsJson` and `testDetails`. Test Data is intentionally not exported in this mode; do not shift it into Steps or Results. Describe this as an aggregate table, without claiming it uploads executable Test Details.
- If literal pipe characters or row boundaries make a lossless five-cell mapping ambiguous, resolve a supported mapping before export. Do not remove content, invent an escaping convention or use `<br>` to force it to fit.

## 4. Verify, preview and confirm the saved files

Read the saved aggregate or manifest and every child. Complete one source-to-file check before preview:

| Check | Required observation |
| --- | --- |
| Selection | Exact selected source IDs, row count/order, and ID → child path/manifest index or aggregate logical row mapping. |
| Native shape | Actual source/project/type, `CEAIA_GEN` labels and mode-specific structure. No serialized Test Details, forbidden aliases/controls, sample values or pointer substitutes. |
| `multiple` | Exact source Title in both Summary/Description; one file per selected case; one native entry per identified step/result pair, full source values and actual data scope. A shared source string does not collapse separate numbered pairs. |
| `single` | Exact five-cell header, one five-cell logical row per selected case, unchanged mapped cells, no separator/extra prose/source-only columns, and no Test Details aliases. Preserve intentional source empties. |
| Text | Source wording, identifiers, numbering and item counts match after safe newline normalization; no collapsed numbered lists, truncation or remaining literal/escaped HTML breaks. |

Correct only evidenced transport/mapping defects and recheck affected files. Unresolved or unsupported content stays pending; no legacy-parameter fallback.

Preview the actual saved file content and effective Jira values under the common preview rules. Include source-selection evidence, mode, selected/omitted IDs, project/type, labels, mapping and integrity observations, all complete child/manifest or aggregate JSON, and the exact one-argument call. An optional `jira-preview.md` is a derived audit view; never reconstruct execution fields from its abbreviated display.

Obtain explicit confirmation for these current files. Existing actual confirmation can be reused only for the identical selection, mode, target and file contents. Changes require updated files, affected checks and renewed confirmation. Immediately before submission read back the aggregate or manifest/children and confirm no change; unchanged verified content does not need another full interpretation of the source.

## 5. Submit once and report the actual outcomes

Call `java-base-mcp.exportjirabydynamicfield` once with exactly:

```json
{
  "story_json_workspace_path": "<actual aggregate or multiple-mode manifest Workspace-relative path>"
}
```

Preserve the complete real response/error using the common result/recovery rules. For `multiple`, report each selected ID, child path/index and actual returned status, key/URL and error when available. If the service returns indexes, use the confirmed manifest order; never guess ticket keys or compress them into a presumed range. For `single`, report the selected ID list and actual aggregate ticket/status.

Distinguish issue creation from Test Details or other returned sub-operation outcomes. Preserve partial success and unknown outcomes; no blind replay of the whole manifest, successful child or aggregate to repair another effect. Missing item evidence stays unknown. No Story-score binding or score synchronization follows this Test Case export.
