# Generation workflow

## Contents

- Generation workflow
- Story rules
- Test design and Test Case Description split gate
- SPEC rules
- Review status synchronization
- Jira handoff

1. Inventory inputs and enrich Jira/Confluence sources when applicable. For existing-ticket updates, complete existing Jira update intake for every supplied ticket first.
2. Complete the planning gate and resolve only the user-focused questions.
3. Process approved candidates one at a time. Generate only the current concise Jira-ready `STORY.md` using `templates/story-template.md`. For an existing-ticket update, generate the proposed replacement description from the ticket baseline plus only supported incremental requirements. Do not precreate, stub, reserve, copy, or name a `TEST_CASE.md` or SPEC.
4. Immediately invoke `nodejs-base-mcp.score_requirement_markdown` exactly once for the generated/regenerated Story, with its workspace-relative `relativePath` and `force_review: false`; persist its entire returned JSON exactly as `story-quality-score.json`. Continue only if `ok` is true and `finalScore >= 76`. Do not average scores, use a local evaluator or adapter, or expose its fields or run any other result or unavailable tool result; verify no downstream file exists and invoke `ask_user_question` for additional/corrected source material, tool recovery, or explicit stop. Do not auto-repair or auto-rescore unchanged evidence.
5. Generate the matching user-visible workspace `TEST_CASE.md` only after the current Story passes scoring, using `templates/test-case-template.md`.
6. Manually inspect every non-empty `Test Case Description` cell with `(?i)\bor\b`; proceed only after the required alternatives are split and the manual check passes. A remaining match or missing split blocks the artifact set. Do not generate its SPEC, start independent review, or prepare Jira processing.
7. Select the final lowercase kebab-case SPEC name before writing it. Never create a generic `SPEC.md` first. For attachment migration, preserve selected attachment ID but use the reviewed CEAIA SPEC filename as attachment display name.
8. Generate the matching SPEC only for Stories that pass scoring and Test Case title validation, from `templates/spec-template.md`.
9. Start `ceaia-spec-review` through the task's read-only independent subagent. The coordinator writes the returned report to the selected internal root and updates `.ceaia-work/review-summary.md`.
10. Maintain `reviewAttempt` without a maximum and `aiSelfRepairCount` with a lifetime maximum of 3 per artifact set. Never reset repair count after user input.
11. Attempts 1–3 may each apply at most one consolidated AI self-repair batch. After repairing run affected gates and conduct fresh post-repair verification within that attempt.
12. If human evidence is required at any attempt, invoke `ask_user_question` immediately. If attempt 3 remains non-PASS after its third repair and verification, ask for the exact missing information, correction, file, or decision. After response, regenerate affected artifacts and run attempt 4 onward; do not self-repair review findings.
13. From attempt 4 onward, do not AI-self-repair review findings. Each non-PASS requires a user popup; after response, regenerate affected artifacts and run the next attempt.
14. Treat SPEC `# Review Result` as review output, not evidence for the same attempt. After every review, synchronize its header to the actual verdict and notes; this write alone does not trigger review.
15. After clean `PASS`, synchronize the set and mark Jira-ready. After every Jira-targeted set passes, verify all SPEC headers read `PASS` and invoke `ceaia-sdlc-only-jira-push-content` immediately when available. For updates, hand over per-ticket manifest, exact attachment mappings, and edited imported attachment paths. Do not call Jira update APIs directly.

Do not send final completion before mandatory review runs. Missing input, ambiguity, tool error, gate failure, clarification, or user decision is recoverable waiting state: use `ask_user_question`, explain the need, and resume at the affected step. The workflow ends only after all intended Jira operations succeed or the user explicitly stops.

For any error, warning, review finding, repair instruction, or popup, identify a complete workspace-relative path and `<line>` or `<start>-<end>` when known. Never cite only a filename or line number.

Prevent and correct platform-owned defects during deterministic pre-review hygiene. Record full paths and repair details internally; do not describe them as missing business information. From attempt 4 onward, an unresolved platform defect is a technical execution blocker: ask only whether to retry or stop, never for invented requirements.

Before every review, perform deterministic platform hygiene (not review-triggered self-repair and not charged to the three-repair budget): Story attachment references contain only matching SPEC name; SPEC embeds current sibling Story exactly; Story/Test Case/SPEC/manifest/review paths and names agree; stale review headers are normalized before review.

## Story rules

Each `STORY.md` contains Summary; As a / I want / so that User Story; Business Context; granular Scope; one combined `## Acceptance Criteria and BDD Scenarios`; and an attachment reference containing only the matching story-named SPEC filename.

Use stable `AC-001` identifiers. Each AC has evidence-supported Given/When/Then behavior and observable result. Cover relevant happy, validation, negative/error, boundary, dependency, empty, and permission/security behavior. Keep Stories Jira-ready; include dependencies only when they affect implementation, sequencing, testability, or release. Never use Out Of Scope to discard explicit requirements. The attachment reference must never contain `outputs/`, another workspace directory, an absolute path, or standalone `TEST_CASE.md`.

## Test design and TEST_CASE.md rules

Before the table, create an internal test-design row for every AC:

```text
AC -> precondition -> trigger/input -> expected outcome -> verification location -> scenario type -> priority -> negative coverage disposition
```

Generate from that design, not generic happy/negative placeholders. For every AC, create at least one executable Positive case and set exactly one negative disposition: Covered (source-supported rejected/prohibited/failed behavior has executable Negative case), N/A (no meaningful negative behavior, concise rationale on Positive row), or To Confirm (negative behavior matters but outcome is missing, exact gap on Positive row).

Only use: `Positive`, `Negative`, `Validation`, `Boundary`, `Permission`, `Dependency`, `Empty State`, `State / Flow`, `Exception`, `Data Consistency`, `Security`, `Audit`, `Notification`, `Regression`. Every row includes template fields, AC/FR reference, P0/P1/P2, executable preconditions, test data, steps, verification location, and observable pass/fail results.

## Test Case Description split gate

The description is the final business scenario/title. Before finalizing `TEST_CASE.md` or embedding its table in SPEC, scan each non-empty description using case-insensitive standalone `(?i)\bor\b`.

- A final description never contains `or`; replacing it with `and`, `/`, comma, or vagueness is prohibited.
- Each `or` denotes independently executable alternatives. Replace it with a separate evidence-supported case for each side.
- With `n` occurrences, treat alternatives as Cartesian product `2^n` separate source-supported combinations as applicable.
- Give each result a unique ID and precise description; update preconditions, data, steps, expected result, AC/FR, priority, and negative disposition for the selected scenario. Do not merely duplicate a row or delete the word.
- Preserve source meaning. If alternatives or valid combinations cannot be determined, record exact gap as To Confirm and request focused clarification before claiming behavior.
- Scan again after deduplication. Any remaining match or failed manual scan blocks review and Jira processing. Record `manual-pass` in the later independent review report after passing.

Assign P0 to core accepted path, release blocker, permission boundary, legal/commercial rule, irreversible action, or integrity outcome; P1 to important negative, boundary, exception, dependency, notification, audit, or alternate path; P2 to source-supported low-risk/low-frequency/supplemental/usability/regression coverage. Deduplicate materially identical AC/FR, precondition, trigger, verification objective, and expected outcome unless distinct risk, partition, role, state, or result justifies another case.

`TEST_CASE.md` is user-visible. SPEC embeds executable content but omits tracking metadata. Never upload, attach, or reference standalone `TEST_CASE.md` in Jira.

## SPEC rules

Create one short meaningful lowercase kebab-case SPEC per Story. Its name appears identically in Story attachment, Test Case metadata, SPEC field, review reports, and Jira manifest. It follows `templates/spec-template.md`, begins with two-field `# Review Result`, embeds sibling Story exactly under `## Jira Story *(mandatory)*`, embeds executable test rows under `## Test Cases *(mandatory)*`, has evidence-supported Mermaid flow, uses `FR-###` and measurable `SC-###`, and reflects the coverage capability matrix without unsupported end-to-end claims.

User-facing artifacts expose no source paths, uploaded-document references, internal URLs, source registers, unresolved sections, or score records. Keep unresolved matters in internal review artifacts and request clarification if they block quality.

## Review-status synchronization

New SPEC starts `Not Reviewed`. While substantive issues remain, write actual normal values `NEEDS_REVISION` or `NEEDS_HUMAN_CLARIFICATION` after report. On substantive pass, reviewer issues `PASS` first; generation then writes PASS and notes into each SPEC header and verifies it. Do not rerun solely because header was stale or not PASS before verdict. `FINAL_WITH_UNRESOLVED` is only for explicit user instruction to stop without clean pass.

## Jira handoff

Clean review means Jira-ready, not created/updated. For new Stories with export capability, mark clean sets ready and invoke Jira skill only after every target passes, then continue missing Jira fields, Epic link, manifest preview, and final `request_user_approval`; do not pause for chat continuation. For updates, invoke export skill after every ticket has PASS, using reviewed attachment mapping and manifest; it owns diff preview, batch approval, and `java-base-mcp.updateJiraTicket`. Do not call Jira APIs directly. Without export capability, return reviewed artifacts as Jira-ready without simulating Jira action. Never upload without explicit final approval; info questions are not approval.

## Artifact production controls

### Candidate-local sequencing

Treat each Story candidate as an isolated production unit. Its only permitted sequence is: evidence-backed planning; current Story generation; one audited score invocation; verbatim score persistence; qualifying score decision; Test Case generation and validation; final SPEC generation; independent review; review-header synchronization; and, when applicable, separate Jira handoff. Do not interleave later artifacts from one candidate with an unqualified Story from another. A candidate that is waiting for evidence or technical recovery remains isolated; its state must not cause a batch-level pass or delay creation of unrelated, already-qualified candidates beyond normal serial workflow control.

Before writing any artifact, establish its complete workspace-relative destination and verify that it belongs to the candidate directory. Never use a temporary generic filename as a substitute for the final Story-named SPEC. Do not copy an artifact from a sibling candidate and edit it in place unless copied content is fully revalidated against the current candidate's evidence, naming, AC links, test rows, and score-gated state. Template structure may be reused; candidate facts and verification claims may not be inherited.

The Story must be generated from supported planning rows, not from a score response, previous review, or generic requirement pattern. It must identify a concise outcome, explain business context without leaking internal source locations, define granular scope, and state acceptance behavior in one combined Acceptance Criteria and BDD section. Acceptance criteria are commitments that can be observed: avoid vague words such as “works,” “appropriate,” “seamless,” or “as needed” unless the source defines observable meaning. Each AC must retain an ID through downstream artifacts unless supported re-decomposition supersedes it.

### Story content verification

Perform a pre-score Story check. Confirm that the Summary is actionable and not a duplicate of a project name; the user-story narrative identifies the actor, intended capability, and value; the scope distinguishes included behavior from unresolved matters; and each AC has evidence-supported conditions, action, and result. Confirm that acceptance criteria do not promise integrations, data retention, notifications, performance, permissions, or error behavior absent from evidence. If a dependency matters to release or testability, describe it as a dependency rather than asserting undocumented implementation behavior.

For every AC, identify the normal outcome and evaluate whether source evidence supports validation, rejected input, alternate state, boundary, dependency outage, empty result, permission, audit, notification, data consistency, or security coverage. This evaluation is not a mandate to invent every category. It is a deliberate decision to cover it, mark a precise gap, or state why it is not meaningful. Never place an explicit in-scope requirement into “Out of Scope” merely to keep a Story concise; split the work or request clarification instead.

The Story attachment reference is a Jira-facing filename only. It must name precisely one matching final SPEC and must never contain a directory, workspace path, internal review location, score record, Test Case filename, audit identifier, or download location. A revised SPEC filename requires synchronized changes to every internal artifact reference and later manifest entry before review.

### Score-gate operational safeguards

Immediately after the current Story is written, invoke `nodejs-base-mcp.score_requirement_markdown` exactly once for that version with the actual workspace-relative `.md` or `.markdown` path, `force_review: false`, and only non-sensitive metadata when metadata is supplied. Persist the complete returned JSON verbatim at the candidate's required internal `story-quality-score.json` location. Do not format, redact, summarize, overwrite, append to, or locally replace it. A cache hit is valid and does not justify a second call.

Only `ok: true` plus numeric `finalScore >= 76` permits creation of that candidate's downstream artifacts. No aggregate, average, status label, diagnostic dimension, reviewer preference, or Jira urgency can change this predicate. If the result cannot be used or cannot be persisted, first confirm that no Test Case or SPEC exists, then enter the relevant recovery state and request supported evidence, technical recovery, or explicit stop. Do not automatically improve or rescore unchanged content. A changed Story is a new version and receives its own sole invocation before it can advance.

Score records, returned identifiers, diagnostic fields, planning material, review reports, and Test Case files are internal. They must not appear in a Jira description, attachment list, export manifest content, user-visible Story, user-visible SPEC, or Jira payload. A reviewer may inspect them internally to validate the gate; it must not use them as a substitute for full-artifact review.

### Test design construction

Generate the Test Case only after the qualifying gate result. Build the internal design map before tabular cases. For each AC, state the test objective, entry state, supplied data, trigger, expected observable outcome, verification location, scenario type, priority, and negative coverage disposition. The map prevents a superficial test table that repeats AC prose without executable evidence.

Preconditions must establish what is required before the action, including role, known state, prerequisite record, configuration, or dependency condition where supported. Test data must be concrete enough to execute while avoiding invented format rules. Steps must identify actor action and system observation in a reproducible order. Expected results must be observable and linked to the AC or functional requirement. A verification location may be a visible screen, returned state, recorded audit result, notification destination, or other supported observation point; do not name an internal table, queue, endpoint, or log when it is not supplied evidence.

Maintain exactly one negative disposition for every AC. Covered means an executable negative case exists for a source-supported rejected, prohibited, or failed behavior. N/A means no meaningful negative behavior is supported and carries a concise rationale on the Positive case. To Confirm means a meaningful negative outcome is missing and states the exact unresolved behavior. Do not use N/A as a shortcut for unexamined validation, and do not create a fictional negative test merely to avoid a clarification.

### Alternative-title validation

The Test Case Description is a final business scenario title, not an informal note. Scan every non-empty description for standalone case-insensitive `or` before finalizing the table and again before embedding it in the SPEC. A match indicates alternatives that need separate, independently executable cases when combinations are supported. Do not evade the check by substituting punctuation, a slash, “and/or,” or a vaguer phrase.

For each split result, change more than the title: update the ID, precondition, selected data, steps, expected result, references, priority, and negative disposition so the case represents the actual alternative. Generate the Cartesian combinations only where the source supports the combinations. If the allowed combinations or their outcomes are unknown, record the gap and request targeted evidence. After deduplication, the manual recheck must have no remaining standalone match. Until then, do not create a SPEC, invoke review, or prepare Jira material.

### SPEC assembly and alignment

Select the final short lowercase kebab-case SPEC filename before writing it. The file must follow the active template, start with the two-field review result header, and embed the exact current sibling Story beneath the mandatory Jira Story section. “Exact” means content-equivalent to the reviewed current Story, including its current attachment reference, not an earlier draft, a shortened paraphrase, or a version with hidden source annotations. Embed executable Test Case content under the mandatory Test Cases section while omitting tracking metadata that does not belong in user-facing material.

Write functional requirements with stable `FR-###` identifiers and measurable success criteria with `SC-###` identifiers. Tie them to supported behavior and preserve their relationships to Story ACs and test cases. Mermaid flows may illustrate supported journeys, states, and decisions; they must not introduce unpublished integrations, retries, storage, roles, or alternate paths. The SPEC must reflect the coverage matrix honestly. When only frontend evidence exists, describe the verified behavior without claiming end-to-end system confirmation.

Before independent review, run hygiene checks: filenames agree across Story, Test Case, SPEC, manifest, and internal trace; the Story reference names only its SPEC; the SPEC embeds the current Story exactly; AC, FR, and test links resolve; the current score record is complete and qualifying; and the review header contains the correct pre-review state. These deterministic corrections are platform hygiene, not a substitute for source clarification or substantive review repair.

### Review and handoff boundaries

The independent review skill is read-only. The generator supplies the complete artifact set and evidence context, receives one normal-vocabulary verdict, and writes the returned report to the internal location. Do not treat a stale SPEC header as the verdict. Synchronize the header after the review report, and retain the normal values `PASS`, `NEEDS_REVISION`, `NEEDS_HUMAN_CLARIFICATION`, and `FINAL_WITH_UNRESOLVED`. A clean PASS is required before Jira-ready status; it still does not create, update, attach, or approve anything in Jira.

Track review attempts and the lifetime self-repair count per artifact set. During the first three attempts, one consolidated self-repair batch may be applied per attempt only when the finding is supportably repairable from existing evidence. Re-run affected deterministic gates and obtain a fresh review of the reused artifacts. Human-evidence findings require an immediate focused question; do not consume repair attempts trying to infer an answer. From attempt four onward, do not self-repair review findings: request the specific user evidence, correction, file, or decision and regenerate the affected artifacts before the next review.

After every Jira-targeted artifact set reaches PASS, use only the separate approval-controlled Jira capability for handoff. Provide the approved Story description and selected matching SPEC according to its contract. Never call Jira APIs directly from this workflow, never attach the standalone Test Case or internal score evidence, and never interpret conversation assent as final approval. If export capability is unavailable, report reviewed artifacts as Jira-ready without simulating a write.
