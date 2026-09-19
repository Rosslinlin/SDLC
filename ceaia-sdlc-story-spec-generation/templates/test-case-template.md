# Test Cases: [FEATURE / STORY NAME]

- **Author**: [Author]
- **Date**: [DATE]
- **Requirement ID**: [UUID]
- **Parent Requirement ID**: [UUID]
- **Related Story**: [Story title or Jira issue if available]
- **Related SPEC**: [workspace-relative story-named SPEC path]
- **Status**: Draft

<!--
Rules:
- TEST_CASE.md is a user-visible workspace deliverable and must not be exported, attached, uploaded, referenced, or included in Jira payloads.
- Test cases must cover Story Acceptance Criteria IDs, including the embedded Given/When/Then BDD details.
- Test cases must cover explicit in-scope source requirements assigned to the Story.
- Use one Test Cases table only. Do not create a separate AC Coverage Matrix.
- Every AC must have at least one Positive test case.
- Record Negative Coverage Status and Coverage Rationale / To Confirm in the single Test Cases table. For Covered, populate the source-supported Negative test case row. For N/A or To Confirm, populate a Positive test case row for that AC. Leave these fields blank in rows that do not carry the AC-level negative coverage disposition.
- Every AC must have a Negative Coverage Status of Covered, N/A, or To Confirm. Use N/A only with a rationale; do not invent artificial negative behavior.
- Include relevant negative/error, boundary, dependency, empty-state, and permission/security behavior when source-supported.
- Every generated test case must have a Scenario Type and P0/P1/P2 execution priority.
- Do not introduce behavior unsupported by STORY.md or the story-named SPEC.
- `Test Case Description` is the final business scenario/title. It must not contain the standalone word `or`, case-insensitively (`(?i)\bor\b`).
- Before finalizing the table, split every `or` alternative into independently executable cases. A candidate with `n` occurrences of `or` must produce the `2^n` source-supported alternative combinations, each with a unique ID and its own preconditions, data, steps, expected results, and coverage disposition.
- Do not evade this rule by replacing `or` with punctuation, `/`, `and`, or vague wording. If a supported alternative or combination cannot be determined, record the exact gap as `To Confirm` and request clarification rather than inventing behavior.
- Before SPEC generation, manually inspect every non-empty `Test Case Description` cell with `(?i)\bor\b` and confirm the required alternative splits. A remaining match or missing required split blocks the artifact set.
-->

## Test Cases

| Test Case Id | AC / Requirement Ref | Scenario Type | Negative Coverage Status | Coverage Rationale / To Confirm | Priority | Test Case Description | Preconditions | Test Data | Test Steps | Expected Results |
|---|---|---|---|---|---|---|---|---|---|---|
| TC01-[Short_Name] | AC-001 | Positive | | | P0 | Verify the core accepted business outcome for AC-001 | [Required role, state, permission, and dependency] | [Valid business data] | 1. [Open the entry point]<br>2. [Perform the business action]<br>3. [Verify the result location] | 1. [Observable accepted outcome]<br>2. [Required status or data change] |
| TC02-[Short_Name] | AC-001 | Negative | Covered | [Source-supported invalid, rejected, prohibited, or failed condition] | P1 | Verify AC-001 blocks a source-supported invalid condition | [Required role, state, permission, and dependency] | [Invalid or disallowed business data] | 1. [Open the entry point]<br>2. [Trigger the invalid condition]<br>3. [Verify the result location] | 1. [Observable rejection or error outcome]<br>2. [No invalid state or data change occurs] |
| TC03-[Short_Name] | AC-002 | Positive | N/A | [Why no meaningful full negative scenario applies] | P0 | Verify the core accepted business outcome for AC-002 | [Required role, state, permission, and dependency] | [Valid business data] | 1. [Open the entry point]<br>2. [Perform the business action]<br>3. [Verify the result location] | [Observable accepted outcome mapped to AC-002] |
| TC04-[Short_Name] | AC-003 | Positive | To Confirm | [Exact missing negative expected behavior] | P0 | Verify the core accepted business outcome for AC-003 while negative behavior remains To Confirm | [Required role, state, permission, and dependency] | [Valid business data] | 1. [Open the entry point]<br>2. [Perform the business action]<br>3. [Verify the result location] | [Observable accepted outcome mapped to AC-003] |
| TC05-[Short_Name] | AC-002 | Boundary | | | P1 | Verify the source-defined boundary behavior for AC-002 | [Required precondition] | [Boundary value] | 1. [Open the entry point]<br>2. [Enter or select the boundary value]<br>3. [Submit and verify] | [Observable boundary outcome mapped to AC-002] |
