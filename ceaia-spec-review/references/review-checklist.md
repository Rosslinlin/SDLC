# CEAIA Review Checklist

Use this checklist for every:

```text
STORY.md + TEST_CASE.md + story-named SPEC.md
```

Return PASS only when every hard gate passes and no unresolved blocker remains. Return NEEDS_REVISION for self-fixable defects and NEEDS_HUMAN_CLARIFICATION only for missing or conflicting evidence.

## 1. Structure: hard gate

- [ ] One Story, one user-visible workspace Test Case file, and one matching story-named SPEC exist.
- [ ] SPEC name is short lowercase kebab-case and not a forbidden generic name.
- [ ] Story, Test Case, SPEC, report, and manifest use same SPEC name.
- [ ] SPEC Review Result is required two-field section at top and uses valid status value. Its prior value is not pass/fail condition for current review.
- [ ] Story, Test Case, and SPEC headings match active templates.
- [ ] Test Case table has all required columns: Test Case Id, AC / Requirement Ref, Scenario Type, Negative Coverage Status, Coverage Rationale / To Confirm, Priority, Test Case Description, Preconditions, Test Data, Test Steps, Expected Results.

## 2. Evidence, coverage, and decomposition

- [ ] Original sources and `planning.md` were reviewed when available.
- [ ] Every direct in-scope `SR-###` has a Story and AC/FR/Test Case disposition.
- [ ] No explicit requirement is silently omitted or put Out Of Scope without source evidence.
- [ ] Coverage Capability Matrix does not claim E2E coverage where FSD/API/backend/integration evidence is absent.
- [ ] Story split is assessed against independent journey, state, action, decision, permission, integration, and business-value boundaries.
- [ ] Each Story is an INVEST vertical slice, not a technical task or broad container.

## 3. Story and acceptance criteria

- [ ] Story has concise Summary, User Story, Business Context, Scope, combined AC/BDD, and matching SPEC attachment.
- [ ] User Story follows As a / I want / so that.
- [ ] Every AC has unique `AC-###`, evidence-supported Given/When/Then, and observable outcome.
- [ ] Relevant validation, negative/error, boundary, dependency, empty, and permission/security behavior is addressed.
- [ ] Dependencies and assumptions do not hide unresolved behavior.
- [ ] Jira-facing content contains no internal source paths, source IDs, URLs, or TEST_CASE.md attachment/reference.

## 4. Test design: hard gate

- [ ] Each AC has at least one executable Positive test case.
- [ ] Each AC has exactly one valid negative disposition: Covered, N/A, or To Confirm.
- [ ] Covered has a source-supported executable Negative test; N/A has a rationale; To Confirm names the exact gap.
- [ ] IDs are unique; scenario types are valid; priorities are P0, P1, or P2 and risk-based.
- [ ] Preconditions, data, entry point, trigger, verification location, and pass/fail expected results are concrete.
- [ ] No vague expected result such as works correctly, as expected, appropriate error, or if applicable.
- [ ] Relevant FRs, edge cases, dependencies, permissions, and measurable outcomes are covered without invented behavior.
- [ ] No material duplicates or artificial fragments remain.
- [ ] Reviewer has completed and recorded a manual per-row title check.
- [ ] Every Test Case Description business scenario/title has no case-insensitive standalone-word match for `(?i)\bor\b`.
- [ ] Every title alternative has been split into independently executable cases. For n title alternatives joined by `or`, source-supported Cartesian combinations are present as `2^n` cases with unique IDs and scenario-specific test details.

## 5. SPEC alignment and readiness: hard gate

- [ ] SPEC embeds sibling Story exactly and embeds executable Test Case content without tracking metadata.
- [ ] FR, entity, and SC formats follow active template.
- [ ] Mermaid flow represents actual supported journey, states, decisions, and relevant outcomes.
- [ ] Story, Test Case, SPEC, diagram, and attachment name do not contradict each other.
- [ ] No deprecated sections, review-operational content, unresolved section, or conditional PASS is present.
- [ ] After reviewer issues current verdict, generation skill synchronizes SPEC Review Result to it. This post-review write does not require another review attempt.

## Issue reporting

Every issue must identify complete workspace-relative artifact path, applicable line number or range, evidence, material impact, recommended fix, severity, and disposition. Use `<workspace-relative-path>:<line>` or `<workspace-relative-path>:<start>-<end>` when possible. Never report only bare filename or unqualified line number. Do not report preferences or harmless wording differences.

- [ ] Every platform-owned or evidence-supported repair is classified Self-fixable.
- [ ] Workspace-path exposure in Jira-facing content is caught by deterministic pre-review hygiene and not presented as missing business information.
- [ ] Unsupported generated flow branches removable from known evidence are self-fixable during attempts 1 through 3.
- [ ] During attempts 1 through 3, Needs human clarification contains only exact evidence user must provide.
- [ ] No more than three AI self-repair batches are used for artifact set.
- [ ] Every non-PASS result from attempt 4 onward sets `userActionRequired: true` and requests user correction or supplementation before next review.
