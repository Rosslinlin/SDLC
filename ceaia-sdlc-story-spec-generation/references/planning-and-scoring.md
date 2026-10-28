# Planning and scoring

Read workflow-contract.md for latest-output paths, retained internal history, invalidation and repair budget. Read [business-planning-checklist.md](business-planning-checklist.md) before planning and [scoring-tool-contract.md](scoring-tool-contract.md) only at the post-review scoring step.

## P1 — Evidence-backed planning

Use templates/planning-template.md at the next unused `plan-revision-<nnn>.md` planningPath. Never overwrite a completed planning revision; state points to the latest applicable plan. Inventory requirements, designs, FSD/API/data contracts, Jira fields/attachments, Confluence and clarifications. Classify direct requirement, UX evidence, implementation constraint, background or supported exclusion. Keep exact source locations internally. Background is not automatically scope.

Each explicit requirement gets SR-### and Evidence ID, actor/trigger, action/rule, observable outcome, journey/state and candidate disposition. Every SR is covered, waiting for clarification or explicitly excluded by source/user decision. Missing detail is not exclusion.

Coverage per journey/layer uses Confirmed, Not provided or To confirm for UI, API/FSD, backend, integration/async, persistence, downstream/reporting and permission/security. Frontend evidence does not prove end-to-end behavior. Inventory supported UX states and transitions; do not infer storage or backend rules from screenshots.

New Stories are independently testable value slices. Split independent outcomes/ownership where warranted, retaining relevant error/validation behavior with its primary outcome. Multiple ACs alone do not require a split. Existing updates preserve baseline scope; do not force new tickets solely to fix inherited broad sizing. Record inherited limitations internally and clarify if the requested delta requires rescoping.

Before generation actor, trigger, primary outcome and material rules must support executable ACs. Material uncertainty blocks the affected candidate. Non-applicable layers require rationale, not invented behavior. Decide unique paths, final SPEC name and attachmentAction now.

Complete source→SR→candidate mapping before generation, extending to AC→FR→TC after artifacts exist. Reconcile the original evidence against this register across the final batch; per-Story PASS alone does not establish global coverage.

## S1 — Invoke evaluator after independent content review

Only `nodejs-base-mcp.score_requirement_markdown` evaluates quality. No local rubric, evaluator adapter, scoring model or manual score. Read-based interface checks and observed revision tracking do not replace scoring.

Call the tool in Workspace mode with three actual values: `relativePath` is the full workspace-relative final `STORY.md` path; `specFileStatus` is exactly `Pass`, mapped from the verified primary SPEC `Review Result` / `Result: PASS` (never send `PASS` or `pass`); `specFilePath` is that SPEC's full workspace-relative path. For updates, use the modified Workspace Story even though a Jira key is known. Never combine relativePath with jira_number/source. Omit optional force_review and metadata by default. Follow scoring-tool-contract.md for the live schema, documented single JSON text block and explicit user-authorized freshness exception.

Before invocation:
1. Story, Test Case and every intended SPEC are complete. Current independent content-review PASS has no unresolved finding; the primary SPEC Result header is synchronized to that review and its complete file has been read back. Global coverage and filename/attachment mappings are current.
2. Read back the saved Story and primary SPEC; record storyRevision, primary SPEC fileRevision, reviewAttempt, source `Result: PASS`, submitted `specFileStatus: Pass` and both paths. Check exactly one standalone `## Acceptance Criteria` heading with distinct evidenced Given/When/Then declarations. A combined AC/BDD heading is not canonical.
3. Resume from a current complete score only if it is associated with this exact Story and verified current SPEC status/path/revision. A pre-change score without final SPEC metadata does not qualify; make one metadata-bearing call and allow normal cache reuse. Do not copy a sibling's result.
4. During the call keep the submitted Story and SPEC unchanged. Real Story changes require a new assessment. A SPEC-only change may reuse the content score but needs a new invocation with current SPEC metadata; never use force_review to refresh metadata.

## S2 — Persist complete response and bind currentness

Allocate one new monotonically numbered Markdown scoreRecordPath for every actual tool invocation. A later assessment never replaces a completed earlier record; state points to the latest applicable attempt. Preserve all returned fields, including optional identifiers and diagnostics.

- Documented MCP result: extract the single JSON text content block; use `templates/score-record-template.md` to create a readable Markdown record and persist the entire JSON text verbatim, unedited, in its fenced block. The MCP envelope is not the score object.
- Only a parsed object available: serialize the complete designated evaluation object once inside the Markdown raw-response block; set persistenceMode=complete-object-json-in-markdown. Do not claim server-byte preservation.
- Follow the tool's documented evaluation channel. Conflicting envelopes or undocumented wrapping mean WAITING_TOOL; do not pick a favorable nested score or reconstruct from partial logs.

Record the input storyRevision, primary SPEC path/fileRevision/status, reviewAttempt, assessmentOrdinal, responseReadBack and actual invocation reference when exposed. Read back the saved response and confirm Story and SPEC remained unchanged during the call using the shared text-only procedure. Preserve returned service checksums without calculating replacements. No checksum is required locally; unknown actual invocation provenance still requires recovery.

The record's readable summary must agree with the raw JSON but is not evaluator evidence by itself. State metadata is separate from the raw response. Score records, audit IDs and diagnostics never enter Story, Test Case, SPEC, update manifest, Jira preview or payload. The sole public score exception is the actual numeric total in the designated SPEC header; finalize it and the primary SPEC revision under [SPEC score display](spec-score-display.md) before readiness. Never copy the raw response or score-record path into that header.

## S3 — Gate and recovery

Pass requires a parseable complete current response, successful persistence, boolean `ok === true`, finite JSON number `finalScore >= 76` within 0–100, and resolved parser integrity. Numeric strings, booleans as numbers, NaN/Infinity, labels and averages fail. Check parseErrors, the missing-AC-section warning and any returned AC counts per scoring-tool-contract.md. A high score cannot override incomplete parsing, missing evidence or later review findings.

| Outcome | Action |
|---|---|
| Pass | Mark score current only if SPEC metadata and content-review binding are current; then enter Jira preparation when all candidates pass |
| Valid non-pass; supported writing defect | Repair from existing evidence within shared budget; independently recheck affected content, synchronize SPEC and score final Story |
| Missing AC section warning/count mismatch | Check canonical heading and declared IDs; repair format only if wrong, otherwise technical recovery; no Jira readiness |
| Missing/conflicting business fact | WAITING_USER; do not invent scope |
| Non-pass with no supported correction | Pause for evidence/decision; no unchanged rescore or premature Jira write |
| Complete response but save failed | Retry saving that response, not scoring |
| Failure confirmed before evaluation started | At most one technical retry after correction, recorded in state |
| Timeout/disconnect with unknown result | Recover/query existing result only if live API supports it; otherwise WAITING_TOOL, no blind replay |
| Malformed response/unknown wrapping or required checksum semantics | WAITING_TOOL for technical resolution |
| Story changed while scoring | Result is stale; reconcile intended content and assess it |

Technical recovery is at most one retry per operation/cause after correction; further attempts require explicit finite recovery authorization. Do not set force_review=true to avoid cache or low score. Only an explicit user request for a fresh same-content assessment can use the documented freshness exception; invalidate dependent readiness first.

Genuine evidence-supported Story correction may be rescored without new user evidence. Cosmetic changes solely to solicit a better score may not. Diagnostics use actual returned fields and full affected paths. No downstream readiness while score is invalid.
