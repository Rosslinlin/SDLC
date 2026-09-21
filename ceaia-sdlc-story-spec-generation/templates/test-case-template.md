# Test Cases: [Story Title]

- **Author**: [supplied author; omit if unknown]
- **Date**: [actual date]
- **Requirement ID**: [supplied requirement ID; omit if unknown]
- **Parent Requirement ID**: [supplied parent ID; omit if unknown]
- **Related Story**: [Story title / known ticket]
- **Related SPEC**: [current workspace-relative SPEC path]
- **Status**: Draft

## Test Cases

| Test Case Id | AC / Requirement Ref | Scenario Type | Negative Coverage Status | Coverage Rationale / To Confirm | Priority | Test Case Description | Preconditions | Test Data | Test Steps | Expected Results |
|---|---|---|---|---|---|---|---|---|---|---|
| TC-001 | AC-001 / FR-001 | [accurate type] | [Covered/N/A/To Confirm or blank] | [rationale when required] | [P0/P1/P2] | [single precise business scenario] | [supported entry state] | [executable data] | 1. [action]<br>2. [action and observation location] | [observable acceptance result] |
| TC-002 | AC-001 / FR-001 | Validation | Covered | [source-supported rejection and why it is meaningful] | P1 | [one specified invalid-input rejection] | [evidenced entry state] | [specified invalid data] | 1. [enter specified invalid value]<br>2. [submit]<br>3. [observe specified location] | [specified rejection; no invented side effects] |
| TC-003 | AC-002 / FR-002 | Permission | Covered | [AC itself defines prohibited access] | P0 | [one evidenced unauthorized action is denied] | [specified role and state] | [specified target] | 1. [attempt prohibited action]<br>2. [observe denial location] | [source-defined denial outcome] |
| TC-004 | AC-003 / FR-003 | Positive | N/A | [specific reason no additional meaningful negative behavior applies] | P0 | [one accepted read-only outcome] | [specified role and existing record] | [supplied record] | 1. [open record]<br>2. [observe supplied values] | [observable display outcome] |
| TC-005 | AC-004 / FR-004 | Boundary | [disposition only if this is its representative row] | [evidenced rationale] | P1 | [one source-defined boundary condition] | [known entry state] | [exact supplied boundary] | 1. [enter boundary data]<br>2. [perform action]<br>3. [observe result] | [source-defined boundary result] |

<!-- Authoring instructions: replace example row; remove this comment.
Every AC has an executable test for its intended behavior, not necessarily a Positive case.
Exactly one row per AC carries its negative disposition; other rows leave it blank.
Covered means supported rejection/failure is tested. N/A requires rationale. To Confirm is draft-only and blocks final PASS.
Use one AC ID per row; repeat rows where distinct ACs require distinct assertions, and add applicable FR references.
Titles have no standalone case-insensitive "or"; split actual evidenced alternatives, not 2 raised to word count.
Rows are alternative authoring examples, not mandatory filler. Keep only evidenced cases, align IDs to actual Story/FRs, and choose exactly one disposition per AC. TC-001 is normally blank when TC-002 carries Covered for that AC.
Draft-only To Confirm is permitted while asking for the exact missing negative outcome; it must not remain in final passing output.
Retain supplied Author/external IDs; omit optional unknown values rather than inventing them.
Standalone file is user-visible but never a Jira attachment. Embed only this executable table in SPEC.
-->
