# Artifact production and targeted repair

Read workflow-contract.md for versions and budget; planning-and-scoring.md owns scoring. Read only the current artifact template.

## A1 — Story

Generate from supported planning SR rows, never from a score response alone. Required sections: Summary; As a/I want/so that; Business Context; granular Scope; exactly `## Acceptance Criteria`; Attachments. BDD remains in each AC's Given/When/Then; do not create a competing AC section.

Assign stable unique AC-### IDs and observable Given/When/Then behavior. Cover supported positive, negative/error, validation, boundary, dependency, empty and permission behavior; no requirement to invent every category. Explicit in-scope requirements must be covered or clarified, never hidden in Out of Scope.

Before scoring check supported actor/trigger/rules/outcomes, no placeholders, exact planned filename and correct attachmentAction:
- new_story/add: exactly its matching final SPEC basename; update/replace: each selected reviewed replacement SPEC basename, no unselected local files;
- update/none: explicit no-attachment-change statement, no link to workspace-only SPEC.
No internal paths, evidence IDs, provenance URLs, score/audit information or TEST_CASE.md reference in Story. Approved business design links belong only in the SPEC fields defined in metadata-and-examples.md.

## A2 — Test design

Generate only after current Story scoring passes. For every AC record internal test intent: precondition, trigger/data, outcome, observation point, type, priority and negative coverage disposition. Verification location must be concrete in steps/results; do not invent endpoints, tables or logs.

At this design step assign the supported FR identifiers in the internal planning trace, then reference those IDs in tests and later declare the same FRs in SPEC. This reserves identifiers only; it does not create a SPEC before the Test Case title gate. Reconcile all final FR declarations against test references before review.

Use one table with exact template columns. Each AC needs at least one executable test verifying its own intended behavior; a denial AC may be verified by a Permission/Negative case without a fabricated Positive companion. Each FR must also have executable coverage.

Allowed scenario types: Positive, Negative, Validation, Boundary, Permission, Dependency, Empty State, State / Flow, Exception, Data Consistency, Security, Audit, Notification, Regression.

Exactly one row per AC carries its AC-level Negative Coverage Status:
- Covered: an executable source-supported rejection/failure/prohibition case exists; put disposition on that row (Negative, Permission, Validation, Security or another accurately classified rejection case).
- N/A: no meaningful additional negative behavior; rationale on its representative test.
- To Confirm: draft only, precise missing expected behavior; blocks final PASS until clarified or supported N/A/exclusion is established.
Other rows leave disposition blank. No blanket Positive requirement for all ACs.

P0: core accepted outcome, release blocker, permission boundary, irreversible/integrity/legal-commercial condition. P1: important alternate/error/boundary/dependency/audit/notification behavior. P2: evidenced low-risk supplemental/usability/regression. Avoid assigning priority mechanically.

Deduplicate equivalent AC/FR, role/state, trigger, observation objective and outcome. Different source-supported partitions/risks may justify distinct tests.

## A3 — Description alternatives

Final Test Case Description must not contain case-insensitive standalone `or` (`(?i)\bor\b`). Inspect meaning as well as lexical matches; do not hide alternatives using punctuation or vague rewriting.

Enumerate actual alternative sets. A or B or C is three choices; two independent binary dimensions have four combinations. Use product of set sizes only for independent dimensions, then exclude unsupported/impossible combinations. Never compute case count from the number of word occurrences.

Each supported split gets distinct ID, title, preconditions, data, steps and expected outcome. Non-choice natural-language uses require a precise single-scenario title, not invented branches. Unknown meaningful combinations/outcomes require clarification. Record manual-pass only after manual semantic inspection and final lexical scan. Read the actual words and meaning; a lexical check alone does not establish business completeness.

## A4 — SPEC assembly

Use final story-based lowercase kebab-case filename by default. Preserve the original exception when the user explicitly requests a particular safe .md basename (including SPEC.md): record it in explicitSpecFileNames and synchronize all references. Never infer that exception or use a generic name as a temporary placeholder. Start with two-field Review Result and a standalone --- separator. Required content follows the SPEC template.

Copy sibling Story exactly into Jira Story section. Copy the single executable table from sibling Test Case into Test Cases section, omitting standalone tracking metadata. Do not independently regenerate either embedded block.

FR-### and SC-### map to supported behavior and measurable outcomes. Measurement can be observable completion/acceptance behavior; do not invent percentages or SLAs. Mermaid represents only evidenced states/decisions. Omit irrelevant optional entities/metadata or mark Not applicable with support.

Metadata: use every applicable supplied field from the full templates and metadata-and-examples.md. Omit optional unknown fields, but never omit a supplied field merely for brevity. Date may use actual generation date. Locally generated IDs must be clearly local and not presented as Jira/source IDs. No fabricated UUID, branch or author. Approved general-spec/architecture commit links are explicit exceptions to provenance-URL exclusion; no source registers, audit material or unresolved business promises in final output.

For multiple update replacements, write one distinct final SPEC per selected mapping when content differs. Each follows the active template, embeds the current shared Story and executable Test Case table, declares the shared FR coverage, and preserves its own source-specific context/entities/flows/obligations. Do not merge selected attachments to save tokens. Read existing-jira-update.md for inventory, bindings and review of every file.

## A5 — Review and repair

Before review validate current score binding, structure, references, exact embedding and titles. Send actual files, original evidence, planning and state to independent review. Parent saves each returned report at the next unused `review-attempt-<nnn>.md` path (or the same attempt's `-post-repair.md` verification path) and points state to the latest applicable report; never overwrite a completed review record.

Classify affected layer before editing:
- SPEC formatting/diagram/embedding defect → SPEC only.
- Missing/incorrect test for existing correct AC → Test Case and embedded SPEC table.
- Missing/wrong Story requirement supported by source → Story and affected downstream, with new score.
- Missing source fact → clarification, not guessed repair.

Run shared bounded repair policy. After fixes recheck changed gates and cross-artifact relationships; never blindly regenerate unchanged Story or rescore it. Independent verifier confirms fixes plus affected consistency gates.

Review header is output of review, not evidence for that same review. Parent writes actual verdict/brief notes after the reviewer returns; header-only synchronization does not trigger another substantive review. Immediately re-read every intended SPEC and compare its Result and Reviewer Notes to the latest review. Record `specHeaderSync` with the review attempt, path, expected values and verified read-back. If the first header-only write/read check fails, retry that header synchronization once; a second mismatch is WAITING_TOOL and jiraReady remains false. Notes must contain no internal paths/score data in Jira-facing SPEC.

After all selected candidates have PASS and verified SPEC-header synchronization, verify global source coverage and current content and observed revisions; hand over to the `jira-createissue-helper` SDLC gateway. If export capability is missing, return WAITING_TOOL with artifacts prepared, not fictional Jira success.
