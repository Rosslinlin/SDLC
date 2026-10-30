# Requirements Review Report: [FEATURE NAME]

- **Review Date:** [Actual review date]
- **Artifacts / Revision:** [Actual document paths and revision, if known]
- **Branch / PR:** [Actual context, or Not applicable]
- **Scope:** [Both phases, or the explicitly requested subset]
- **Validation Method:** [Independent second-agent validation / no candidates to validate / independent validation incomplete, with reason]
- **Status:** [PASS / NEEDS REVISION / NOT REVIEWED]

## Executive Summary

[Brief assessment of both phases, key risks, and overall conclusion. State any incomplete review coverage.]

## Phase 1: Requirements Review

### Specialized Review Findings

**Understandability: [PASS / NEEDS REVISION / NOT REVIEWED]**

- Score: [1–5, or Not assessed with reason]
- Observations: [Evidence supporting the score; PASS requires at least 4/5]

**Success Criteria: [PASS / NEEDS REVISION / NOT REVIEWED]**

[Measurability, user focus, technology independence, and verifiability findings.]

**User Story Format: [PASS / NEEDS REVISION / NOT REVIEWED]**

- Format Compliance: [Compliant Stories / reviewed Stories]
- INVEST Principles: [Assessment]
- Acceptance Criteria: [Clarity, testability, and Given/When/Then form]
- Story Sizing: [Granularity assessment]

**Testability & Coverage: [PASS / NEEDS REVISION / NOT REVIEWED]**

[Scenario clarity and relevant error, negative, boundary, and failure coverage.]

**Feasibility & Constraints: [PASS / NEEDS REVISION / NOT REVIEWED]**

[Feasibility, missing functional details, undefined cases, constraints, and coupling risks.]

**Scope & Dependencies: [PASS / NEEDS REVISION / NOT REVIEWED]**

[Boundaries, assumptions, external dependencies, and cross-team dependencies.]

### Sizing & Split Recommendation: [SPLIT / NO SPLIT / NOT ASSESSED]

**Decision Gate Assessment:**

- AC Count: [Actual count; typically ≥4 triggers evaluation]
- Independent Workflows: [Actual count, or Unknown]
- System Components Affected: [Actual count, or Unknown]
- Cross-Team Dependencies: [Evidence, or Unknown]
- Estimated Effort / Team Capacity: [Supplied estimate/context, or Unknown]

**Decision:** [SPLIT / NO SPLIT / NOT ASSESSED]

For a recommended split, fill the plan below. Otherwise omit the table and explain why one ticket remains appropriate or what information is missing.

| Proposed Ticket | Scope | ACs | Estimated Effort | Dependencies | Business Value |
| --- | --- | --- | --- | --- | --- |
| [Piece identifier] | [Scope] | [Count] | [Known estimate, or Unknown] | [Known dependencies] | [Incremental value] |

**Rationale:** [Why the recommendation reduces risk or preserves cohesion.]

**Split Quality Validation:** [Independent testing, meaningful value, minimal dependencies, delivery order, risk distribution, and AC coverage.]

### Critical Issues (Phase 1) — Count: [Actual count]

For each finding, provide:

- **ID / Category / Priority:** [Stable issue ID, review perspective, P1/P2/P3]
- **Location:** [Artifact, section/requirement ID, line if available]
- **Problem:** [Specific confirmed defect]
- **Evidence and Impact:** [Relevant source facts and consequence]
- **Recommended Fix:** [What must be clarified or changed without inventing the business answer]
- **Validation:** [Confirmation basis and method]

### Major Issues (Phase 1) — Count: [Actual count]

[Material findings requiring revision, using the same issue format. Keep P2 issues distinct from non-blocking warnings.]

### Warnings (Phase 1) — Count: [Actual count]

[Non-blocking, evidence-based recommendations. State None when there are none.]

### Strengths (Phase 1)

[Well-supported strengths of the requirements.]

## Phase 2: Decomposition Review

### Step 1: Requirement-to-Task Mapping: [PASS / FAIL / NOT REVIEWED]

- Coverage: [Covered requirements / total in-scope requirements = percentage, or Not assessed]
- Orphaned Tasks: [Actual count, or Not assessed]
- Findings: [Issue details, or None]

### Step 2: Task Decomposition Quality: [PASS / FAIL / NOT REVIEWED]

- Average Granularity: [Good / Fair / Poor, with evidence; or Not assessed]
- Consistency: [Consistent tasks / assessed tasks, if measurable; otherwise qualitative evidence]
- Findings: [Issue details, or None]

### Step 3: Task Description Completeness: [PASS / FAIL / NOT REVIEWED]

- Complete Tasks: [Tasks with title, input, output, AC, and requirement link / reviewed tasks]
- Findings: [Missing elements by task, or None]

### Step 4: Task Dependency Logic: [PASS / FAIL / NOT REVIEWED]

- Total Dependencies: [Known count, or Not assessed]
- Circular Dependencies: [Known count and cycles, or Not assessed]
- Findings: [Missing, invalid, or unjustified relationships; or None]

### Step 5: Consistency and Naming: [PASS / FAIL / NOT REVIEWED]

- Naming Consistency: [Evidence; percentage only with a defined denominator]
- Format Consistency: [Evidence; percentage only with a defined denominator]
- Findings: [Material inconsistencies or duplicates, or None]

### Critical Issues (Phase 2) — Count: [Actual count]

[Blocking findings using the same issue format as Phase 1.]

### Major Issues (Phase 2) — Count: [Actual count]

[Material findings requiring revision, with location, evidence, impact, and fix.]

### Minor Issues (Phase 2) — Count: [Actual count]

[Useful non-blocking recommendations; omit cosmetic preferences.]

## Combined Assessment

### Overall Status: [PASS / NEEDS REVISION / NOT REVIEWED]

**Phase 1 Verdict:** [PASS / NEEDS REVISION / NOT REVIEWED]

- Critical Issues: [Actual count]
- Major Issues: [Actual count]
- Warnings: [Actual count]

**Phase 2 Verdict:** [PASS / NEEDS REVISION / NOT REVIEWED]

- Critical Issues: [Actual count]
- Major Issues: [Actual count]
- Minor Issues: [Actual count]

### Required Actions Before Proceeding

[Prioritized fixes needed to meet the review criteria. State None when the review passes.]

### Recommendations for Improvement

[Non-blocking suggestions, distinct from required fixes.]

### Review Limitations

[Missing inputs, excluded phases, unavailable tools, failed validation, or None.]

### Next Steps

[If both phases PASS: requirements and decomposition are ready for the next design step. If NEEDS REVISION: address required fixes, then review the revised artifacts. If scope was partial/incomplete: state exactly which conclusion remains unassessed.]
