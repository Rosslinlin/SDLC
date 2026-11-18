# Prepare the selected local Story as JSON

Read this only for a ticketless Story, locally authored content or the user's selected local business revision. An original Jira dump remains Jira input even when saved to Workspace; identify its primary ticket/source before conversion. A local revision with an existing target remains JSON input when that is the version the user selected.

## Reuse before converting

Read the complete selected content, not a truncated preview. Reuse an existing trustworthy `.json4jira` and its established path when it already represents the requested revision, reading its two referenced wiki files when it uses root `descPath`/`acPath`; do not create a second copy or query merely because this Skill did not author it. A previously scored inline Story remains bound to its original bytes and can use the existing route where the actual backend still supports it; never silently migrate or overwrite that input. Resolve genuinely missing routing/schema facts through [JSON assembly and profiles](jira-json-assembly.md). A ticket referenced as evidence is not an update target.

For raw text or Markdown, prepare one final JSON and two distinct `.jirawiki` files using the [new Story template](../templates/story-json-template.json), or the [local update template](../templates/story-update-json-template.json) only for a confirmed existing target. Save all three under the selected candidate's session Workspace folder, replace template guidance and preserve source meaning. This preparation never exports to Jira, generates SPEC/Test Case files or improves business wording to obtain a score.

## Source-preserving field mapping

- Map actual Summary/Title to `fields.summary` once. The old H1/Summary repetition is unnecessary.
- Put the complete authored non-AC Story body in the Jira wiki file referenced by root `descPath`, with its original section names and order. This includes the Story statement, Business Context, Pre-conditions, separately presented BDD/Scenarios, Scope, assumptions, dependencies, exclusions, open questions and other business prose. Preserve every paragraph, list item and table row/cell; do not collapse a section into a summary. Do not silently turn an opaque schema-bound option/ID into prose; resolve an actual field-mapping ambiguity.
- Put the actual AC field-ID string in root `acceptanceCriteria`, and the complete authored Acceptance Criteria section in the separate Jira wiki file referenced by root `acPath`, including any BDD steps that belong to those criteria. Omit `fields.description` and `fields[acceptanceCriteria]` from newly prepared path-based JSON. No `customFields` wrapper or top-level AC content array. A separately authored BDD section remains in the Description file even when it refers to the same scenarios as the ACs; preserve source repetition across the two named sections instead of deleting one.
- Map additional actual Jira fields using their real metadata-confirmed IDs, types and option representations. Omit unknown optional technical values. Do not invent project/type/field IDs or turn query/profile identity into root `staffId` or `fields.reporter`.
- A new Story omits `issueIdOrKey`. A selected local update has the exact confirmed target key there, after `issueType`; do not infer it from Parent/Epic/references or a saved profile. A known target does not switch the assessment back to Jira's older content.
- Missing business values, TBC and incomplete criteria are assessment subjects, not permission to fill gaps. Preserve absence using only empty/omitted values the actual JSON contract allows. If the service rejects that structure, report its validation failure rather than fabricate a title, goal, value, AC or expected behavior.

Keep a compact field map, actual criteria count, source provenance, conversion changes and known target in current context until scoring returns. No separate source copy or manifest is required.

## Creation handoff check

Use this check only when this reviewer is loaded to assess a complete Story draft authored by a creation workflow in the same task. The authored Story package is the source; the `.json4jira` is its scoring carrier. A structurally valid JSON is not a complete, trustworthy representation merely because its Summary and ACs match.

Before scoring each Story, make an in-context inventory of its title, Story statement, Business Context, Pre-conditions, ACs, BDD scenarios, scope, assumptions, dependencies, exclusions, deferred candidates, open questions, notes and other supplied business sections. For every section, record each distinct paragraph, list item, table row/cell and criterion, including its label and order; then read back the saved JSON and both referenced wiki files. Match every non-AC body item to the `descPath` file and every AC item to the `acPath` file; finding an item only in the other file does not pass this field-specific check. For an existing supported inline JSON, check its two actual inline field strings instead. Check section presence, item counts, wording and order in both directions. The gate passes only with zero missing, changed, misplaced or unapproved extra business items. A combined summary does not replace separate authored preconditions or other individually stated conditions. A separately displayed BDD section must appear in Description; BDD steps authored inside ACs must appear in AC. If the authored Story repeats them in both sections, preserve both occurrences. Allow only presentation changes required for Jira wiki; do not accept omitted or changed business facts as a merely cosmetic or immaterial difference. Keep review findings, scores, loaded-skill lists and other workflow reporting outside the Story-content comparison. Do not invent or rewrite business content to make the two versions agree. Keep the inventory in context rather than creating another file.

If any authored item is missing, changed or in the wrong Description/AC file in a newly prepared package, correct the source-preserving mapping at the established paths, save and read it back, then repeat the field-specific check before calling scoreUserStory. An edit to a previously assessed JSON or wiki file requires a new assessment.

After scoring, build the displayed Story from the assessed JSON's Summary and its two referenced wiki files, or from the actual inline fields of a supported legacy input. Before delivering the package or attributing its score, compare the complete display with that same assessed content, including every Business Context, Pre-conditions and separately presented BDD item. Fix display-only discrepancies by rendering the display again from the assessed files, with no input write or new score. For an authorized Story revision or a correction to an actual input file, prepare and read back that revision, then obtain a current assessment before presenting it as scored or [handing it to export](jira-score-sync.md#return-the-scored-json-without-changing-it). If the complete draft or final display cannot be checked, state precisely which selected inputs were assessed and identify any known unassessed sections. A standalone JSON-only request has no unseen draft to infer or reconstruct.

## Whole criteria, original labels

Identify criteria from actual IDs/numbers/titles, scenario headings, complete table rows or clearly separate source statements. Preserve each criterion's full conditions/action/results and original And/But text together. Given/When/Then bullets under one scenario are one criterion, not three. Do not add missing steps, change a When to Given, merge independent criteria to lower the count or invent AC-001 labels.

Render each complete criterion as one Jira wiki list item. Use its existing label in Jira bold when available; an unlabeled criterion remains an unlabeled item. Preserve substantive nested lists, tables and code within their original relationships. Presentation-only line breaks may be combined only when wording, ordering and meaning survive. Ambiguous grouping needs a focused clarification; do not guess.

Example, only when this numbered criterion and all three steps actually exist:

```text
* *1. Reference/Target Tab Order*: Given User has clicked on Gap Analysis Button When User sees the right pane for Document Selection Then Reference Document is positioned before Target Document.
```

No label or business words from this example are defaults. An incomplete criterion remains incomplete. Count each identified source criterion once, including TBC or incomplete criteria; count the whole group rather than its heading plus individual BDD steps. Code samples and references to IDs are not additional criteria.

## Jira-visible text before scoring

For newly prepared path-based Stories, the two saved `.jirawiki` files contain final Jira wiki presentation; the JSON contains their paths and there is no later export-time conversion. A supported existing inline JSON already holds its final Jira wiki strings. Convert recognized authored Markdown once, outside code/literal examples:

| Source presentation | Jira wiki string |
| --- | --- |
| `#` through `######` headings | `h1.` through `h6.` |
| `**bold**`, Markdown italics | `*bold*`, `_italic_` |
| Unordered / ordered lists | `*` / `#`; preserve nested and mixed levels |
| Fenced / inline code | `{code}` block / `{{inline code}}`; exact body |
| `[label](https://example)` | `[label\|https://example]` |
| Block quote | `bq.` line |
| Markdown table | Jira double-pipe header and single-pipe body; remove only the separator row |

Keep plain text, dates, decimals, identifiers, link targets, actual newlines and blank lines. Do not apply rich-text conversion to Summary, routing, option values, labels, numbers, URLs, attachment paths or opaque custom strings. Existing valid Jira wiki is idempotent: identify its format and do not convert `*bold*` to italics or duplicate headings/list markers. Preserve code and literal Markdown examples exactly. Unsupported presentation needs a focused mechanical correction, never omitted or paraphrased content.

## Read back before scoring

Read the saved JSON and its two distinct, nonempty `.jirawiki` files once after each source-preserving correction. Check that root `descPath` and `acPath` are different session Workspace-relative paths without traversal, match the actual files, and that `fields.description` and the mapped AC field are absent in a new path-based package. Check every source section/criterion in its assigned file, original grouping/order, links, code and additional values. For a supported existing inline JSON, check its actual mapped field strings without silently rewriting it. Preserve a business item in each section where the authored Story actually contains it; do not invent a second occurrence or deduplicate an authored repeated section. No H1/Summary duplication is needed. Newlines in wiki files must be actual line breaks, not literal backslash-n or HTML break tags; serialize one JSON object, not a quoted inner request.

Preserve supplied image/attachment references and filenames. Do not invent URLs, describe unseen images or insert hypothetical upload paths. The JSON `attachments` list contains only actual intended file paths already supplied/authorized for that file; otherwise it is empty. A SPEC used only for assessment is not automatically an upload. This Skill does not create a SPEC or decide attachment replacement/deletion.

Keep the selected `.json4jira` path byte-for-byte for [scoring](scoring-and-results.md) and any later handoff, with both referenced wiki paths and their observed file revisions. Do not create a result file before a score invocation. After scoring compare service AC diagnostics with the verified source criteria; presentation conversion never guarantees a higher score. Do not mutate scored JSON, either wiki file or SPEC to display a score or to prepare export.
