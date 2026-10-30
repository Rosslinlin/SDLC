---
name: review-requirements
description: Review feature requirements and their decomposition into tasks, including User Story format, clarity, measurable success criteria, acceptance coverage, feasibility, scope, sizing, traceability, task granularity, and dependencies. Use for reviewing requirement documents and task plans, including those in a pull request.
---

# Requirements and Decomposition Review

Review two distinct subjects:

1. **Requirements:** Are the requirements complete, understandable, testable, unambiguous, user-focused, and appropriately sized?
2. **Decomposition:** Do executable tasks cover those requirements with suitable granularity, clear descriptions, valid dependencies, and consistent naming?

This is a standalone qualitative review. It does not generate or edit business requirements, call the Story scoring tool, synchronize Jira scores, export Jira issues, or replace the existing SDLC generation/review/export gates. Run it when this review is requested; its presence in the repository does not add a stage to the SDLC Task.

## Platform and scope

Use the platform's available file, GitHub/PR, Jira read, and agent tools. All instructions and resources in this Skill are plain Markdown. Do not run shell commands, scripts, local code, or external telemetry requests.

Reviewing documents does not authorize publishing comments. Return the report to the user and save it through the platform's file tool when available. Post a PR summary only when the user explicitly requests it and a suitable GitHub write tool is available. Never invent a tool name or claim an unavailable operation succeeded.

All review criteria are included in this file under **Detailed review criteria**. Use [templates/review-report-template.md](templates/review-report-template.md) for the report. The Skill consists of this file and that template; both are plain Markdown.

## 1. Locate and load artifacts

Accept a requirement document and task plan supplied directly, workspace paths, or a PR URL. Examples include `spec.md`, `tasks.md`, and requirement documents under `spec/`, `specs/`, or `doc/`. Locate actual artifacts rather than assuming a filename exists.

For a PR:

1. Accept GitHub or the organization's GitHub Enterprise PR URL. Extract the repository and PR number. If only a number is supplied, obtain the missing repository context.
2. Read the title, description, head/base branch, head commit, changed files, diff, and existing discussion/review comments through available tools. Use the diff to identify changed lines and binary/image artifacts, and read the relevant full document sections for context.
3. Find the requirement document and task plan. If the PR has no identifiable requirement document, ask for the document path or confirmation of the intended scope.
4. If the title follows `[JIRA-ID] design summary`, optionally read that ticket for feature context and acceptance criteria through a Jira read tool. If the title has `[NO-JIRA]` or no Jira ID, proceed using the documents and PR description. An unavailable optional Jira read must be disclosed, not silently treated as completed.
5. Review existing comments to avoid duplicate feedback. Acknowledge an already raised issue and its current resolution state rather than posting it again.

For direct documents or workspace paths, use the supplied material without requiring a PR. Preserve actual artifact names and identifiers in findings. Do not rename existing SDLC artifacts or confuse a task plan with the platform's SDLC Task JSON.

If a required artifact is missing, ask for it. If the user requests a requirements-only or decomposition-only review, complete that scope and mark the other phase `NOT REVIEWED`; do not claim a combined PASS. Do not create missing requirements or tasks to make the review pass.

## 2. Review requirements

Apply the specialized review perspectives detailed below:

- **Understandability:** Plain language, consistent terms, logical structure, useful examples; score 1–5, with 4/5 required for PASS.
- **Success Criteria:** Measurable, technology-agnostic, user-focused, independently verifiable outcomes.
- **User Story Format:** Role, goal, value, INVEST principles, and clear Given/When/Then acceptance criteria.
- **Testability & Coverage:** Happy, negative, boundary, error, and failure scenarios relevant to the requirement.
- **Feasibility & Constraints:** Missing functional details, ambiguity, constraints, coupling, and implementation leakage.
- **Scope & Dependencies:** Boundaries, assumptions, external/cross-team dependencies, and over-scoping.
- **Sizing & Split Recommendation:** Evaluate large AC counts, independent workflows, components, dependencies, effort, and incremental value. Four or more ACs trigger evaluation, not an automatic split.

When agent tools are available, these perspectives may be reviewed in parallel. They are review roles, not separate installed Skills or a requirement to run code. Keep each role within its assigned subject, share the same artifact revision, and combine duplicate candidate findings before validation.

Report high-signal issues supported by the input. Do not flag wording preferences, reasonable clarification markers, or sufficient sections merely because more detail is possible. Do not guess business rules, effort estimates, dependencies, or missing evidence.

## 3. Validate requirement findings

The original workflow requires a second reviewer to confirm each candidate requirement issue before inclusion as a validated finding.

When independent agents are available:

1. Give each unique issue an ID and assign exactly one validation agent to it. Different agents validate different issues; do not duplicate the same validation across agents.
2. Provide the relevant requirement text, the candidate issue, and the reason it was raised.
3. The validator must confirm or reject the issue with evidence. Reject an issue when the violation cannot be confirmed with high confidence.
4. Wait for all assigned validations and include only confirmed findings. Record rejected issues separately only if useful to explain the review.

If candidate findings require validation but the platform cannot provide an independent agent, a separate verification pass may still produce a useful provisional report. Label it **single-agent verification; independent validation incomplete**, keep those candidates separate from independently confirmed findings, and withhold a combined PASS until the required independent validation is complete. Do not claim second-agent confirmation. If there are no candidate findings, report that there was nothing to validate rather than inventing a validator result.

Apply this validation to clarity/feasibility findings, success criteria/testability findings, and sizing/splitting recommendations. An uncertain suggestion must not become a confirmed defect.

## 4. Review decomposition

Assess task quality separately from requirement validity. Use the five detailed decomposition checklists below:

1. **Requirement-to-Task Mapping:** Every requirement has at least one task; every task has a requirement source. Require 100% coverage and zero orphaned tasks.
2. **Task Decomposition Quality:** Tasks are complete, executable units, generally 1–5 days, with consistent granularity and no unnecessary fragmentation.
3. **Task Description Completeness:** Each task has a title, input, output, acceptance criteria, and requirement link.
4. **Task Dependencies and Sequencing:** Dependencies are necessary, justified, acyclic, and correctly distinguish sequential and parallel work.
5. **Consistency and Naming:** Titles, IDs, terminology, and formatting are consistent, with no duplicate tasks.

These are review criteria for the supplied task plan; they do not change any SDLC Skill, workflow stage, or Task configuration.

## 5. Produce the report

Use the bundled template and populate it with evidence from the reviewed artifacts. Include:

- Artifact identities, revision/PR context when available, review scope, and validation method.
- Findings for all requested perspectives, the 1–5 understandability score, Story format/INVEST/AC observations, and sizing/split recommendation.
- The five decomposition checks, traceability coverage, orphan counts, and task completeness.
- Each issue's ID, category, priority, location, problem, impact, recommended fix, and validation basis.
- Critical/Major/Minor counts, phase verdicts, combined status, required actions, and non-blocking recommendations.

Use real counts. For a percentage, show its numerator/denominator or explain the calculation. If input is absent or a measure cannot be established, say `Not assessed` with the reason instead of inventing a value. Keep source understandability scoring separate from checking the clarity of the review report itself.

### Verdicts and priorities

- **P1 / Critical:** A confirmed blocker to understanding, validating, or executing the requirement/plan, such as missing mandatory content, untestable outcomes, uncovered requirements, or circular dependencies.
- **P2 / Major:** A material issue requiring revision, such as incomplete task descriptions, invalid sequencing, or excessive scope supported by the evidence.
- **P3 / Minor:** A useful non-blocking improvement. Do not include subjective wording preferences as findings.

Use `PASS` or `NEEDS REVISION` for assessed phase verdicts. Requirements PASS requires understandability ≥4/5 and no unresolved confirmed issues that prevent the criteria from being met. Decomposition PASS requires the five checks to meet their stated criteria. Combined PASS requires both requested phases to be complete and pass. Missing artifacts, unresolved material findings, or failed essential checks must not be hidden by a summary PASS. Non-blocking recommendations alone do not fail an otherwise passing review.

If the user asked for a narrower review, use `NOT REVIEWED` for excluded phases and describe the limited conclusion. A review status is not the numeric Story score or the SDLC `specFileStatus` parameter; do not send it to those tools.

### Report delivery

When a workspace file tool is available, save the report at the user-specified path. Otherwise, for the original branch-based workflow, use `specs/{actual-branch}/checklists/requirements-decomposition-review.md` only when that branch and workspace location are known. For direct documents without branch context, use a suitable path beside the supplied artifacts or return the complete report in the conversation. Never use a literal unresolved branch placeholder or claim a file was saved when it was not.

If posting a PR summary was requested, read existing comments first, fill the template with the actual findings, and post through the available GitHub write tool. Preserve the report if publishing fails; do not repeat an uncertain write blindly.

Present a concise user summary with both phase verdicts, score, issue counts, split recommendation, mapping coverage, combined status, and the actual report location. On PASS, state that the reviewed material is ready for the next design step; mention a separate generator only if it exists in the target environment. On NEEDS REVISION, list the required fixes and recommend another review after revision. Do not invoke a nonexistent generator or start the next workflow automatically.

## Errors and incomplete work

- **Requirement document missing:** Request its actual path/content; suggest the environment's requirement/spec generator only if available.
- **Task plan missing:** Request its actual path/content; suggest the environment's task generator only if available.
- **Malformed document or missing mandatory sections:** Identify locations and structural fixes as material findings. Mandatory sections must come from the supplied specification/template, not invented rules.
- **Tool/agent failure:** Continue with available evidence, state the affected coverage and validation limits, and withhold any conclusion that depends on missing results.
- **Insufficient information:** Ask a focused question rather than guessing. Keep completed review work available.

## Detailed review criteria

### User Story writing format

The standard User Story expresses who, what, and why:

> As a [type of user], I want [goal or desire], so that [benefit or value].

1. **Who / Role:** Identify the user or stakeholder, for example a customer or administrator.
2. **What / Goal:** State what the user wants to achieve, for example viewing transaction history. Focus on the need rather than its implementation.
3. **Why / Value:** Explain the benefit, for example tracking spending.
4. **Acceptance Criteria:** Include clear, testable scenarios in Given/When/Then format:
   - **Given:** Initial context or state.
   - **When:** Action or event.
   - **Then:** Expected observable outcome.

Example:

> Given I am logged into my account,
>
> When I navigate to the Transaction History page,
>
> Then I should see a list of my recent transactions.

5. **INVEST:** Check all six principles:
   - **Independent:** Can be developed and tested independently.
   - **Negotiable:** Supports discussion rather than prescribing every implementation detail.
   - **Valuable:** Delivers value to the user.
   - **Estimable:** Provides enough information to estimate effort.
   - **Small:** Small enough for a sprint.
   - **Testable:** Has clear acceptance criteria.

#### User Story checklist

- [ ] Role, goal, and value follow As a / I want / So that.
- [ ] The Story focuses on user needs and business value.
- [ ] Acceptance criteria are clear, testable, and in Given/When/Then form.
- [ ] INVEST principles are met.
- [ ] Implementation choices do not replace the user need.
- [ ] Plain language makes the Story understandable to stakeholders.
- [ ] Scope is appropriate, with a split recommendation when justified.

Common problems include an implementation-only Story such as “As a developer, I want to use PostgreSQL,” a vague goal such as “the system should be fast,” a missing value statement such as “I want to log in,” a broad goal such as “manage my entire account,” and a Story with no acceptance criteria. Flag the actual missing need, measurable result, value, scope boundary, or AC; a technical term or developer persona by itself does not prove a defect.

### Specialized requirement review perspectives

#### Understandability

- Use plain language and minimal jargon.
- Define domain terminology and acronyms consistently.
- Use logical organization, descriptive headings, concise sentences, and active voice.
- Explain complex or abstract requirements with concrete examples.
- Check whether a non-technical stakeholder can understand the intended outcome.

#### Success Criteria

- Are outcomes measurable with specific metrics where needed?
- Are criteria technology-agnostic rather than implementation instructions?
- Are statements focused on user outcomes rather than internals?
- Can the criteria be verified without knowing the implementation?

#### User Story Format

- Do Stories express role, goal, and value?
- Are they user-focused and INVEST compliant?
- Are ACs clear, testable, and expressed in Given/When/Then form?
- Are implementation details leaking into the business need?
- Is the scope small enough, or does it span several independent journeys?

#### Testability & Coverage

- Can every functional requirement be tested?
- Are acceptance scenarios clear and complete?
- Do relevant error, negative, boundary, and failure cases accompany the happy path?
- Are success criteria measurable and unambiguous?

Do not demand irrelevant edge cases. Identify a concrete missing case and its impact.

#### Feasibility & Constraints

- Are functional requirements feasible and unambiguous on the available evidence?
- Are important functional details or edge cases undefined?
- Are constraints and coupling risks identified?
- Do implementation choices obscure the required outcome?

Distinguish an explicitly supplied business/regulatory/technical constraint from an unnecessary implementation prescription. Do not remove a real constraint or invent an architectural solution during review.

#### Scope & Dependencies

- Are scope boundaries, exclusions, and assumptions clear?
- Are external and cross-team dependencies identified?
- Is scope excessive for a single delivery unit?
- Would a coherent split reduce risk while retaining incremental value?

### Understandability rubric

Score the reviewed requirements on a 1–5 scale. Use the output checklist below separately to improve the report's clarity.

#### 5/5 — Excellent

- Plain language throughout, with jargon absent or immediately defined.
- Active voice and clear subject–verb–object sentences.
- Logical structure, descriptive headings, and smooth transitions.
- Concrete examples explain complex concepts.
- Content is well formatted and easy to navigate.
- Non-technical stakeholders need no additional explanation.

#### 4/5 — Good

- Mostly plain language; the few technical terms are defined.
- Generally active voice, with a few acceptable passive sentences.
- Clear organization, with minor navigation improvements possible.
- Most concepts have examples; one or two could be clarified.
- Only minor formatting improvements are needed.

#### 3/5 — Fair

- A mix of plain language and jargon, with some undefined terms.
- Some ambiguous sentences and mixed active/passive voice.
- A recognizable structure that could be clearer.
- Some concepts lack examples.
- Understandable, but requires effort to interpret.

#### 1–2/5 — Poor

- Heavy jargon or implementation detail throughout.
- Predominantly passive voice and ambiguous sentences.
- Disorganized structure and a difficult narrative.
- Complex concepts lack explanation or examples.
- Expert knowledge is needed; non-technical readers cannot follow.

**PASS threshold: ≥4/5.** A score of 3 requires revision; 1–2 requires substantial rewriting and blocks approval. Support the score with observations from the actual text.

#### Review-output clarity checklist

- [ ] Clear headings and sections.
- [ ] Issues are numbered and categorized.
- [ ] Each issue identifies location, problem, impact, and recommended fix.
- [ ] Recommendations use P1/P2/P3 priorities.
- [ ] An executive summary is provided.
- [ ] Summaries use business language and avoid unexplained jargon.
- [ ] Technical references are contextualized when necessary.

### Sizing and split framework

#### AC-count gate

- **Typically 4+ ACs:** Trigger split evaluation.
- **2–3 ACs:** Usually suitable for one ticket.
- **1–2 ACs:** Consider whether related requirements should be consolidated, while retaining meaningful standalone value.

These are the original review's planning heuristics. Count alone neither proves excessive scope nor justifies deleting ACs or forcing a split.

#### Decision process

When AC count or other evidence suggests excessive scope, evaluate:

1. **Independent workflows:** Two or more independent journeys suggest a vertical split by workflow.
2. **System components:** Three or more components suggest a split candidate, but a component-based split must still produce independently testable business value.
3. **Cross-team dependencies:** Consider splits that reduce coupling and coordination risk.
4. **Sprint capacity:** Effort approaching or exceeding the team's sprint capacity suggests delivery risk.
5. **Cohesion and remaining complexity:** A single ticket can remain appropriate when the outcome is cohesive and the proposed split would make delivery worse.

Do not infer component count, team capacity, or days of effort from AC count alone. Mark absent estimates as unknown.

#### Split quality validation

For every proposed piece:

- [ ] Aim for 1–3 ACs without discarding necessary coverage.
- [ ] It can be tested independently, even if another piece fails.
- [ ] Dependencies on other pieces are minimal and explicit.
- [ ] Delivery order and prerequisites are clear.
- [ ] It delivers incremental business value.
- [ ] Risk is distributed rather than concentrated in one piece.

#### When not to split

- The requirements are too tightly coupled to separate meaningfully.
- A proposed piece has no standalone business value.
- Splitting adds excessive cross-team coordination.
- A demonstrated performance constraint requires unified design/implementation.

#### Examples and planning rationale

**Poor split:** “Design data model” and “Implement API” merely separate a design artifact from dependent implementation. When neither independently delivers the required value, keep that delivery unit together.

**Potentially good split:** “Capture KCI download tracking” and “Query tracking data and analytics” separate write and read workflows. The read workflow may use staged data for independent development/testing, while each piece delivers incremental value. Confirm those conditions rather than assuming them.

The original planning example assumes a five-day sprint, roughly 2–3 ACs per developer per sprint, and a delivery ticket within 1–2 sprints, while INVEST favors a one-sprint Story. Use the actual team's capacity when supplied; treat the example as a sizing discussion aid, not a universal commitment or a change to existing SDLC decomposition rules. Focus on natural sub-features, coupling, cross-team risk, and value cohesion.

### High-signal findings and false-positive prevention

Flag supported, material problems:

- Clear violations of the applicable requirement guidance.
- Ambiguous or untestable requirements and immeasurable criteria.
- Missing mandatory sections.
- Excessive/unnecessary `NEEDS CLARIFICATION` markers, with more than three prompting investigation.
- Clear over-scoping with coherent sub-features.
- Happy-path-only coverage where error/exception behavior materially matters.
- Missing role/value/ACs, non-INVEST scope, or implementation detail replacing the need.

Do not flag:

- Minor wording or subjective style preferences.
- Sufficient sections that could merely be longer.
- Reasonable clarification markers, including up to three unresolved critical decisions.
- Widely understood technical terms such as authentication or API.
- Requirements/tasks that are clear enough for reasonable interpretation.

A marker count is a review signal, not a substitute for checking impact. Reject a proposed defect when validation cannot confirm it. Prefer omitting a minor uncertain concern to reporting a false positive.

Good requirements are SMART here: Specific, Measurable, Achievable, Relevant, and Testable. They are unambiguous, user-focused, verifiable, written in plain language, and technology-agnostic except for explicitly supplied constraints. Check all mandatory sections and distinguish business outcomes from implementation prescriptions such as “Use PostgreSQL” or “Call REST API.”

### Decomposition review

Review the task plan, not requirement validity again. Apply the following five checks.

#### 1. Requirement-to-Task Mapping

- [ ] Every requirement maps to at least one task.
- [ ] Every task traces to at least one requirement.
- [ ] No orphaned tasks.
- [ ] No requirements without a task breakdown.
- [ ] Mapping is documented, for example using requirement IDs in task descriptions.

**Pass criteria:** 100% requirement coverage and zero orphaned tasks. Flag missing tasks, absent requirement linkage, and gaps preventing implementation. Report coverage as covered requirements / total in-scope requirements; do not guess when the denominator is unknown.

#### 2. Task Decomposition Quality

- [ ] Tasks are executable, complete units of work, generally 1–5 days.
- [ ] Similar requirements use comparable task granularity.
- [ ] Complex requirements have enough subtasks.
- [ ] Simple requirements are not over-decomposed.
- [ ] Each task has a coherent standalone deliverable.

**Pass criteria:** Appropriate and consistent granularity. Inspect tasks spanning 3+ components or 10+ ACs as potential overlarge tasks; flag trivial fragments that should be consolidated. These signals require context, not automatic rejection. If estimates are unavailable, assess the described scope and disclose the effort limit.

#### 3. Task Description Completeness

Each task must define:

- [ ] **Title:** A clear action-oriented description, such as Verb + Object + Details.
- [ ] **Input:** Required information, data, or artifacts.
- [ ] **Output:** Deliverable or result.
- [ ] **Acceptance Criteria:** Observable completion and correctness.
- [ ] **Requirement Traceability:** Source requirement link(s).

**Pass criteria:** All five elements are defined for 100% of tasks. Flag missing elements, vague descriptions, and ambiguous ACs. Count only actual task records; do not manufacture missing descriptions.

#### 4. Task Dependencies and Sequencing

- [ ] Dependencies are necessary and justified.
- [ ] There are no dependency cycles, including indirect cycles.
- [ ] Sequential tasks have the necessary dependencies.
- [ ] Independent work is correctly identified as parallel.
- [ ] The dependency chain is traceable.

**Pass criteria:** Valid, acyclic dependencies and sound sequencing. Flag circular, missing, arbitrary, or incorrectly classified relationships, with the relevant task IDs and evidence.

#### 5. Consistency and Naming

- [ ] Titles use a consistent naming pattern.
- [ ] Task IDs, when used, are logical and consistent.
- [ ] Terminology is consistent.
- [ ] Capitalization, punctuation, and formatting are uniform.
- [ ] There are no duplicate or redundant tasks.

**Pass criteria:** Consistent task naming, terminology, and structure. Focus reported findings on ambiguities, duplication, or inconsistency that affects use; do not contradict the high-signal rule by reporting cosmetic preferences alone.

## Source adaptation

Reconstructed from the supplied original `review-requirements/SKILL.md` and `templates/review-report-template.md` screenshots. All review criteria are contained in this SKILL.md; the original two-file layout is retained; the two business review phases, independent validation of candidate requirement findings, and report template are retained. Original command examples are replaced by platform-tool descriptions, PR publication requires the user's request, and shell-based session/staff lookup and external telemetry are omitted for the text-only target platform. The report adds a Phase 1 Major-issue section to match its issue priorities. These adaptations do not change the existing SDLC Skills or Task.
