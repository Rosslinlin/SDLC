# Mandatory review gates

Each gate has PASS/FAIL; UPDATE may be N/A only for new_story. Return evidence for every gate, including SCORE. Score PASS alone never substitutes for other gates.

## SCORE — Current audited Story assessment

Verify the complete persisted Markdown evaluation record and persistenceMode (`verbatim-json-in-markdown` or `complete-object-json-in-markdown`), genuine invocation provenance, candidate association and current exact Story bytes. The readable summary must agree with the complete raw JSON block, but only the raw block is evaluator evidence. Read the complete record and Story, verify the actual invocation association and observed storyRevision, and apply the shared text-only currentness checks. Service checksum is preserved if returned; no local hash calculation is required.

Require boolean ok=true and finite numeric finalScore>=76 within 0–100. Also require parser integrity: no missing-AC-section warning, unresolved parseErrors or disagreement between returned AC counts and declared Story ACs. Optional diagnostic fields may differ between documented and observed response versions; never require absent diagnostics or substitute section scores. Strings, stale results, malformed/partial response, unknown provenance and fabricated records fail. Optional audit/review IDs are reported only when returned. Never rescore or calculate a replacement.

## STRUCTURE — Current artifacts and template integrity

Use state paths, including source-qualified update directories. Require planning, state, score response, Story, Test Case and every current named SPEC, including additional distinct update replacements. Required headings/table columns match active templates, no unresolved placeholders or duplicated test tables. Review header contains Result and Reviewer Notes and a supported status. Stale/Not Reviewed header alone does not fail current substantive review.

Check final SPEC name (default lowercase kebab-case, or a safe exact user-requested basename recorded in explicitSpecFileNames), path references and attachmentAction. In update/none, the local SPEC exists but Story must not claim it is uploaded. In add/replace, Story references only intended matching SPEC basenames. Internal source/audit paths never leak into business artifacts.

## COVERAGE — Source traceability and decomposition

Read original evidence before the register. Every candidate-assigned SR has supported Story/AC/FR/test coverage or explicit disposition; no hidden exclusions or unsupported claims. Source records contain precise locations and SR-to-Evidence links. Coverage matrix honestly identifies supported layers.

For new Stories explicitly assess INVEST and the planning business-preservation checklist; record sizingSplitRecommendation and rationale. Do not mechanically split related error handling. For updates preserve inherited baseline; broad pre-existing scope is not an automatic demand to create new tickets. Report cross-Story omissions/contradictions to the global coverage check.

## STORY — Acceptance quality

Clear actor/value/trigger/scope and material dependencies. Exactly one standalone ## Acceptance Criteria heading; stable unique AC IDs, evidenced Given/When/Then and observable results within it. Relevant supported positive/error/boundary/permission/dependency behavior is represented. Missing business facts are clarification, not defaults to invent. Internal scores, provenance references and TEST_CASE.md links are absent.

## TESTS — Executability and title validation

Each AC and FR has executable coverage. A denial AC can have a Permission/Negative case; do not require an invented Positive companion. Cases have unique IDs, allowed scenario types, P0/P1/P2 risk-based priority, supported preconditions/data, ordered steps, observation point and observable expected results.

Exactly one row per AC carries Covered/N/A/To Confirm. Covered identifies a real supported rejection/failure case; N/A has rationale. To Confirm is valid only in draft and blocks PASS until resolved. Other rows are blank in that disposition column. Remove material duplicates without hiding distinct risks.

Manually inspect every Test Case Description; no standalone case-insensitive or. Enumerate actual alternatives/independent dimensions and valid combinations. Never require 2^n from word counts. Mechanical word scan does not establish semantic split completeness. Record manual-pass only when both checks pass.

## SPEC — Alignment

Every SPEC embeds current sibling Story and executable Test Case table exactly. FR/SC references resolve, success criteria are measurable without invented SLAs, and Mermaid matches evidenced behavior. Preserve supplied template metadata and explicitly approved design-reference fields. No unresolved promises, generic diagram, internal provenance/audit leakage or undocumented integration branches.

Only the two-field review header is excluded from body binding. Parent synchronizes it after verdict, re-reads every complete intended SPEC, compares both fields with the latest numbered review record, and records the final fileRevision plus `specHeaderSync`. Any other change requires affected review. A stale, mismatched or unreadable header blocks jiraReady even when the substantive verdict is PASS.

## UPDATE — Preservation and supported write scope

For updates additionally apply existing-jira-update-review.md. Require complete baseline/inventory, supported delta, explicit attachmentAction, resolved mapping or valid none, and no unsupported field changes. Separate AC empty is not a standalone failure when description is usable.

## Findings and re-verification

Each finding: ID; severity Critical/Major/Minor; gate; owner; full workspace-relative path and known line; source evidence; observed defect; impact; supported action; affected artifacts; storyChangeRequired.

Verify findings are not already resolved elsewhere. Initial review covers all gates. Post-repair review checks every invalidated gate plus cross-artifact consistency, retaining still-current evidence for unchanged gates; never carry a gate across changed bindings. PASS still requires the complete current gate set.
