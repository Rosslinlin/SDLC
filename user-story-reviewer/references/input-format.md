# Workspace input conversion

Read this only after selecting a genuine Workspace assessment: ticketless input, a locally authored Story or the user's selected local business revision, including continued edits after Jira export. A long Jira crawl/export automatically saved to a file remains Jira input; selecting that file, changing its presentation or finding Markdown headings does not by itself establish a local revision. Return raw Jira imports to primary-Ticket/source routing before conversion.

For a selected local revision, its mapped Ticket Key remains an export/update target. Use the actual revised Story as evidence; a template supplies structure, never missing business text. Preserve an explicit request to assess a local draft, even when it has a Ticket.

Read the current local Markdown first. If it already has the supported field/AC structure, score its actual path without making another input copy. If presentation conversion is required, preserve business wording and record the exact converted input path; that path is the later export binding path. Missing/ambiguous local content requires clarification, not a fallback to Jira's older content.

## Field contract

Use this shape. Replace all angle-bracket guidance with actual source values; omit optional fields that are absent.

```markdown
# <actual Summary value>

## Summary
<actual Summary value>

## Description
<original Description text>

## Acceptance Criteria
- **<existing first criterion number/title>**: <the complete original criterion, including all its original Given/When/Then steps>
- **<existing next criterion number/title>**: <the complete original criterion>

## <actual additional field name>
<original field value>

## <another actual field name>
<original field value>
```

Every field, including Summary and every additional field, starts with `##`. Keep Summary, Description and Acceptance Criteria in that order, then additional fields in their original order. Do not introduce an `Other Fields`, `Additional Fields` or similar wrapper. For example, source fields Scope, Dependencies and Release become `## Scope`, `## Dependencies` and `## Release` directly.

The `#` document title repeats the real Summary for compatibility with the scorer's observed H1 title extraction; it is not another field. This title/Summary repetition is the only deliberate content duplication. If Summary is absent, leave `## Summary` empty and omit the H1; do not invent a title. Keep required Description/Acceptance Criteria sections empty when their source values are missing, without adding fabricated placeholders.

Map clear aliases such as Summary/Title, Description, and AC/Acceptance Criteria/Acceptance Criteria 1/验收标准 to their canonical fields. Move clearly identified AC text out of Description once, without duplication. Several source AC fields may share the single `## Acceptance Criteria` section only when their identity and order are clear; retain their existing grouping/labels. Record original field names in the result after scoring.

Only actual field boundaries use `##`. Subheadings inside a field use `###` or deeper, so a subheading cannot accidentally close the field. Preserve their text. Do not promote an ordinary paragraph label into a new field unless the source identifies it as one.

## One complete criterion per top-level list item

The bundled SDLC Story template uses one top-level list item per AC. Apply that representation here while retaining source IDs/numbers/titles; do not manufacture AC-001 IDs.

First identify whole criteria from existing AC IDs, numbered scenario titles, explicit scenario headings, table rows representing complete criteria, or clearly separated source criteria. Keep each criterion's conditions, action, expected outcome and original And/But steps together. Consecutive Given/When/Then bullets under one explicit scenario heading belong to that one criterion; those bullets and its title are not separate ACs.

Render each identified criterion as one top-level Markdown list item. Put its existing label in bold, followed by its original text. For simple text steps, replace presentation-only line breaks/bullet markers with spaces to keep the entire scenario in that item. Preserve words, step order, capitalization, punctuation and meaningful spacing/code. Do not rewrite clauses, add missing steps, change a When into a Given, or turn ordinary prose into BDD.

Example of a presentation-only conversion:

Source:
```text
*1. Reference/Target Tab Order*
* *Given* User has clicked on Gap Analysis Button
* *When* User sees the right pane for Document Selection
* *Then* Reference Document is positioned before Target Document.
```

Scoring input:
```markdown
## Acceptance Criteria
- **1. Reference/Target Tab Order**: Given User has clicked on Gap Analysis Button When User sees the right pane for Document Selection Then Reference Document is positioned before Target Document.
```

The inserted colon is a label separator. No business words are added. A second source scenario becomes a second top-level item. For an explicitly separate criterion without an ID/title, use `- <complete original criterion>`; do not invent a label.

Keep substantive nested lists, tables and code under their own criterion using Markdown continuation indentation. Do not flatten structures if doing so loses relationships or changes meaning. Table columns labeled Given/When/Then may be rendered together per row only when the row clearly represents one criterion. Preserve original headers, cell values and order. Do not count the three columns as three ACs.

Independent requirement bullets stay independent; do not merge them just to obtain a lower AC count. If grouping is ambiguous, retain the source, explain the ambiguity and resolve it before scoring. Do not infer hidden scenarios or expected outcomes.

## Conversion and read-back checks

1. Hold a field map and original criterion count in conversation state. Count complete source criteria, not headings plus their individual BDD steps.
2. Normalize known presentation syntax: Jira wiki headings, emphasis, lists, color wrappers, paragraph breaks and links. Preserve displayed wording, identifiers and link targets. Do not globally remove asterisks or punctuation from code/data.
3. Preserve image/attachment references and filenames. Do not invent attachment URLs or describe unread images. An unresolved image-only requirement remains an evidence gap. Leave an unsupported reference intact and note the limitation in the later result.
4. If conversion is needed, write the input and read it back against the actual source. For an already suitable local Markdown input, use its complete current read-back without rewriting it. Check fields, all criterion text, original grouping/order, links and additional values. Every value occurs once, except the intentional H1/Summary repetition.
5. Count the rendered top-level AC items. They must equal the unambiguous source criterion count, including incomplete criteria such as TBC; incompleteness is scored, not repaired. Confirm one exact `## Acceptance Criteria` heading and that the next `##` starts a different field.
6. Keep the verified field map, count and presentation changes in state until the score call returns. Do not create the result file during preparation.

This is a consistent input convention based on the bundled template, not proof of the external parser's full behavior. After scoring, compare the tool's AC count and diagnostics as described in the scoring reference. Never promise that presentation normalization will fix genuine content gaps or guarantee a higher score.
