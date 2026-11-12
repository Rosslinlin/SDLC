# Prepare the selected local Story as JSON

Read this only for a ticketless Story, locally authored content or the user's selected local business revision. An original Jira dump remains Jira input even when saved to Workspace; identify its primary ticket/source before conversion. A local revision with an existing target remains JSON input when that is the version the user selected.

## Reuse before converting

Read the complete selected content, not a truncated preview. Reuse an existing trustworthy `.json4jira` and its established path when it already represents the requested revision; do not create a second copy or query merely because this Skill did not author it. Resolve genuinely missing routing/schema facts through [JSON assembly and profiles](jira-json-assembly.md). A ticket referenced as evidence is not an update target.

For raw text or Markdown, prepare one final JSON using the [new Story template](../templates/story-json-template.json), or the [local update template](../templates/story-update-json-template.json) only for a confirmed existing target. Remove template guidance and preserve source meaning. This preparation never exports to Jira, generates SPEC/Test Case files or improves business wording to obtain a score.

## Source-preserving field mapping

- Map actual Summary/Title to `fields.summary` once. The old H1/Summary repetition is unnecessary.
- Put non-AC Description and supported additional business prose in `fields.description`, in original order. Preserve named sections such as Scope or Dependencies when no actual Jira field mapping is supplied. Do not silently turn an opaque schema-bound option/ID into prose; resolve an actual field-mapping ambiguity.
- Put the actual AC field-ID string in root `acceptanceCriteria`, and the entire AC text in `fields[acceptanceCriteria]`. No `customFields` wrapper or top-level AC content array. Move clearly identified AC text out of Description once; retain grouping and references elsewhere.
- Map additional actual Jira fields using their real metadata-confirmed IDs, types and option representations. Omit unknown optional technical values. Do not invent project/type/field IDs or turn query/profile identity into root `staffId` or `fields.reporter`.
- A new Story omits `issueIdOrKey`. A selected local update has the exact confirmed target key there, after `issueType`; do not infer it from Parent/Epic/references or a saved profile. A known target does not switch the assessment back to Jira's older content.
- Missing business values, TBC and incomplete criteria are assessment subjects, not permission to fill gaps. Preserve absence using only empty/omitted values the actual JSON contract allows. If the service rejects that structure, report its validation failure rather than fabricate a title, goal, value, AC or expected behavior.

Keep a compact field map, actual criteria count, source provenance, conversion changes and known target in current context until scoring returns. No separate source copy or manifest is required.

## Creation handoff check

Use this check only when this reviewer is loaded to assess a complete Story draft authored by a creation workflow in the same task. The authored Story package is the source; the `.json4jira` is its scoring carrier. A structurally valid JSON is not a complete, trustworthy representation merely because its Summary and ACs match.

Before scoring each Story, compare its title, Story statement, business context, preconditions, AC identifiers/count/order/full text, BDD scenarios, scope, assumptions, dependencies, exclusions, deferred candidates, open questions, notes and other supplied business sections with the candidate JSON. Compare meaning after the required Jira wiki conversion, not cosmetic Markdown. Keep review findings, scores, loaded-skill lists and other workflow reporting outside the Story-content comparison. Preserve authored non-AC sections as named sections in `fields.description` when no actual Jira field mapping exists; retain ACs only in their mapped AC field, without duplicating them as BDD. Do not invent or rewrite business content to make the two versions agree.

If the candidate JSON omits or materially changes authored Story content, complete the source-preserving mapping at its established path and read it back before calling scoreUserStory. After a supported revision, assess the current JSON. Before claiming that a score applies to the final displayed Story, compare its business content with that assessed JSON again. If they differ materially, prepare and assess the changed content as a new revision; if the complete draft or final display cannot be checked, state precisely that the score applies only to the selected JSON and identify any known unassessed sections. Do not rerun scoring merely to improve a low score. A standalone JSON-only request has no unseen draft to infer or reconstruct.

## Whole criteria, original labels

Identify criteria from actual IDs/numbers/titles, scenario headings, complete table rows or clearly separate source statements. Preserve each criterion's full conditions/action/results and original And/But text together. Given/When/Then bullets under one scenario are one criterion, not three. Do not add missing steps, change a When to Given, merge independent criteria to lower the count or invent AC-001 labels.

Render each complete criterion as one Jira wiki list item. Use its existing label in Jira bold when available; an unlabeled criterion remains an unlabeled item. Preserve substantive nested lists, tables and code within their original relationships. Presentation-only line breaks may be combined only when wording, ordering and meaning survive. Ambiguous grouping needs a focused clarification; do not guess.

Example, only when this numbered criterion and all three steps actually exist:

```text
* *1. Reference/Target Tab Order*: Given User has clicked on Gap Analysis Button When User sees the right pane for Document Selection Then Reference Document is positioned before Target Document.
```

No label or business words from this example are defaults. An incomplete criterion remains incomplete. Count each identified source criterion once, including TBC or incomplete criteria; count the whole group rather than its heading plus individual BDD steps. Code samples and references to IDs are not additional criteria.

## Jira-visible text before scoring

The saved JSON already contains final Jira wiki presentation; there is no later export-time conversion. Convert recognized authored Markdown once, outside code/literal examples:

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

Read the saved JSON and decode its field strings once. Check the actual root mapping and corresponding field value, every source field/criterion, original grouping/order, links, code and additional values. Each business value occurs once in its authoritative field; no H1/Summary duplication is needed. Newlines must be real decoded line breaks, not literal backslash-n or HTML break tags; serialize one JSON object, not a quoted inner request.

Preserve supplied image/attachment references and filenames. Do not invent URLs, describe unseen images or insert hypothetical upload paths. The JSON `attachments` list contains only actual intended file paths already supplied/authorized for that file; otherwise it is empty. A SPEC used only for assessment is not automatically an upload. This Skill does not create a SPEC or decide attachment replacement/deletion.

Keep the selected `.json4jira` path byte-for-byte for [scoring](scoring-and-results.md) and any later handoff. Do not create a result file before a score invocation. After scoring compare service AC diagnostics with the verified source criteria; presentation conversion never guarantees a higher score. Do not mutate scored JSON/SPEC to display a score or to prepare export.
