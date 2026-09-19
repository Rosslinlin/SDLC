---
name: ceaia-sdlc-story-spec-generation
description: Generate or update granular Jira-ready CEAIA Stories, apply the mandatory evidence-only score gate, create TEST_CASE.md and story-named SPEC artifacts, run independent review, and automatically hand clean results to the bundled Jira export or update gate.
---

# CEAIA Story and SPEC Generation

## Responsibility

Own source intake, planning, Story decomposition, Story generation and audited scoring, Test Case and SPEC generation, review repair, and post-review handoff.

Do not push to Jira directly. Use `ceaia-spec-review` for independent review and `ceaia-sdlc-only-jira-push-content` for Jira writes.

## Mandatory first Jira gate

If any supplied Jira ticket lacks an explicitly user-supplied source, the immediate next action must be `ask_user_question`. Show one independent selector per unresolved ticket with exactly `WPB`, `ALM`, `DATA`, `FCR`, and `GO`; then wait.

Do not infer a value, use a shared default, request a Jira URL instead, ask unrelated intake questions, or call any Jira tool before this gate is complete.

Required popup content:

```text
Problem: Jira source is required before ticket lookup.
For each unresolved ticket:
<ticket-key> source: [WPB | ALM | DATA | FCR | GO]
Action: confirm sources or explicitly stop.
```

Required source-error popup content:

```text
Error: <exact tool error>
Ticket: <key>
Confirmed source: <source>
Choose: correct ticket key | correct source | retry same mapping | stop
```

## Progressive reference loading

Read only the references needed for the current phase:

- For Jira or Confluence enrichment, read `references/source-enrichment.md`.
- For an explicit existing Jira ticket update, read `references/existing-jira-update.md` before planning or editing.
- When planning or scoring Stories, read `references/planning-and-scoring.md`.
- When generating Story, Test Case, SPEC, or pairing review findings, or handing off to Jira, read `references/artifact-generation-rules.md`.
- Read templates only when producing or validating that artifact:
  - `templates/planning-template.md`
  - `templates/story-template.md`
  - `templates/test-case-template.md`
  - `templates/spec-template.md`

The sole evaluator is `nodejs-base-mcp.score_requirement_markdown`. It evaluates the generated workspace `STORY.md` directly. Do not read, retain, create, or use a local evaluator rubric, output schema, calibration fixture, evaluator-input adapter, scoring implementation, or manual score calculation. The tool is invoked only when the current Story version has been fully written and is ready for its mandatory audited score gate. Do not preload every reference at task start.

## Resource manifest

Every bundled resource is declared below. Read the phase-specific resource before acting; do not rely on memory or infer an unlisted template.

- Intake and generation references:
  - `references/source-enrichment.md`
  - `references/existing-jira-update.md`
  - `references/planning-and-scoring.md`
  - `references/artifact-generation-rules.md`
- Artifact templates:
  - `templates/planning-template.md`
  - `templates/story-template.md`
  - `templates/test-case-template.md`
  - `templates/spec-template.md`
- External audited evaluator:
  - `nodejs-base-mcp.score_requirement_markdown`
  - Input: one workspace-relative `STORY.md` path and `force_review: false`
  - Output: the complete returned JSON, retained verbatim as internal audit evidence

## Request mode

Classify before generation:

- `new_story`: create one or more new CEAIA Story/SPEC artifact sets.
- `existing_ticket_update`: update supplied existing Jira tickets and related SPEC attachments.

Never create a new Jira Story for an explicit existing-ticket update.

For an update, treat each ticket as an independent target. Do not merge ticket descriptions, requirements, attachments, manifests, reviews, score records, or Jira payloads.

## Jira source confirmation

Before calling any Jira read tool, require the user to explicitly supply or confirm the source for each ticket: `WPB`, `ALM`, `DATA`, `FCR`, or `GO`.

Never infer or default Jira source from a ticket prefix, project key, prior tool behavior, organisation context, or another ticket. A ticket that looks like `AIWPB-*` still requires explicit source confirmation.

When one or more tickets lack a source, invoke one `ask_user_question` popup listing every unresolved ticket with one independent constrained source selector per ticket. Different tickets may belong to different sources; do not apply a shared default. Do not call `java-base-mcp.getJiraInfos` or `java-base-mcp.readJiraAttachments` for a ticket before its source is confirmed.

If a Jira tool returns not-found or another source-related error, display the exact error verbatim in an `ask_user_question` popup and ask the user to reconfirm or correct that ticket's key and source. Do not request a URL as a substitute and do not automatically try another Jira source.

Apply this low-freedom gate:

```text
IF any Jira ticket has no explicit source:
  NEXT ACTION = invoke ask_user_question
  POPUP = one independent selector per unresolved ticket
  OPTIONS = WPB | ALM | DATA | FCR | GO
  DO NOT ask unrelated questions in this popup
  DO NOT call a Jira tool
  WAIT for the response

IF a Jira read returns not-found or a source-related error:
  DISPLAY the exact tool error verbatim in the popup
  NEW ACTION = invoke ask_user_question
  INCLUDE the ticket key and currently confirmed source
  OFFER correct ticket key | correct source | retry same mapping | stop
  DO NOT request a Jira URL as a substitute
  DO NOT try another source automatically
```

## Existing Jira field compatibility

For every update ticket, request Jira description and AcceptanceCriteria in separate `java-base-mcp.getJiraInfos` calls.

An empty, null, unavailable, not-found, or field-level error result for AcceptanceCriteria is not by itself a blocker when description was retrieved successfully. Some Jira Stories store Acceptance Criteria inside the description.

When the separate AcceptanceCriteria result is empty but description is non-empty:

1. Record the separate field status as `empty-or-unavailable`.
2. Inspect the complete description for embedded Acceptance Criteria, BDD scenarios, numbered conditions, or equivalent acceptance statements.
3. Use the description as the authoritative Jira baseline.
4. Continue attachment intake and planning even when no separately identifiable Acceptance Criteria are present in the description.
5. Do not report the empty separate field as a Jira read failure or ask the user to repair it.

For Jira field intake, block only when the description is also unavailable. Any genuine requirement-content gap discovered later must follow the normal score or review outcome instead of being treated as an AcceptanceCriteria retrieval failure.

## Deliverable contract

For every Story:

```text
STORY.md -> audited score record -> TEST_CASE.md -> story-named SPEC -> independent review
```

New Story paths:

```text
outputs/ceaia/<story-slug>/STORY.md
outputs/ceaia/<story-slug>/TEST_CASE.md
outputs/ceaia/<story-slug>/<story-name>.md
.ceaia-work/stories/<story-slug>/story-quality-score.json
```

Existing update paths:

```text
outputs/ceaia/updates/<TICKET-KEY>/STORY.md
outputs/ceaia/updates/<TICKET-KEY>/TEST_CASE.md
outputs/ceaia/updates/<TICKET-KEY>/<story-name>.md
.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json
```

`STORY.md` is the Jira description. The story-named SPEC is the Jira attachment. `TEST_CASE.md`, planning, score records, and review files are never exported to Jira.

## Audited Story score-tool gate

The current generated or regenerated `STORY.md` is the direct input to `nodejs-base-mcp.score_requirement_markdown`. Immediately after writing that Story version, and before creating any `TEST_CASE.md` or SPEC for the same candidate, invoke the tool exactly once:

```json
{
  "relativePath": "<workspace-relative path to this STORY.md>",
  "force_review": false
}
```

`relativePath` must be a complete workspace-relative path only. Never supply an absolute path, URI, URL, copied Markdown body, conversation ID, path containing `..`, local evaluator input file, or alternate representation. `force_review` must be boolean `false`.

For multiple Stories, process candidates serially and make one tool invocation for each generated or regenerated Story version. One Story means one invocation; three Stories mean three invocations. Do not batch candidates, use one Story's result for another, aggregate or average results, or skip a Story because an equivalent assessment was previously cached.

A cache hit is the valid outcome of that one required invocation. Do not make a second invocation to refresh, confirm, improve, compare, diagnose, or retry the same Story version. A changed Story is a new version: update the evidence and planning trace, regenerate the Story, and make exactly one invocation for that new version. Do not preserve, transfer, or reuse its prior score, checksum, review ID, audit ID, or pass decision.

Persist the complete JSON returned by the tool verbatim before evaluating the gate:

- new Story: `.ceaia-work/stories/<story-slug>/story-quality-score.json`
- existing update: `.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json`

Do not format, pretty-print, minify, sort keys, trim, redact, extract, summarize as a replacement, append to, reconstruct, or overwrite the returned JSON. It is internal audit evidence only and must retain all returned fields, including `trace`, `checksum`, `score`, `diagnostic`, `issue`, `warning`, `strength`, and `detail` fields when present.

The only pass predicate is:

```text
ok === true AND finalScore >= 76
```

`ok` must be boolean `true`; `finalScore` must be numeric. `75` does not pass. Do not substitute `finalStatus`, title, quality label, score dimension, score breakdown, reviewer judgment, a legacy score, or an aggregate/average result for either condition.

If the result passes, record the candidate's internal gate decision and continue to its Test Case. If the result is non-qualifying, malformed, unavailable, unparseable, stale, mismatched to changed Story content, or lacks a usable `ok`/`finalScore` determination, verify that no downstream artifact exists for that candidate. Enter the applicable waiting state and use `ask_user_question` for supported corrected source material, correction, Story evidence, technical recovery, or explicit stop. Do not invent a result, manually calculate a score, auto-rewrite or re-score unchanged evidence.

Score records, audit IDs, review IDs, checksum, diagnostics, planning evidence, and evaluation outputs stay internal. Never embed, attach, link, quote, or copy them into a Jira description, Jira attachment, `STORY.md`, `TEST_CASE.md`, SPEC, Jira manifest, or Jira payload.

## Mandatory sequence

1. Inventory all supplied requirement, UX/UI, FSD, API, data, Jira, Confluence, attachment, and workspace evidence.
2. For an update, complete the update intake and read exactly one complete description plus each separate AcceptanceCriteria field per ticket.
3. Complete the planning gate and decide the evidence-supported Story count.
4. Process candidates one at a time. Generate only the current candidate's `STORY.md`; do not precreate, stub, reserve, or copy its `TEST_CASE.md` or SPEC.
5. Immediately invoke the score tool once for that Story and persist the entire JSON response verbatim. Do not start another artifact for that candidate before the score gate is resolved.
6. Only when `ok === true` AND `finalScore >= 76`, generate that candidate's `TEST_CASE.md`, complete the manual Test Case Description split check, and generate its matching SPEC.
7. Run `ceaia-spec-review` in a fresh read-only subagent against actual files, including the persisted score record.
8. Resolve the review outcome according to the review loop below.
9. After every target artifact set has PASS, synchronise each SPEC header and immediately invoke `ceaia-sdlc-only-jira-push-content`.

Do not ask the user to type `continue`, confirm continuation, or separately request permission to begin Jira preparation after review passes. Proceed automatically to Jira field collection, preview, and the mandatory request_user_approval write gate.

## Hard gates

- A score fails unless `ok` is true and numeric `finalScore` is at least 76. Request additional or corrected source material; do not auto-rewrite and re-score from the same evidence.
- Do not generate or leave behind `TEST_CASE.md` or SPEC for a failed, unvalidated, malformed, unpersisted, stale, or non-qualifying Story score result.
- No final Test Case Description may contain standalone `or`, case-insensitively. Split supported alternatives and conditions as required by the detailed rules.
- Do not start Jira preparation until every Jira-targeted artifact set has an independent PASS.
- Never bypass final Jira approval.

## Independent review loop

Maintain two counters independently for each artifact set:

- `reviewAttempt`: starts at 1 and increases without a maximum.
- `aiSelfRepairCount`: starts at 0, has a lifetime maximum of 3, and never resets after user input.

- Review attempts 1, 2, and 3 may each use at most one consolidated AI self-repair batch.
- After a PASS on any attempt, mark that artifact set Jira-ready. Continue automatically to Jira handoff only after every Jira-targeted artifact set has PASS.
- During attempts 1 through 3, if only Self-fixable findings remain, apply one consolidated repair batch, increment `aiSelfRepairCount`, rerun affected gates, and run a fresh post-repair verification within the same attempt. If verification still fails before attempt 3, continue to the next attempt automatically.
- If human evidence is required on any attempt, invoke `ask_user_question` immediately. Do not wait for three attempts.
- If attempt 3's post-repair verification still fails, invoke `ask_user_question`; do not perform an AI self-repair.
- Review attempt 4 and later are user-driven. Do not self-repair Story, Test Case, or SPEC findings. Report the exact unresolved problems and required user information, wait for the response, regenerate the affected artifacts from that response, and run the next numbered review.
- Before every review, perform deterministic platform hygiene without consuming `aiSelfRepairCount`: use only the SPEC filename in Jira-facing Story attachments, synchronise embedded Story content, align paths and filenames, confirm score-record correctness, and normalise stale review headers.

`ceaia-spec-review` must verify the persisted score record only. It must not invoke the scoring tool, request a second call, refresh a cache, recalculate a score, or create a replacement record. A valid score record is a mandatory gate but never substitutes for source coverage, decomposition, Story quality, test executability, SPEC alignment, update preservation, or Jira readiness. Stop only when explicitly told stop. Do not set `FINAL_WITH_UNRESOLVED` based on attempt count.

## Popup recovery and completion

Use `ask_user_question` for every missing input, ambiguity, source gap, tool failure, score failure, clarification verdict, human-evidence review checkpoint, attachment decision, Jira correction, retry decision, or stop decision. The popup must state the problem, its current impact, and the exact file, information, or choice needed. Plain chat must not be the only blocking prompt.

Whenever reporting an artifact problem, use its complete workspace-relative path, including its story or ticket directory. Add a line number or line range when known, for example `outputs/ceaia/payment-review/STORY.md:42` or `outputs/ceaia/updates/ABC-123/TEST_CASE.md:31-34`. Never report only a bare filename or an unqualified line number.

For a score-related waiting state, identify the complete Story path and the intended or actual score-record path. When available, provide the returned `finalScore`, `finalStatus`, relevant `criticalIssues`, `warnings`, `dimensions`, and `details` as internal diagnostic context; do not fabricate unavailable fields or expose the full score record in Jira-facing artifacts.

Treat waiting input, score failure, score-tool recovery, review clarification, any numbered non-PASS review, rejected approval, unavailable approval tooling, Jira partial failure, Jira failure, and blocked payloads as recoverable active states. Resume from the earliest affected gate after the user responds.

Complete the bundled workflow only when:

- every intended Jira create or update operation succeeds; or
- the user explicitly chooses stop or cancel.

Relative paths in this skill are relative to the skill directory.

Use workspace root name `ceaia-sdlc-story-spec-generation` and the referenced relative path.

Use workspace tools only for files in the conversation workspace.

Note: file list is sampled.

<skill_files>
<file>README.md</file>
<file>references/artifact-generation-rules.md</file>
<file>references/existing-jira-update.md</file>
<file>references/planning-and-scoring.md</file>
<file>references/source-enrichment.md</file>
<file>templates/planning-template.md</file>
<file>templates/spec-template.md</file>
<file>templates/story-template.md</file>
<file>templates/test-case-template.md</file>
</skill_files>
