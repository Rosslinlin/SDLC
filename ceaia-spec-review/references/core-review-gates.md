# Mandatory review gates

Content gates have PASS/FAIL; UPDATE may be N/A only for new_story. SCORE is `DEFERRED` during independent content review because the real scoring call occurs after the reviewed SPEC status has been synchronized. Return evidence for every applicable content gate. A content PASS is not final Jira readiness.

## SCORE — Deferred to the parent after content review

The independent reviewer marks SCORE=`DEFERRED` and does not invoke or verify a score response. It checks the Story's actual canonical AC heading and declarations under STORY/STRUCTURE so the parent can safely submit the final file. After content PASS, the parent synchronizes the SPEC header, scores the final Story with verified SPEC metadata, preserves the complete raw response and verifies boolean ok=true, finite numeric finalScore>=76, AC/parser integrity and current Story/SPEC binding. A pre-change score without current SPEC metadata cannot satisfy this later gate. No score is invented or inferred from content review.

## STRUCTURE — Current artifacts and template integrity

Use state paths, including source-qualified update directories. Require planning, state, Story, Test Case and every current named SPEC, including additional distinct update replacements; a score response is not required before content-review PASS. Required headings/table columns match active templates, no unresolved placeholders or duplicated test tables. Review header starts with Result and Reviewer Notes, then Story Score. The initial Not scored value is permitted until the parent's later real scoring step; do not fail or invent a pre-review score. Stale/Not Reviewed header alone does not fail current substantive review.

Check every generated SPEC's actual basename against the mandatory `<name>-spec.md` rule, with no safe-filename exception, including a local update/none SPEC that is not uploaded. The `<name>` portion must agree with the reviewed Story/business function; distinct replacement contents need distinct names. Compare each actual path and basename with its SPEC File Name field, state.specArtifacts, planning, standalone Test Case Related SPEC path, review binding, applicable update manifest and attachment mapping, and intended upload plan. In update/none, Story must not claim the local SPEC is uploaded. In add/replace, Story references only intended matching SPEC basenames. Any mismatch fails STRUCTURE and blocks PASS/Jira readiness; do not accept an upload-only alias. Internal source/audit paths never leak into business artifacts.

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

Only Result, Reviewer Notes and the derived Story Score header field are excluded from business-body binding. Parent synchronizes the first two after the content verdict, re-reads every complete intended SPEC and compares them with the latest numbered review record. The primary SPEC Result/path accompany the real Story score; parent then follows [SPEC score display](../../ceaia-sdlc-story-spec-generation/references/spec-score-display.md) to populate the third field and finalize current fileRevision/score metadata. This exact derived display write needs no new substantive review. Other body/reference changes still require affected review. A stale, mismatched or unreadable header blocks readiness even when the substantive verdict is PASS.

## UPDATE — Preservation and supported write scope

For updates additionally apply existing-jira-update-review.md. Require complete baseline/inventory, supported delta, explicit attachmentAction, resolved mapping or valid none, and no unsupported field changes. Separate AC empty is not a standalone failure when description is usable.

## Findings and re-verification

Each finding: ID; severity Critical/Major/Minor; gate; owner; full workspace-relative path and known line; source evidence; observed defect; impact; supported action; affected artifacts; storyChangeRequired.

Verify findings are not already resolved elsewhere. Initial review covers all gates. Post-repair review checks every invalidated gate plus cross-artifact consistency, retaining still-current evidence for unchanged gates; never carry a gate across changed bindings. PASS still requires the complete current gate set.
