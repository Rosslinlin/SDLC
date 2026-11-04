# Assemble native issue fields from actual source

Read only while preparing or intentionally revising a file. Reuse complete trustworthy upstream JSON unchanged under [the file contract](common-json4jira-contract.md). Use [profiles](common-state-and-user-info.md) and [discovery](common-project-issue-type-validation.md) for missing metadata. [Test Case source export](flow-testcase-source-export.md) owns its field/step/table shapes and overrides general content-generation rules.

## Content and value precedence

Preserve supplied export-ready wording, order, identifiers, criterion grouping, tables, examples, links and approved replacements. Map clearly identified source fields once. Non-field business sections remain faithfully named in Description; do not discard extra requirements or flatten an opaque schema-bound value into prose to hide an unresolved mapping. Never replace actual content with a source/preview/attachment pointer.

A raw request explicitly asking to generate an issue permits content derived from its supplied facts; mark generated values in preview and obtain confirmation. Exporting an authored source does not authorize polishing, invented actors/benefits/ACs or regeneration to improve scores. Business gaps remain honest assessment subjects where permitted; unresolved required export content needs clarification rather than fabrication.

Resolve values from current explicit input and the selected revision first. On update preserve unchanged target values or omit unrequested fields as supported. Only then consider applicable confirmed preferences or evidenced defaults. Create defaults never overwrite target values. Omit optional fields without a requested/required value; do not add them merely because they seem relevant.

## Native fields and types

Put Jira-visible values directly in native `fields` using actual field IDs. Do not assemble `dynamicFieldsJson`, a `customFields` wrapper or old direct tool arguments. Root routing/type and the update target follow the file contract and agree with included field values.

| Actual schema | Representation rule |
| --- | --- |
| Text | Complete source or approved generated JSON string; rich text uses [preparation rendering](common-jira-text-rendering.md). |
| Number | A valid JSON number, without invented defaults or quoted numeric substitutes. |
| Date | Supported encoding such as YYYY-MM-DD when applicable; not today's date as a guessed business date. |
| Select/user/project/version reference | Actual supported ID/object/value representation from metadata or verified facts; never fabricated hidden IDs. |
| Multi-value field or labels | Native array of the supported element type, preserving requested content/order where meaningful; no JSON-encoded string. |

Select allowed values by actual source/user match or applicable evidenced default, never `allowedValues[0]` or an arbitrary enabled option. Distinguish ID from display label and user name from account identity. Unsupported encodings or unresolved required selections need a focused clarification or precise tool limitation.

Preserve intentional source empties only where supported. Omit unused optional fields. Do not use `"."`, `"N/A"`, string `"null"` or whitespace as missing-value substitutes. Keep collections typed and use JSON null only when supported. Do not globally replace punctuation in real content.

An update omits unrelated fields; empty does not mean unchanged. A clear needs explicit intent, supported encoding and a preview identifying the clear operation. Every batch child owns its effective values; manifest/default inheritance must not change unintended fields.

## Story-specific mapping

- Root `acceptanceCriteria` is the actual AC field-ID string; complete AC text is at `fields[that ID]`. No root AC content array, `customFields` mapping or independent `bddScenarios` object.
- Map real Title/Summary to `fields.summary` once. Preserve non-AC Description and additional sections in `fields.description` in original order. Do not duplicate Title as H1 or repeat the extracted AC body in Description.
- Preserve whole criteria, existing labels/IDs and Given/When/Then/And/But relationships. BDD steps within one scenario remain one criterion. Do not invent AC-001 labels, merge criteria or complete missing steps to raise a score.
- Create omits `issueIdOrKey`; update uses the exact confirmed actual target at the root. Parent/Epic/reference keys or profile history never select it.
- Omit root `staffId` and Story `fields.reporter`. Platform/profile identity is not business payload. Do not generate SPEC/Test Case files or add attachments solely to satisfy scoring.

Other types use only applicable actual fields. Do not add Story AC/BDD/SPEC obligations to Bug/Task/Test/Epic or misclassify the actual type to put a Story in a non-Story manifest. Use [assessment evidence](story-assessment-evidence.md) when supplied or actually required; this helper neither imposes universal Story scoring nor calls the scorer.

## Labels, attachments and relationships

Preserve requested labels and unrelated current labels on updates. Test Case specifically requires native `fields.labels` containing `CEAIA_GEN`; the old blanket caller-omission rule must not apply. Other marker behavior follows the confirmed file contract, not a guessed backend assumption.

Paths/attachment operations and dedicated relationship controls follow [attachments](common-attachments.md), [associations](flow-associate-issue.md) and the file contract, not arbitrary custom fields. Query/profile facts never authorize adding or clearing them. A score-only SPEC path is not an upload instruction.

## Save once and preserve assessment provenance

Complete text rendering, field mapping and authorized upload selection before saving. Read back and decode native strings once; verify actual IDs/types, source-to-field mapping, whole-criterion counts, original order, newlines, links and code. Required values remain real, supported or precisely unresolved.

Keep the exact path/content for any requested or upstream assessment and submission. A user-authorized change to previously assessed content makes that prior score historical and requires a fresh preview/confirmation; never represent it as a score of the changed file. A new assessment is needed only where the actual project/backend/user requires it and remains with the reviewer/upstream caller. No submission-time mutation or reserialization is permitted. Retain conversion/source mapping in existing preview/context, not another manifest or source-copy file.
