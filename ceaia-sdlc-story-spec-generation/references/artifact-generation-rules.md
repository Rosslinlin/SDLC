# Plan, author and precheck

Before writing, read [Q1–Q7](quality-contract.md). [Workflow contract](workflow-contract.md) owns phases, revisions and bounded repair; this file owns production steps only.

## 1. Plan from sources

Use [planning template](../templates/planning-template.md). Record source location → explicit requirement → assigned candidate, plus material questions and approved exclusions. For updates also use [existing Jira update](existing-jira-update.md). Preserve the original source rather than treating a generated register as complete evidence.

Assign SR IDs uniquely across the selected batch before allocating candidates; retain those same IDs and original locations in candidate plans and global coverage. Do not restart SR-001 independently for each candidate or replace the original scope with a shortened generated summary.

Retain explicit downstream integration, reporting, notification, audit/fraud, retention, operational MI and testing obligations when supplied. Missing implementation detail is not permission to omit the obligation or label it out of scope.

Split new Stories by cohesive independently testable value where supported; record a short rationale. Preserve inherited update scope. Fix candidate paths, final SPEC names and attachment mappings before authoring.

Simple inputs need only the requirement/coverage rows. When sources actually contain multiple UX states, API/data contracts, asynchronous or cross-system behavior, add the applicable state/layer rows. Label Confirmed / Not provided / To confirm honestly; a UI source does not prove backend behavior. Do not create eight mandatory registers or demand irrelevant layers.

## 2. Story → tests → SPEC

1. Write the [Story](../templates/story-template.md): supported Summary, actor/goal/value, Business Context, Scope, exactly `## Acceptance Criteria`, and Attachments. Give every AC a unique stable AC-### and Given/When/Then observable outcome. Do not invent category-filler ACs. New/add names its SPEC; update/replace names intended replacements; update/none states no attachment change.
2. Read the saved Story. Assign supported FR IDs in planning, then derive executable [tests](../templates/test-case-template.md). Every AC and FR has coverage; each test supplies supported preconditions/data, ordered actions, observation and expected results. Do not invent endpoints, logs, thresholds, copy or permission rules.
3. Keep the existing 11 test columns. Types: Positive, Negative, Validation, Boundary, Permission, Dependency, Empty State, State / Flow, Exception, Data Consistency, Security, Audit, Notification, Regression. P0 = core/release-blocking/permission/integrity behavior; P1 = important alternatives/errors; P2 = supported supplemental behavior.
4. Consider meaningful negative coverage for each AC. Put Covered on its actual rejection case, or N/A with a reason when no additional negative behavior applies. The representative-row convention is authoring layout, not a content gate. To Confirm names a material missing expected outcome and waits for that fact; optional unknown metadata does not block.
5. Check test scenarios semantically. Separate materially different conditions/results into executable cases; equivalent supported data can be parameterized with explicit data and outcomes. A title containing `or` is not automatically wrong. Do not calculate case counts from word counts or invent a full combination product.
6. Assemble every [SPEC](../templates/spec-template.md) by copying the complete saved Story and the one saved test table exactly. Declare the same FR IDs, measurable supported SCs and relevant entities. Use a supported Mermaid flow when meaningful; a simple feature may have a simple flow. Never create generic or invented integration branches.
7. Preserve supplied metadata and approved business references using [metadata/examples](metadata-and-examples.md). Keep internal registers, local paths, score diagnostics and credentials out of business deliverables. Only the real numeric Story Score belongs in the designated SPEC header later.

Different update replacements keep distinct files when source-specific contents differ. Each contains the current shared Story/table and its own preserved source context. Do not merge files or create extra Jira tickets to simplify authoring.

## 3. One production precheck

Read the current files once and reuse those reads for this pass:
- Actual safe `<name>-spec.md` names, SPEC File Name, Story attachment names, Test Case Related SPEC, state and all replacement mappings agree.
- Exactly one canonical AC heading, unique declared IDs and BDD; referenced AC/FR/TC IDs exist. Count declarations, not references.
- Required template sections/11 columns and supplied metadata are present; no placeholder examples remain. Table rows have escaped literal pipes and `<br>` within cells.
- Embedded Story and executable test table match their saved originals; all primary/additional SPECs are included.
- Self-check Q1–Q7, with source-to-artifact coverage and no unsupported additions. Record the concise actual result in the plan, not another full report.

Correct mechanical defects under the workflow's bounded policy. Then send readable sources, plan, current files and observed revisions to independent review. The reviewer evaluates content; formatting does not become a new business rule.

## 4. Apply findings locally

Fix only affected content: SPEC defect → affected SPEC; test defect with correct AC → tests + embedded tables; wrong requirement → Story + dependent content. Missing business fact → clarification. Align references and read back every affected file, then independently verify affected Q criteria and cross-artifact consistency. Parent saves each new review at an unused numbered path.

After content PASS parent synchronizes Result/Reviewer Notes in **every** SPEC and verifies the complete body unchanged. First header mismatch permits one header-only retry; a second is WAITING_TOOL. Continue with [real scoring](scoring-tool-contract.md), including the retained [score display / metadata procedure](spec-score-display.md). Generation never performs Jira A or B itself.
