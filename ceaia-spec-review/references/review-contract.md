# Independent SPEC review contract 6.1.0-preview.7

## Inputs and bindings

The parent supplies candidate identity, mode, original source paths/locations and every current Markdown SPEC path. It also supplies the final Workspace storyJsonPath (`story.json4jira`), testCasePath, known evidence/storyJson/test/SPEC body revisions and confirmed field meanings as reference context for SPEC alignment, not as additional review targets. Updates include the saved original Description, separate AC retrieval result, complete readJiraAttachments inventory with actual returned IDs, approved changes and the SPEC plan to add reviewed current files while retaining every existing attachment, or explicit none when the user requests no remote attachment change. Read the saved evidence; do not repeat Jira discovery or intake.

Original evidence is authoritative for business facts. Inspect the relevant complete originals, directly or as full original tool-returned snapshots already supplied with actual path/currentness evidence; a shortened register or generator assertion cannot replace them. Reuse complete unchanged contents already in the review context rather than refetching them for each criterion. Missing/abbreviated/changed or uncertain content requires an actual read. Review only this candidate's assigned scope; surface omissions or contradictions across candidates for the parent's global coverage check.

A ticket used only as an Epic/Parent relationship target is not automatically a requirement source. The parent's actual key/source/type validation evidence is sufficient for that role; empty Description/AC does not create a missing-business-content finding. If explicitly also selected as a source of requirements, assess that separate business evidence without declaring the confirmed relationship invalid.

Record actual paths and observed revisions for the SPECs and any references used. Revisions supplied by the parent describe observed edits; they are not hashes or proof of unchanged content. A missing counter alone is not a content defect when current files can be fully read and identified. Use null rather than inventing a revision. If a required SPEC or reference needed to verify its alignment cannot be read, report a technical blocker and do not issue PASS for that SPEC.

## Story reference and Markdown SPEC

For alignment only, use the current Story package as a reference: `fields.summary` is the title; the root `descPath` and `acPath` identify two distinct Jira Wiki files containing Description and AC; the root `acceptanceCriteria` is the actual AC field ID, not AC prose. The JSON does not carry `fields.description` or a mapped AC body. When needed, read the referenced files or reuse complete current contents supplied by the parent. Interpret headings such as `h2.` and AC declarations such as `* *AC-001*: Given ...` as presentation around the business text. Use confirmed business custom-field values only where needed for a SPEC comparison; do not invent labels or rules for unknown IDs. Original evidence remains authoritative for business facts. Do not give the Story package a separate quality verdict.

A missing/invalid AC mapping, absent `descPath`/`acPath`, unreadable Wiki file or unsupported presentation is a parent-owned assembly or scoring issue, not another Q1-Q7 business criterion. If it prevents verifying a SPEC's projection against the Story reference, report the precise technical blocker and mark that SPEC's affected criterion NOT_ASSESSED. Do not treat the mapping ID or a path string as acceptance text. Do not independently validate JSON field schema, Reporter, tool arguments or Wiki-file quality; those checks belong to generation and scoring.

Review every related SPEC's readable Markdown projection, requirements, flows and embedded tests against original evidence and, where needed for alignment, the current Story package and standalone Test Case. Story business changes automatically require affected local SPEC/test synchronization; the parent owns standalone Test Case correction. Do not waive affected SPEC review because the user mentioned only Story or selected no remote attachment change. An actual stale SPEC requirement, projection or embedded test is a Q6 consistency defect with its concrete location and impact. A routing/schema-only change or already consistent content needs verification, not a cosmetic rewrite. Preserve actors, rules, AC/scenario outcomes, scope and relevant business custom-field values; Jira wiki and Markdown presentation differ. Do not demand byte-for-byte raw JSON copies or a standalone Markdown Story. Routing, identity, project/type/field IDs and other internal transport metadata do not belong in the SPEC business projection. A custom-field ID is transport metadata; its source-supported business value may still require coverage.

All current SPECs remain review targets, including a score-only SPEC. A score-only SPEC is a required input to the parent's scoring stage; that does not require it to be listed for upload. Attachment declarations follow the confirmed JSON schema and authorized scope, not a reviewer-invented rule to attach the scoring file. Do not infer that a local SPEC was or will be uploaded merely because it exists.

SPEC review metadata consists of Result and Reviewer Notes. No Story Score is required in SPEC; real score output belongs in a separate parent-owned report. Header-only changes do not by themselves fail Q1-Q7; the parent handles resulting scoring/currentness work. An edited Story JSON, either referenced Wiki file, SPEC or Test Case business body requires affected SPEC verification even when its path is unchanged.

## Findings and verdicts

Use these classifications:

- CONTENT_BLOCKER: an evidenced omission, contradiction, unsupported promise or inability to understand/verify required behavior. The parent can repair from existing facts.
- CLARIFICATION: a specific missing/conflicting business fact that changes scope or acceptance. State the exact decision needed without guessing it.
- TECHNICAL_BLOCKER: required evidence/current content cannot be read or safely associated. Request technical recovery, not an invented business decision.
- ADVISORY: a useful improvement when the content already meets the standard. It never prevents PASS.

For each finding identify the criterion, actual SPEC/item, source evidence, observed defect, practical impact and smallest supported correction. When a SPEC discrepancy appears to originate in a Story or Test Case reference, identify that dependency for the parent without issuing a separate verdict on it. A SPEC-only defect does not justify rewriting correct Story content. Confirm the issue is not already resolved elsewhere; do not duplicate one defect under several criteria. Keep short current quotations only when they locate the problem.

Do not fail a SPEC for filename style, table-column order, the row holding a negative label, use of the word "or", a stale review header or optional unknown metadata. Template/layout and transport-schema checks belong to the parent's generation precheck. Malformed/unreadable reference material needed for SPEC alignment is a technical blocker, not a new business requirement. A broken SPEC reference, misleading attachment claim or inconsistent SPEC business content is still a real defect and must identify its impact. Do not invent mandatory scenarios, per-AC negative companions, SLAs, endpoints, logs or implementation details to satisfy a checklist.

Criteria results are PASS, FAIL, NOT_ASSESSED or N/A. FAIL requires a supported content blocker or material clarification. NOT_ASSESSED identifies missing technical access or incomplete review coverage. Q7 is N/A for a new Story; applicable content criteria are not waived because evidence is inconvenient. Each result needs concise actual evidence or the precise missing item.

- PASS: every applicable Q1-Q7 criterion passes for the SPECs, every current SPEC has been read and bound, and no blocking SPEC finding remains. Advisories may remain. This is not a Story package PASS.
- NEEDS_HUMAN_CLARIFICATION: at least one material business fact/decision is missing or conflicting. Preserve known repair and technical findings too.
- NEEDS_REVISION: supported content correction, technical recovery or incomplete review coverage remains, without a missing business decision taking precedence.
- FINAL_WITH_UNRESOLVED: the user explicitly stopped before PASS; identify unresolved items. It does not authorize scoring or export.

## Update preservation

Review each update target's SPECs separately against the parent's confirmed ticket, baseline and approved delta. Target field validation belongs to the parent; do not infer a different ticket from filenames or prefixes. A usable baseline Description with an empty/unavailable separate AC field is not itself a SPEC defect. Use embedded criteria where evidenced and record the available baseline honestly. The completed attachment inventory, including zero attachments, distinguishes relevant evidence from irrelevant files; an unread irrelevant attachment alone is not a content blocker.

Preserve original obligations unless a specific approved change supersedes them. Check the delta against the baseline, including all related SPECs. The SDLC update includes synchronized SPEC delivery without separate "update SPEC" wording; final confirmation still covers exact remote operations. By default append the reviewed current SPECs and retain every existing attachment. Inherited broad scope alone does not require new tickets; review does not authorize unrelated fields, renamed Summary or arbitrary attachment operations.

For material original SPEC evidence, inspect complete content and its actual source/ticket and inventory binding. Establish its relevance from content and purpose. Distinct business obligations must be represented in the reviewed outputs. Incomplete material evidence requires resolution before a preservation PASS. A missing old attachment ID alone is not an upload blocker when the baseline is established. Use the existing Q1/Q6/Q7 criteria.

The parent validates the root `attachmentsJson.add` upload entries and exact output paths and filenames, or explicit none. Original IDs remain preservation evidence. The reviewer uses the approved plan and relevant original SPEC content to assess baseline preservation and reports a contradictory plan to the parent without adding an independent transport-schema verdict.

For explicit none, remote attachments stay unchanged while every affected local SPEC is synchronized and read. For add, check that each reviewed current SPEC preserves the applicable baseline obligations and existing attachments are retained. Each current SPEC gets its own preservation conclusion. Resolve material unread or ambiguous evidence with the parent. Unreconciled baseline changes block preservation PASS.

## Return

Use the report template. Include every current SPEC in reviewedSpecArtifacts with its path, observed bodyRevision, actual contentReadBack, result and preservation conclusion. Bind JSON/Test Case/evidence only as references in reviewedBindings; no Wiki path/revision or Story quality verdict is part of this report. No score placeholder, readiness flag, header-sync object, repair counter or title-validation compatibility field is part of this report. The parent owns those workflow decisions.
