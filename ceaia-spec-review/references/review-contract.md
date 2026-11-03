# Independent review contract 6.1.0-preview.5

## Inputs and bindings

The parent supplies candidate identity, mode, original source paths/locations, the final Workspace storyJsonPath (`story.json4jira`), testCasePath and every current Markdown SPEC path. It supplies known evidence/storyJson/test/SPEC body revisions and confirmed schema/field meanings from its earlier Jira preparation. Updates include the saved original Description, separate AC retrieval result, attachment inventory, approved changes and resolved attachmentAction=none or replace with selected original-to-output mappings. Read the saved evidence; do not repeat Jira discovery or intake.

Original evidence is authoritative for business facts. Read the relevant originals rather than only a shortened register. Missing business facts cannot be replaced by generator assertions. Review only this candidate's assigned scope; surface omissions or contradictions across candidates for the parent's global coverage check.

Record actual paths and observed revisions. Revisions supplied by the parent describe observed edits; they are not hashes or proof of unchanged content. A missing counter alone is not a content defect when current files can be fully read and identified. Use null rather than inventing a revision. If current association or required content cannot be read, report a technical blocker and do not issue PASS.

## JSON business content and Markdown projection

Read fields.summary and the Jira wiki string fields.description from the current JSON. Resolve the nonempty field-ID string in root acceptanceCriteria, then read the actual AC Jira wiki string from fields[that ID]. There is no customFields wrapper. Interpret headings such as `h2.` and AC declarations such as `* *AC-001*: Given ...` as presentation around the business text; keep the stable AC ID and its conditions/actions/outcomes. The mapping ID is configuration, never AC text. Read other confirmed custom-field business values from fields[actual field ID], interpreting them through the parent's confirmed metadata; do not invent labels or rules for unknown IDs. The JSON is the current authoritative business object, but original evidence still determines whether its claims are correct.

A missing/invalid AC mapping, a mapping that points to an absent field, or an empty/non-string mapped AC value is a technical structure problem. Unconverted authored Markdown presentation outside code/literal examples in Jira-bound rich text returns to the parent's assembly precheck for presentation repair; it does not add a Q1-Q7 business criterion. Preserve code/literal contents and already valid Jira wiki. Identify the exact JSON location; do not treat the mapping ID as evidence of covered acceptance. Assess the actual AC conditions/actions/outcomes once readable. Do not require root staffId, fields.reporter, customFields, a retired top-level AC content array or bddScenarios, an independent BDD object, or duplicate scenario text merely to match the former structure. Root acceptanceCriteria is the required field-ID mapping, not AC content.

Compare each SPEC's readable Markdown projection with the JSON business meaning, requirements and tests. Preserve actors, rules, AC/scenario outcomes, scope and relevant business custom-field values; Jira wiki and Markdown presentation differ. Do not demand byte-for-byte raw JSON copies or a standalone Markdown Story. Routing, identity, project/type/field IDs and other internal transport metadata do not belong in the SPEC business projection. A custom-field ID is transport metadata; its source-supported business value may still require coverage.

All current SPECs remain review targets, including a score-only SPEC. A score-only SPEC is a required input to the parent's scoring stage; that does not require it to be listed for upload. Attachment declarations follow the confirmed JSON schema and authorized scope, not a reviewer-invented rule to attach the scoring file. Do not infer that a local SPEC was or will be uploaded merely because it exists.

SPEC review metadata consists of Result and Reviewer Notes. No Story Score is required in SPEC; real score output belongs in a separate parent-owned report. Header-only changes do not by themselves fail Q1-Q7; the parent handles resulting scoring/currentness work. An edited JSON/SPEC/test business body requires affected independent content verification even when the front end preserves its path.

## Findings and verdicts

Use these classifications:

- CONTENT_BLOCKER: an evidenced omission, contradiction, unsupported promise or inability to understand/verify required behavior. The parent can repair from existing facts.
- CLARIFICATION: a specific missing/conflicting business fact that changes scope or acceptance. State the exact decision needed without guessing it.
- TECHNICAL_BLOCKER: required evidence/current content cannot be read or safely associated. Request technical recovery, not an invented business decision.
- ADVISORY: a useful improvement when the content already meets the standard. It never prevents PASS.

For each finding identify the criterion, actual file/item, source evidence, observed defect, practical impact and smallest supported correction. Mark whether JSON business content must change. A SPEC-only or test-only defect does not justify rewriting correct JSON. Confirm the issue is not already resolved elsewhere; do not duplicate one defect under several criteria. Keep short current quotations only when they locate the problem.

Do not fail content for filename style, table-column order, the row holding a negative label, use of the word "or", a stale review header or optional unknown metadata. Template/layout and transport-schema checks belong to the parent's generation precheck. Malformed/unreadable JSON or an unavailable material field mapping is a technical blocker, not a new business requirement. A broken reference, misleading attachment claim or inconsistent business content is still a real defect and must identify its impact. Do not invent mandatory scenarios, per-AC negative companions, SLAs, endpoints, logs or implementation details to satisfy a checklist.

Criteria results are PASS, FAIL, NOT_ASSESSED or N/A. FAIL requires a supported content blocker or material clarification. NOT_ASSESSED identifies missing technical access or incomplete review coverage. Q7 is N/A for a new Story; applicable content criteria are not waived because evidence is inconvenient. Each result needs concise actual evidence or the precise missing item.

- PASS: every applicable Q1-Q7 criterion passes, every current SPEC has been read and bound, and no blocking finding remains. Advisories may remain.
- NEEDS_HUMAN_CLARIFICATION: at least one material business fact/decision is missing or conflicting. Preserve known repair and technical findings too.
- NEEDS_REVISION: supported content correction, technical recovery or incomplete review coverage remains, without a missing business decision taking precedence.
- FINAL_WITH_UNRESOLVED: the user explicitly stopped before PASS; identify unresolved items. It does not authorize scoring or export.

## Update preservation

Review each update target separately. The user-confirmed root issueIdOrKey (capital I in Id), after issueType, must name the exact nonempty existing ticket selected in the parent's baseline; create JSON omits it. The ticket identity must agree with source and approved delta, without inferring it from filenames or prefixes. A usable Description with an empty/unavailable separate AC field is not itself a defect. Use embedded criteria and record the available baseline honestly. The completed attachment inventory, including zero attachments, distinguishes relevant evidence from irrelevant files; an unread irrelevant attachment alone is not a content blocker.

Preserve original obligations unless a specific approved change supersedes them. Check the delta against the baseline, including supplied relevant attachments. Inherited broad scope alone does not require new tickets. Do not authorize new fields, renamed Summary or expanded attachment operations as a side effect of review.

For none, there is no replacement mapping or claim of a new upload: attachments=[] preserves remote attachments while the local current SPEC is read and sent through the scorer's confirmed specFilePath argument. Do not demand an extra capability proof merely because that SPEC is not uploaded. For replace, use the selected returned attachment IDs and the parent's output mapping; do not choose among ambiguous originals or merge distinct replacements. Each current SPEC gets its own preserved-obligations conclusion, even when JSON business content/tests are shared. A missing business selection is a clarification; an absent/mismatched issueIdOrKey, unsupported replacement encoding or an actual backend rejection is a specific technical issue for the parent. Basic update targeting through issueIdOrKey is confirmed and is not an unknown-schema blocker. Never invent an alternate issue-key/attachment operation field or create fallback to make the JSON appear executable. Unreconciled known baseline changes block preservation PASS. Add/delete requests go back to the parent for a scope decision; this review does not expand the none/replace update route.

## Return

Use the report template. Include every current SPEC in reviewedSpecArtifacts with its path, observed bodyRevision, actual contentReadBack, result and preservation conclusion. Bind the JSON/Test Case/evidence in reviewedBindings. No score placeholder, readiness flag, header-sync object, repair counter or title-validation compatibility field is part of this report. The parent owns those workflow decisions.
