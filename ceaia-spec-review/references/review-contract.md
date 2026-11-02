# Independent review contract v6

## Inputs and bindings

The parent supplies candidate identity, mode, original source paths/locations, current Story/Test Case paths and every current SPEC path. It also supplies known evidence/story/test/SPEC body revisions. Updates include the saved original Description, separate AC retrieval result, attachment inventory, approved changes and resolved attachmentAction=none or replace with any selected original-to-output mapping. Read the saved evidence; no routine fresh Jira read is needed.

Original evidence is authoritative for business facts. Read the relevant originals rather than only a shortened register. Missing business facts cannot be replaced by generator assertions. Review only this candidate's assigned scope; surface omissions or contradictions across candidates for the parent's global coverage check.

Record actual paths and observed revisions. Revisions supplied by the parent describe observed edits; they are not hashes or proof of unchanged content. A missing counter alone is not a content defect when current files can be fully read and identified. Use null rather than inventing a revision. If current association or required content cannot be read, report a technical blocker and do not issue PASS.

## Findings and verdicts

Use these classifications:

- CONTENT_BLOCKER: an evidenced omission, contradiction, unsupported promise or inability to understand/verify required behavior. The parent can repair from existing facts.
- CLARIFICATION: a specific missing/conflicting business fact that changes scope or acceptance. State the exact decision needed without guessing it.
- TECHNICAL_BLOCKER: required evidence/current content cannot be read or safely associated. Request technical recovery, not an invented business decision.
- ADVISORY: a useful improvement when the content already meets the standard. It never prevents PASS.

For each finding identify the criterion, actual file/item, source evidence, observed defect, practical impact and smallest supported correction. Mark whether Story content must change. A SPEC-only or test-only defect does not justify rewriting a correct Story. Confirm the issue is not already resolved elsewhere; do not duplicate one defect under several criteria. Keep short current quotations only when they locate the problem.

Do not fail content for filename style, table-column order, the row holding a negative label, use of the word "or", a stale review header or optional unknown metadata. Template/layout checks belong to the parent's generation precheck. A broken reference, misleading attachment claim or inconsistent business content is still a real defect and must identify its impact. Do not invent mandatory scenarios, per-AC negative companions, SLAs, endpoints, logs or implementation details to satisfy a checklist.

Criteria results are PASS, FAIL, NOT_ASSESSED or N/A. FAIL requires a supported content blocker or material clarification. NOT_ASSESSED identifies missing technical access or incomplete review coverage. Q7 is N/A for a new Story; applicable content criteria are not waived because evidence is inconvenient. Each result needs concise actual evidence or the precise missing item.

- PASS: every applicable Q1-Q7 criterion passes, every current SPEC has been read and bound, and no blocking finding remains. Advisories may remain.
- NEEDS_HUMAN_CLARIFICATION: at least one material business fact/decision is missing or conflicting. Preserve known repair and technical findings too.
- NEEDS_REVISION: supported content correction, technical recovery or incomplete review coverage remains, without a missing business decision taking precedence.
- FINAL_WITH_UNRESOLVED: the user explicitly stopped before PASS; identify unresolved items. It does not authorize scoring or export.

## Update preservation

Review each update target separately. A usable Description with an empty/unavailable separate AC field is not itself a defect. Use embedded criteria and record the available baseline honestly. The completed attachment inventory, including zero attachments, distinguishes relevant evidence from irrelevant files; an unread irrelevant attachment alone is not a content blocker.

Preserve original obligations unless a specific approved change supersedes them. Check the delta against the baseline, including supplied relevant attachments. Inherited broad scope alone does not require new tickets. Do not authorize new fields, renamed Summary or expanded attachment operations as a side effect of review.

For none, there is no replacement mapping or claim of a new upload; still read the local current SPEC. For replace, use the selected returned attachment IDs and the parent's reviewed output mapping; do not choose among ambiguous originals or merge distinct replacements. Each current SPEC gets its own preserved-obligations conclusion, even when Story/tests are shared. A missing material selection is a clarification. Unreconciled known baseline changes block preservation PASS. Add/delete requests go back to the parent for a scope decision; this review does not expand the none/replace update route.

## Return

Use the report template. Include every current SPEC in reviewedSpecArtifacts with its path, observed bodyRevision, actual contentReadBack, result and preservation conclusion. Bind the Story/Test Case/evidence in reviewedBindings. No score placeholder, readiness flag, header-sync object, repair counter or title-validation compatibility field is part of this report. The parent owns those workflow decisions.
