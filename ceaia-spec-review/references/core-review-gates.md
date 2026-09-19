# Review workflow

## Contents

- Artifact structure
- Source coverage and decomposition
- Story and Acceptance Criteria quality
- Test design and executability
- SPEC alignment and Jira readiness
- Issue validation and classification

## Gate 1: Artifact structure

Verify:

- one `STORY.md`, one `TEST_CASE.md`, and one story-named SPEC exist in each user-visible `outputs/ceaia/<story-slug>/` folder;
- SPEC is lowercase kebab-case and is not generic `SPEC.md`, `spec.md`, `story.md`, or `requirement.md` unless user explicitly requested it;
- artifact paths and SPEC name agree across Story, Test Case, SPEC, review reports, and manifest when present;
- SPEC `# Review Result` contains both Result and Reviewer Notes, using a valid status value;
- all required headings and table headers match active templates.

Missing required Test Case columns, missing template headings, or incompatible file mapping is `NEEDS_REVISION`. This gate is deterministic: do not waive it for otherwise plausible content.

For an existing Jira update, an imported attachment workspace path may retain attachment ID prefix or old source filename. Do not fail naming rule solely because import path. Instead, validate reviewed replacement `fileName` in update manifest and final `attachments.replace` mapping is lowercase kebab-case CEAIA SPEC filename and maps one-to-one to selected attachment ID.

## Gate 2: Source coverage and decomposition

Use Atomic Requirement Register in `planning.md` where available. Verify every direct, explicit in-scope `SR-###` is:

- covered by a Story candidate and final Story;
- traceable to one or more ACs, FRs, or Test Cases;
- not hidden in Out of Scope;
- not reduced to vague prose.

Verify Coverage Capability Matrix is reflected honestly:

- frontend-only evidence is not presented as end-to-end coverage;
- FSD/API, integration, data, downstream, and permission coverage is generated only when evidence supports it;
- missing material layers are stated as clarification gaps.

Evaluate each Story as INVEST vertical slice. Require a split when it combines independently testable user outcomes, journeys, screen/action states, decisions, permissions, integrations, or business ownership without strong reason to stay together. Do not require a split merely because there are several ACs; require it when scope is broad, untestable, or delivery-risky.

## Gate 3: Story and AC quality

Verify:

- Summary, actor, goal, business value, trigger, scope, and observable outcome are clear;
- User Story uses As a / I want / so that;
- each has unique sequential `AC-###` ID and evidence-supported Given/When/Then behavior;
- ACs cover relevant positive, validation, negative/error, boundary, dependency, empty, and permission/security paths;
- dependencies and assumptions are present only when material and do not hide blockers;
- Jira Story references only matching SPEC as attachment;
- no user-facing artifact exposes internal source paths, identifiers, URLs, or evidence registers.

Do not report subjective wording preferences. Report only issues that affect business understanding, implementation, acceptance, testing, traceability, or Jira readiness.

## Gate 4: Test design and executability

Verify each AC/FR group in `TEST_CASE.md`:

- has at least one executable Positive test;
- has exactly one applicable negative coverage disposition:
  - Covered on a source-supported executable Negative row;
  - N/A on a Positive row with a rationale;
  - To Confirm on a Positive row naming exact missing expected behavior;
- uses valid scenario types and P0 / P1 / P2 priorities;
- has unique IDs, concrete preconditions, test data, entry point, trigger/action, verification location, and observable expected results;
- does not use vague results such as “works correctly”, “expected”, “appropriate outcome”, or “if applicable”;
- covers relevant FRs, edge cases, and measurable outcomes without inventing behavior;
- has a documented manual per-row Test Case Description check;
- treats Test Case Description as business scenario/title and contains no case-insensitive standalone-word match for `(?i)\bor\b`;
- splits every alternative title into independently executable cases; for `n` alternative `or` occurrences, verifies source-supported Cartesian combinations have been covered as `2^n` cases rather than retained in one broad case;
- gives every split case its own executable preconditions, data, steps, expected results, and coverage disposition without inventing unsupported combinations.

Detect duplicates by comparing AC/FR, role/state, trigger/input, verification objective, and expected outcome. Flag materially duplicated cases and artificial fragments of one continuous test.

A failed manual title check, remaining `(?i)\bor\b` match in final Test Case Description, or missing required alternative split is deterministic `NEEDS_REVISION`. Do not waive this gate for otherwise plausible test content. Independent reviewer must manually inspect every non-empty title and record `testCaseTitleValidation: manual-pass` when check passes.

Standalone `TEST_CASE.md` remains user-visible workspace deliverable only. Story must not list it as Jira attachment or reference.

## Gate 5: SPEC, alignment, and Jira readiness

Verify:

- SPEC embeds full sibling Story content exactly under `## Jira Story *(mandatory)*`;
- SPEC embeds executable Test Case content under `## Test Cases *(mandatory)*`, without standalone tracking metadata;
- FR, Entities, and SC formatting matches template;
- Mermaid flow matches actual supported states and decisions, rather than generic placeholder;
- Story, Test Case, SPEC, user-flow diagram, attachment name, and source coverage do not contradict each other;
- no unresolved behavior is hidden in assumption, unresolved section, or conditional PASS;
- SPEC does not contain deprecated sections or review-operational content.

Current SPEC review header is not evidence of substantive quality for review attempt being performed. A new or repaired SPEC may legitimately say Not Reviewed or show prior review result. When all substantive gates pass, issue PASS. Generation skill then synchronizes header to PASS and verifies write. Do not return `NEEDS_REVISION` solely because header is stale, Not Reviewed, or not yet PASS; synchronization write does not require another review attempt.

## Issue validation and classification

Before reporting issue, confirm:

1. exact artifact using complete workspace-relative path and, when known, exact line or line range;
2. source or artifact evidence;
3. material impact;
4. it is not already addressed elsewhere;
5. recommended fix is supported by evidence, or requires clarification.

For every issue include:

- Issue ID;
- severity: Critical, Major, or Minor;
- category;
- location;
- evidence;
- material impact;
- recommended fix;
- disposition: Self-fixable or Needs human clarification.

Format issue locations as `<workspace-relative-path>:<line>` or `<workspace-relative-path>:<start>-<end>` when line information is available. Otherwise use complete workspace-relative path plus exact heading, table, AC ID, FR ID, or Test Case ID. Never use only bare filename such as `STORY.md` or `TEST_CASE.md`.

Classify:

- `NEEDS_REVISION` for template, mapping, traceability, duplication, clarity, or coverage defects fixable from evidence;
- `NEEDS_HUMAN_CLARIFICATION` only for truly missing/conflicting business, data, interface, security, validation, or downstream behavior;
- `PASS` only when no material issue remains.

Treat these as platform-owned Self-fixable findings during first three review attempts:

- Jira-facing Story attachment reference containing `outputs/`, another workspace directory, an absolute path, or URL instead of only SPEC filename;
- stale or unsynchronized review header;
- template heading, table, naming, mapping, or exact-embedding mismatch;
- unsupported generated Mermaid flow that can be removed while preserving source-supported behavior;
- generated duplication, wording, or traceability defects repairable from existing evidence.

From attempt 4 onward, still classify findings accurately, but set `userActionRequired: true` for every non-PASS report and state exact correction, decision, information, or file required before regeneration. Never use `FINAL_WITH_UNRESOLVED` because of an attempt number. Use it only after explicit user instruction to stop without clean pass. Do not emit `aiRepairLimitReached`.
