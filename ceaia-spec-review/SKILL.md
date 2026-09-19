---
name: ceaia-spec-review
description: Independently review generated or updated CEAIA STORY.md, TEST_CASE.md, and story-named SPEC artifact sets for structure, evidence traceability, decomposition, test executability, alignment, Jira readiness, and existing-ticket preservation.
---

# Skill: ceaia-spec-review

# CEAIA SPEC Review

## Responsibility

Act as the independent read-only quality gate for `ceaia-sdlc-story-spec-generation`.

Do not generate scope, edit workspace files, create or update Jira issues, upload files, request Jira approval, invoke `nodejs-base-mcp.score_requirement_markdown`, or recalculate any score. Review actual source and artifacts, including the persisted audited score record, and return one structured verdict to the parent workflow.

## Progressive reference loading

For every review:

1. Read `references/review-contract.md`.
2. Read `references/core-review-gates.md`.
3. Read `references/review-checklist.md`.
4. Read `references/review-report-template.md`.

For an existing Jira ticket update, additionally read `references/existing-jira-update-review.md`.

Do not load the update-only reference for new Story creation.

## Resource manifest

Read every core review reference for every review:

- `references/review-contract.md`
- `references/core-review-gates.md`
- `references/review-checklist.md`
- `references/review-report-template.md`

For existing Jira updates, also read:

- `references/existing-jira-update-review.md`

Read the exact active generation templates supplied by the parent workflow:

- `../ceaia-sdlc-story-spec-generation/templates/planning-template.md`
- `../ceaia-sdlc-story-spec-generation/templates/story-template.md`
- `../ceaia-sdlc-story-spec-generation/templates/test-case-template.md`
- `../ceaia-sdlc-story-spec-generation/templates/spec-template.md`

## Required artifact set

New Story:

```text
outputs/ceaia/<story-slug>/STORY.md
outputs/ceaia/<story-slug>/TEST_CASE.md
outputs/ceaia/<story-slug>/<story-name>.md
.ceaia-work/planning.md
.ceaia-work/stories/<story-slug>/story-quality-score.json
```

Existing Jira update:

```text
outputs/ceaia/updates/<TICKET-KEY>/STORY.md
outputs/ceaia/updates/<TICKET-KEY>/TEST_CASE.md
outputs/ceaia/updates/<TICKET-KEY>/<story-name>.md
.ceaia-work/updates/<TICKET-KEY>/plan.md
.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json
.ceaia-work/updates/<TICKET-KEY>/update-manifest.md
```

Read original requirement evidence, active templates, planning, score result, and every applicable imported readable Jira attachment. Do not pass from a generator summary or previous verdict.

## Audited score-record verification gate

The required `story-quality-score.json` is internal audit evidence. It must be complete JSON output returned by `nodejs-base-mcp.score_requirement_markdown`, persisted verbatim: no reconstruction, hand-authored substitute, extraction, redaction, normalization, formatting change, or local score calculation qualifies.

For the reviewed artifact set, locate score record at exactly required path:

- new Story: `.ceaia-work/stories/<story-slug>/story-quality-score.json`
- existing Jira update: `.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json`

Validate all following as mandatory review gate:

1. record is present, parseable as complete tool-returned JSON, and stored at applicable required internal path;
2. record is current for exact `STORY.md` being reviewed, rather than a prior, copied, partial, or materially changed Story version;
3. when tool output contains `content_checksum`, validate it against current `STORY.md` using tool's checksum evidence/semantics; a mismatch is stale evidence;
4. when `content_checksum` is absent, validate currentness from retained audit/provenance evidence and relationship of persisted record to exact reviewed Story version; do not invent a checksum or claim checksum validation tool did not provide;
5. `ok` is boolean `true`; and
6. `finalScore` is numeric and `>= 76`.

The pass predicate is exactly:

```text
ok === true AND finalScore >= 76
```

75 does not pass. Do not substitute `finalStatus`, a label, a dimension, a breakdown, an average, a legacy score, or reviewer judgment for either predicate. Score record is mandatory additional gate, not substitute for source coverage, decomposition, Story and AC quality, test executability, SPEC alignment, update preservation, or Jira readiness.

Reviewer verifies persisted evidence only. It must never invoke scoring tool, request a second invocation, refresh cached result, retry a result, create a replacement record, calculate a score, or diagnose/reconcile scoring model. It may validate a supplied `content_checksum` only according to tool-supplied checksum evidence/semantics; it must not invent, replace, or reinterpret checksum. A missing, malformed, non-verbatim, stale, mismatched, `ok: false`, nonnumeric, or below-threshold record prevents PASS; report failed score-record gate with complete score-record path and concrete parent action under normal issue classification and attempt rules.

Treat `review_id`, `audit_id`, and `content_checksum` as optional returned audit fields: report values only when present. Other returned diagnostics, including `finalStatus`, `scoreBreakdown`, `dimensions`, `criticalIssues`, `warnings`, `strengths`, and `details`, may be recorded only as internal score-gate evidence. Score diagnostics, audit identifiers, review identifiers, checksums, and all score JSON content are internal: never place, quote, attach, link, or recommend them for Jira descriptions, Jira attachments, Jira update payload, user-visible Story content, Test Case content, SPEC content, or update manifest.

Include score-gate result in internal review report with complete score-record path, record/audit/provenance and currentness conclusion; `ok`, `finalScore`, qualifying predicate result, and optional audit fields that were present. It may summarize diagnostic fields internally when material to gate. A clean overall PASS requires this gate and every other substantive gate to pass.

## Issue location contract

For every issue, warning, failed gate, clarification, and recommended repair, identify affected file using complete workspace-relative path, including story or ticket directory.

When line is known, use `<workspace-relative-path>:<line>` or `<workspace-relative-path>:<start>-<end>`. Never return only `STORY.md`, `TEST_CASE.md`, a SPEC filename, line N, or section name without full artifact path. Use structured `storyPath`, `testCasePath`, and `specPath` values consistently. For a score-record finding, use its complete required `.ceaia-work/.../story-quality-score.json` path.

## Jira Acceptance Criteria compatibility

For existing-ticket update, confirm Jira description and Acceptance Criteria were requested separately.

Do not fail review solely because separate Acceptance Criteria field was empty, null, unavailable, not found, or returned field-level error when Jira description was retrieved successfully. In that case:

- treat complete description as authoritative baseline;
- review embedded Acceptance Criteria or equivalent acceptance statements in description;
- require update manifest to record separate field as `empty-or-unavailable`;
- fail only when combined available evidence is materially insufficient, conflicting, or cannot preserve baseline safely.

## Verdict and attempt behavior

Return exactly one:

- `PASS`
- `NEEDS_REVISION`
- `NEEDS_HUMAN_CLARIFICATION`
- `FINAL_WITH_UNRESOLVED`, only when user explicitly stops before a clean pass

`FAIL` is a gate or issue result, not normal overall verdict.

Parent workflow owns attempt control:

- Maintain `reviewAttempt` and `aiSelfRepairCount` per artifact set.
- `reviewAttempt` increases without a maximum. `aiSelfRepairCount` has a lifetime maximum of 3 and never resets.
- Attempts 1, 2, and 3 may each have at most one consolidated AI self-repair followed by fresh post-repair verification within same attempt.
- If attempt 3 remains non-PASS after third self-repair and verification, parent must ask user for information, correction, file, or decision before review attempt 4.
- Review attempt 4 and later do not permit AI self-repair of review findings. Every non-PASS result returns user-actionable details to parent, which asks user and regenerates affected artifacts before next review.
- After PASS, synchronize artifact set and mark it Jira-ready. Continue to Jira export or update preparation only after every Jira-targeted artifact set has PASS. Do not ask whether user wants to continue.
- Classify internal workspace-path exposure, stale review headers, template formatting, file-name synchronization, exact embedding, and unsupported flow branches removable from existing evidence as Self-fixable platform-owned issues.
- If human evidence is required during attempts 1 through 3, parent asks immediately rather than waiting for repair budget to be used.
- Deterministic platform hygiene is owned by parent and does not consume `aiSelfRepairCount`.

Reviewer must not ask whether to continue, export, or push to Jira.

## Output

Return one complete report per artifact set using `references/review-report-template.md`, including every required structured field.

Keep platform-owned findings in internal report. During attempts 1 through 3, do not recommend showing self-fixable findings to user as information they must supply. From attempt 4 onward, populate `userRequestedEvidence` with exact correction, decision, or additional evidence required from user for every non-PASS finding.

Include audited score-record verification in report's Gate Results and internal reviewer notes. Preserve every required report-template field and do not add scoring diagnostics to Jira-facing artifacts or payload recommendations.

When running as task's read-only subagent, do not write files. Parent persists reports to:

```text
.ceaia-work/stories/<story-slug>/review-attempt-<nn>.md
.ceaia-work/updates/<TICKET-KEY>/review-attempt-<nn>.md
.ceaia-work/review-summary.md
```

For PASS, set:

```text
jiraReady: true
automaticPostReviewJiraHandoffRequired: true only when the parent confirms every Jira-targeted artifact set has PASS and the Jira export capability is available; otherwise false
reviewStatusSynchronizationRequired: true
testCaseTitleValidation: manual-pass
recommendedNextAction: synchronize this SPEC PASS result, mark this artifact set Jira-ready, and let the parent continue automatically to the Jira write-gate workflow after all Jira-targeted sets have PASS
```

Relative paths in this skill are relative to the skill directory.

Use `skill_read_resource` with name=`ceaia-spec-review` and referenced relative path.

Use workspace tools only for files in conversation workspace.

Note: file list is sampled.

## Review execution method

### Evidence-first review order

Perform review in deliberate order so polished artifact cannot conceal missing source support. First establish review mode (new Story or existing-ticket update) and artifact-set identity. Then load required review resources and active templates. Read original source evidence, planning trace, and, for an update, ticket baseline and every applicable imported readable attachment. Next read current Story, score record, Test Case, SPEC, and update manifest where applicable. Only then assess reportable gates. Do not begin from generator summary, a prior review report, a score status, or a Jira-ready assertion.

Create internal review map that links source requirements to planning rows, Story scope and ACs, Test Case rows, SPEC FR and SC items, and review findings. This map is evidence for report, not a new delivery artifact. When artifact claims behavior that cannot be linked to available evidence, classify unsupported claim as finding even if it sounds plausible. When a source requirement has no artifact coverage or supported exclusion, classify omission. Do not accept passing score record as evidence that every source item has been preserved.

Review entire requested artifact set. Missing required file, incorrect placement, mismatched candidate directory, or stale internal record is substantive gate condition, not reason to infer what file would have contained. Use exact complete workspace-relative path in every finding. If line is known, cite it; otherwise name complete file and describe observable location or missing artifact without inventing line number.

### Score-record verification procedure

Generation workflow, not reviewer, owns single score invocation for each Story version. Reviewer must never invoke `nodejs-base-mcp.score_requirement_markdown`, ask it to be invoked again, request cache refresh, retry assessment, or make independent calculation. Reviewer validates only persisted, complete tool result and relationship to current Story.

Verify required internal path based on mode. Confirm record is present and parseable as JSON; represents complete returned object rather than human-written fragment; has not been reformatted, reduced, annotated, or substituted; and available provenance connects it to exact Story under review. If returned object includes `content_checksum`, evaluate currentness only using tool's supplied checksum evidence and semantics. Mismatch is stale. If no checksum is present, do not manufacture one: determine currentness from retained audit/provenance evidence and version relationship recorded by parent.

Apply only this predicate:

```text
ok === true AND finalScore >= 76
```

`ok` must be boolean true and `finalScore` actual numeric value. Textual number, label, breakdown, status, diagnostic conclusion, reviewer confidence, prior score, or cross-Story average cannot substitute. Score 75 does not qualify. Positive `finalStatus` does not override failing numeric value, and high numeric value does not override `ok: false`, stale content, malformed data, or failed persistence.

Record result in internal review report's Gate Results: complete score-record path; record presence and parseability conclusion; currentness conclusion; `ok`; `finalScore`; predicate outcome; and optional returned identifiers only when present. Diagnostics such as breakdowns, dimensions, issues, warnings, strengths, and details may be summarized internally when relevant to finding. Never quote, attach, link, or recommend score diagnostics, audit identifiers, review identifiers, checksums, or JSON content for Jira-facing material.

### Artifact integrity gates

Assess Story independently of score. Confirm it has required Summary, user-story narrative, Business Context, granular Scope, combined Acceptance Criteria and BDD section, stable AC identifiers, observable Given/When/Then behavior, and exactly matching SPEC filename as Jira-facing reference. Check scope is neither broadened by assumptions nor narrowed by hiding explicit requirements in Out of Scope. Check actor, trigger, condition, action, rule, and result are specific enough to implement and verify where source supports it.

Assess Test Case content independently. For every AC, verify executable Positive case and exactly one intentional negative disposition. Confirm each row has valid scenario type, priority, executable preconditions, test data, ordered steps, verification location, and observable result. Test that merely restates AC, omits initial state, relies on unspecified data, or says “verify success” is not executable. Confirm categories and priorities are evidence- and risk-supported rather than mechanically copied.

Verify manual Test Case Description split gate. Review reported manual-pass state and inspect descriptions for standalone alternatives. Description containing unresolved standalone `or`, or alternative hidden by vague rewrite, fails gate. Where supported alternatives exist, verify split cases have distinct data, steps, expected results, references, and dispositions rather than duplicate rows. If combinations are not evidenced, correct outcome is precise clarification need, not invented expansion.

Assess SPEC as aligned user-facing deliverable. Verify lowercase kebab-case final name, required review header, exact current Story embedding, executable Test Case embedding, supported Mermaid flow, functional requirement identifiers, measurable success criteria, and no exposure of internal source paths or audit material. Compare embedded Story to sibling Story rather than relying on claim it was copied. Check requirements and success criteria reflect supported scope and diagrams do not add unstated systems, roles, retries, persistence, or end-to-end assurances.

### Planning and coverage gates

Read planning as traceability artifact, not assertion of completeness. Confirm sources were classified, explicit in-scope requirements received stable internal rows, coverage capability was stated honestly, UX states were captured where supplied, and candidates were split according to independent value and testability. Verify gaps are represented as questions or `To confirm` items rather than silently converted into behavior. Source register complete in form but omitting explicit requirement is gate failure.

Review coverage matrix for unsupported end-to-end claims. UI evidence may support visible behavior and user interaction; it does not establish backend persistence, integration delivery, reporting, notifications, audit storage, security enforcement, or performance characteristics without evidence. If Story, Test Case, or SPEC claims layers beyond matrix, report unsupported claim with artifact path and source gap. If required source item is dependency rather than confirmed contract, ensure artifact preserves distinction.

### Existing-ticket update gates

For update, verify each ticket independently. Confirm source explicitly supplied or confirmed, description and Acceptance Criteria requested separately, attachment reading occurred exactly once before planning and selection, readable attachments used as evidence, skipped attachments and reasons retained, and update manifest records preservation analysis. Do not fail only because separate Acceptance Criteria field was empty or unavailable when description baseline exists and manifest says `empty-or-unavailable`; instead assess whether available combined evidence can preserve ticket safely.

Compare proposed Story and SPEC to original description and attachment evidence. Identify any lost baseline requirement, unapproved field change, unsupported new behavior, unpreserved attachment requirement, or ambiguity in selected replacement mapping. Confirm manifest has proposed description, incremental scope, summary decision, inventory, one-to-one mapping or explicit unresolved decision, and concise change summary. Reviewer must never infer selected attachment from similar filename or propose attachment ID to user.

Verify proposed update is limited to supported fields: approved description content, explicitly supported summary only when available, and selected reviewed SPEC replacements. It must not imply updates to priority, assignee, labels, custom fields, status, or deletion/creation operations. Confirm score and review material is internal and absent from all Jira-facing content and handoff recommendations.

### Findings and normal verdict vocabulary

Return exactly one overall verdict using established normal vocabulary: `PASS`, `NEEDS_REVISION`, `NEEDS_HUMAN_CLARIFICATION`, or `FINAL_WITH_UNRESOLVED`. Do not substitute `FAIL` as overall verdict; FAIL may describe individual gate condition in report. PASS allowed only when every required artifact, score-record, source-coverage, Story, test, SPEC, update-preservation, hygiene, and handoff-readiness gate passes. `NEEDS_REVISION` is defect parent can correct from existing supported evidence. `NEEDS_HUMAN_CLARIFICATION` is missing, conflicting, or decision-dependent evidence that cannot safely be inferred. `FINAL_WITH_UNRESOLVED` allowed only after explicit user instruction to stop before clean pass.

For each finding, state type, severity or blocking effect, complete affected path, line/range when known, observed evidence, and concrete parent action. Separate self-fixable platform-owned issues from evidence gaps. Examples of self-fixable issues include stale review headers, filename synchronization, exact embedding, template formatting, accidental internal-path exposure, and unsupported flow branches that can be removed without changing supported business meaning. Do not label missing business rule self-fixable merely because it would be easy to write.

Reviewer is read-only: it does not modify a file, update manifest, synchronize header, create score record, request approval, contact Jira, or ask user questions. It returns actionable findings to parent. Parent owns repair, user question, regeneration, report persistence, and handoff. Reviewer recommendation must not claim Jira write occurred or user approval has been obtained.

### Re-review and report completeness

Parent owns `reviewAttempt` and `aiSelfRepairCount`. Within attempts one through three, it may apply at most one consolidated repair batch where findings are resolvable from existing evidence, then submit revised complete artifact set for fresh review. It must ask immediately for missing human evidence. After third repair budget is exhausted, and from attempt four onward, each non-PASS result requires user-supplied correction, evidence, file, or decision before next attempt. Reviewer assesses revised files and current score record, not assertion that prior findings were addressed.

Use every field required by active review-report template. Include artifact paths, mode, evidence read, gate results, findings, traceability conclusion, score-record conclusion, verdict, user-requested evidence where required, Jira readiness, review synchronization requirement, title-validation result, and recommended next action. Keep all details needed for parent repair internal. On PASS, set Jira readiness only as defined by template and make clear automatic handoff requires parent to confirm all Jira-targeted sets have passed and separate export capability is available.

This review method preserves independent, read-only oversight while keeping score tool as mandatory audited gate rather than replacement for substantive CEAIA review.
