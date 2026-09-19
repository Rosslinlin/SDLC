# Existing Jira update intake

Use this flow when user supplies existing Jira keys and asks to update tickets, descriptions/summaries, attached SPECs, or all of these with added requirements. Each ticket is an independent target: never merge descriptions, attachments, requirements, or payloads.

This is highest-priority workflow for an explicit update request, before generic enrichment, Story generation, attachment questions, summary questions, review, or update preparation. Reading attachments is mandatory for every target, including apparent summary/description-only changes and zero-attachment tickets.

## `java-base-mcp.readJiraAttachments` contract

Call exactly once per target ticket before selecting/editing any attachment:

```json
{
  "ticketKey": "<TICKET-123>",
  "source": "WPB|ALM|DATA|FCR|GO",
  "conversationId": "<current conversation ID when required by runtime>"
}
```

Process every attachment, including imported and skipped records. Read imported text from returned workspace-relative `relativePath`; content is not returned directly. Preserve attachment ID, filename, MIME type, size, import status, skip reason, and path in internal manifest. Never use download URL, token, absolute path, guessed ID, or `..` path. Tool errors save UTF-8 text only; a potentially relevant skipped attachment is unresolved evidence.

If unavailable, failed, or incomplete, block that ticket: tell user attachment reading could not complete and ask for readable content or resolution. Never replace an attachment or ask user to manually give an ID as fallback.

For each target:

1. Require explicitly supplied/confirmed source `WPB`, `ALM`, `DATA`, `FCR`, or `GO`; never infer/default from key, project, context, prior ticket/call. If any missing, one `ask_user_question` lists all unresolved tickets with independent constrained selectors. Preserve not-found/source errors, ask to reconfirm key and source, and never probe sources automatically.
2. Read `description` and `AcceptanceCriteria` in separate `java-base-mcp.getJiraInfos` calls.
3. Call attachment reader with key/source/current conversation ID where runtime requires, before planning, attachment question, SPEC generation, review, preview, or update payload. Read complete result, not only Markdown.
4. Use every imported readable text attachment as evidence. Record each non-imported attachment and reason in manifest; do not assume binary, oversized, or invalid UTF-8 means irrelevant.
5. Preserve returned attachment ID, display name, MIME type, and imported path for each imported attachment; these are required to safely replace it.

AcceptanceCriteria is optional evidence, not mandatory non-empty data. When description is non-empty and separate call is empty/null/unavailable/not-found/field-error, record `AcceptanceCriteria` retrieval status `empty-or-unavailable`; inspect complete description for embedded AC, BDD, numbered conditions, or equivalent; use description as authoritative baseline and continue. Do not treat that combination as intake failure or request repair of separate field. A non-empty description completes field intake even with no identifiable AC; handle later quality gaps through audited Story scoring or independent review. If description is unavailable, stop and request it.

If update depends on skipped/unreadable attachment, ask user for readable content or handling clarification; never fabricate requirements.

## Existing Jira update artifact rules

Keep imported user-visible deliverables and internal work separate per ticket:

```text
outputs/ceaia/updates/<TICKET-KEY>/STORY.md
outputs/ceaia/updates/<TICKET-KEY>/TEST_CASE.md
outputs/ceaia/updates/<TICKET-KEY>/<short-story-based-spec-name>.md

.ceaia-work/updates/<TICKET-KEY>/intake/jira-description.md
.ceaia-work/updates/<TICKET-KEY>/intake/acceptance-criteria.md
.ceaia-work/updates/<TICKET-KEY>/plan.md
.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json
.ceaia-work/updates/<TICKET-KEY>/review-attempt-01.md
.ceaia-work/updates/<TICKET-KEY>/update-manifest.md
```

Do not create an evaluator adapter or local evaluator artifacts. For every generated/regenerated update `STORY.md`, invoke `nodejs-base-mcp.score_requirement_markdown` exactly once with its workspace-relative `relativePath` and `force_review: false`. Persist whole returned JSON verbatim at shown internal score path. The Story proceeds only if `ok` is true and `finalScore >= 76`; never average, reuse a previous assessment for changed Story content, or put score output in Jira. A changed Story is a new assessment version and receives its one new invocation.

Keep imported readable attachments exactly at returned paths; do not move/rename them for this layout. They remain canonical evidence. Write reviewed replacement SPEC under `outputs/ceaia/updates/<TICKET-KEY>/`; that is later payload file. Internal area holds intake, planning, score evidence, manifest, and review evidence.

Build `update-manifest.md` before editing. Record ticket key and user-confirmed source; original description and separate AC retrieval statuses (including `empty-or-unavailable`); incremental requirements; imported inventory/IDs/names/MIME/paths/skips; summary change only if explicitly supplied or reliably evidenced; proposed description; replacement mapping or explicit unresolved decision; and concise baseline-to-proposed change summary.

Never infer current Jira summary: `getJiraInfos` does not provide it. Update `summary` only with user-supplied replacement or reliable current-request source; otherwise omit it.

Use all readable attachments as evidence even if not replacement target. A non-CEAIA text attachment may become a CEAIA story-named SPEC when user requests corresponding SPEC update; preserve ID and replacement only after review and approval.

Attachment selection is a blocker, never inference:

- If exactly one readable attachment is clear current SPEC, propose it.
- If several can be SPECs, including two or more spec-like files, present filename options with MIME type and concise evidence-supported purpose; retain returned ID as internal option value.
- Never ask user to key/know/look up an ID. User selects filename(s); use already returned IDs.
- For multiple replacements, establish explicit one-to-one reviewed-file mapping. Never map multiple attachments to one file absent explicit confirmation.
- If no readable attachment is safely selectable, ask which existing attachment to replace or for missing source. Never silently add, delete, or replace one.
- Migrate a readable non-CEAIA attachment to CEAIA SPEC rather than preserving obsolete format when user requests corresponding update. Preserve original attachment ID for eventual selected replacement, but do not preserve obsolete format as user-facing deliverable.

Do not update priority, assignee, labels, custom fields, or other Jira fields. Never delete attachments or create new Jira tickets as implicit response to update request.

## Per-ticket intake ledger

Create and maintain a separate internal intake ledger for each ticket before any proposed content is generated. The ledger is authoritative record of what was read, unavailable, user requested, and what can safely be changed. It must contain supplied ticket key, explicitly confirmed source, description retrieval outcome, separate Acceptance Criteria retrieval outcome, attachment-reader outcome, readable attachment inventory, skipped attachment inventory, incremental requirements, proposed update scope, and all pending decisions. Never combine ledgers across tickets, even where tickets have similar descriptions or attachments.

Store original description and separately retrieved Acceptance Criteria as internal evidence at prescribed intake paths. Record retrieval state precisely: returned, empty, unavailable, not found, field-level error, or tool failure. Preserve full description as baseline when retrieved successfully. A blank or unavailable separate Acceptance Criteria field does not authorize deletion of acceptance content embedded in description and does not permit a generic replacement description that discards existing baseline.

Every readable attachment is evidence whether or not eventually selected for replacement. Read it at returned workspace-relative path and record attachment ID, display name, MIME type, size, import status, path, and purpose as far as evidence supports. A skipped attachment is not automatically irrelevant. Its skip reason must be retained, and a potentially relevant skipped file requires either user-provided readable content or an explicit safe handling decision before a change that depends on it. Never use an external download location, raw URL, guessed attachment ID, or manually supplied ID to bypass attachment-reader contract.

## Source confirmation and retrieval failures

The source selector is a required user-controlled safety input. For every target, obtain explicit value from permitted source set before reading ticket data. Do not infer it from ticket key, project prefix, prior ticket, organization convention, issue content, or successful call made for another ticket. Where multiple tickets lack source, ask one focused question that lists each separately and provides independent constrained selector. Preserve any not-found or source-specific error in ledger and ask for confirmation of key and source rather than probing alternative sources.

If description retrieval fails or produces no usable description, stop work for that ticket and ask for missing baseline or correction. Do not use prior generated Story, visible title, attachment name, or incremental request as substitute for current description. If separate AcceptanceCriteria retrieval is empty or unavailable but description is available, examine description for numbered criteria, BDD statements, conditions, constraints, or equivalent acceptance language. Mark ledger `empty-or-unavailable`; do not treat ticket as fully evidence-free and do not ask user to repair field that is optional for this workflow.

If attachment reading is unavailable, incomplete, or fails, block only affected ticket. Explain technical condition using ticket-specific complete workspace-relative intake location where applicable and request readable content or resolution. Never prepare replacement mapping, update payload, or selected attachment decision until required attachment inventory has been obtained. A zero-attachment result is still a completed reader invocation and must be recorded as such.

## Safe change-scope analysis

Compare user's requested increment with baseline description, separate acceptance evidence, and every readable attachment. Classify proposed change as preserved baseline, supported addition, supported clarification, unsupported inference, or hard conflict. Proposed Story is replacement description constructed from preserved baseline plus supported incremental requirements; it is not permission to rewrite ticket as fresh Story that loses existing obligations.

Preserve behavior, constraints, roles, acceptance outcomes, dependencies, and attachment-derived requirements unless explicit user instruction and sufficient evidence change them. When source statements conflict, expose conflict in internal ledger and ask focused question. Never choose an interpretation merely because it makes proposed description shorter, more modern, or easier to test. If increment applies only to one state, role, or attachment requirement, constrain it accordingly rather than generalizing it across ticket.

Summary changes are exceptional. `getJiraInfos` does not provide trustworthy current summary for this workflow, so do not infer or silently replace it. Include summary field only if user provides exact new summary or reliable current-request evidence explicitly supports it. Otherwise leave summary out of intended update. Do not update priority, assignee, labels, custom fields, status, links, components, or other fields merely because new Story mentions them.

## Update Story and scoring sequence

After intake, planning, and safe scope analysis, write proposed update Story at `outputs/ceaia/updates/<TICKET-KEY>/STORY.md`. Generated Story must preserve evidenced ticket baseline and distinguish supported new behavior without exposing source paths, attachment IDs, score data, or internal planning notes. It must follow normal Story structure, use stable AC identifiers, and name only final matching SPEC as Jira-facing attachment reference. Do not create a Test Case, SPEC, placeholder, or tentative attachment mapping before Story has passed audited score gate.

Immediately invoke `nodejs-base-mcp.score_requirement_markdown` exactly once for generated or regenerated Story version. Supply actual workspace-relative Story `.md` or `.markdown` path, set `force_review` to boolean false, and include only optional non-sensitive metadata if useful. Metadata may identify workflow, update mode, ticket key, and artifact type; it must not include secret, credential, token, conversation identifier, raw ticket description, raw attachment content, raw URL, absolute path, or instructions intended to influence assessment.

Persist complete returned JSON verbatim at `.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json` before evaluating it. Do not format, trim, append, redact, extract, construct a summary as substitute, or update audit fields. Current update version proceeds only when `ok` is true and `finalScore` is numeric and at least 76. A cache hit is valid result of required one call. Do not request fresh assessment merely to avoid cache hit, and do not use second call to challenge low result.

If returned record is missing, malformed, unpersistable, stale, mismatched to changed content, `ok: false`, nonnumeric, or below threshold, block ticket before Test Case, SPEC, review, mapping, preview, or payload preparation. Verify no downstream artifact exists. Request only supported corrective inputs: additional or corrected source material, corrected Story evidence, technical recovery, or explicit stop. State complete Story path, intended or actual score-record path, returned values only when present, relevant diagnostic evidence, and technical failure when applicable. Do not ask user to calculate a score, alter an audit record, choose a score, or manually repair service state.

## Manifest construction and attachment mapping

Build update manifest before any Jira edit. It is an internal preservation record, not a Jira payload. Include ticket key; confirmed source; baseline retrieval outcomes; source evidence locations; incremental requirements; attachment inventory and skip reasons; selected replacement candidate or unresolved mapping decision; proposed description; summary decision; current Story path; score-record path; review status; and concise baseline-to-proposed change summary. Include returned audit, cache, checksum, status, and score values only when tool returned them, and keep them internal.

Attachment mapping must be explicit, one-to-one, and based on returned attachment IDs. When exactly one readable file is demonstrably current relevant SPEC, it may be proposed as replacement. When several files could be relevant, present filename options with MIME type and evidence-supported purpose, retaining IDs as hidden internal values. User chooses a filename, never an ID. For multiple replacements, document each source attachment ID to one reviewed output file and obtain clarification for every ambiguous pair. Never map several attachments to one output, overwrite an attachment because its name looks similar, add attachment implicitly, or delete an attachment as part of replacement.

A readable non-CEAIA attachment may supply requirements that belong in a new CEAIA story-named SPEC when user requests corresponding update. Preserve original attachment ID for eventual selected replacement, but do not preserve obsolete format as user-facing deliverable. Imported attachments remain at paths returned by reader; do not move, rename, or alter evidence copy. Reviewed output SPEC is written only under ticket output directory.

## Review, approval, and export boundaries

After update Story qualifies, generate Test Case and final matching SPEC under normal generation controls. Perform title alternative validation, exact Story embedding checks, filename synchronization, and deterministic hygiene before sending complete set to read-only independent review skill. Review must verify update preservation, manifest completeness, score-record currentness, supported scope, and attachment mapping in addition to normal Story, test, and SPEC quality. A review PASS makes ticket Jira-ready; it does not authorize a Jira write.

Only separate Jira handoff capability may prepare diff preview, gather final explicit approval, and perform approved update. Hand it reviewed per-ticket manifest, selected mappings, and reviewed replacement paths. Do not call `java-base-mcp.updateJiraTicket` directly from this workflow. Never include score JSON, review report, planning files, attachment ledger, audit identifiers, review identifiers, checksums, standalone Test Case, or internal source evidence in Jira descriptions, payload fields, or attachment selections.

If one ticket is blocked, retain its ledger and awaiting reason without contaminating other ticket ledgers. Report each issue with complete ticket-specific workspace-relative path and line or range where known. Do not declare multi-ticket work complete until every intended ticket has been updated successfully through approval-controlled handoff or user explicitly stops its outstanding target.

## Update recovery matrix

| Condition | Required action | Prohibited shortcut |
|---|---|---|
| Source not confirmed | Ask for the source for that ticket | Guessing from project or prior call |
| Description unavailable | Ask for current baseline content | Replacing from attachment or summary |
| Separate AC empty | Record `empty-or-unavailable`; inspect description | Treating it as automatic intake failure |
| Relevant attachment skipped | Ask for readable content or handling decision | Assuming the file is irrelevant |
| Mapping ambiguous | Ask user to select displayed filename | Asking for or guessing attachment ID |
| Score record non-qualifying | Hold downstream work and request supported evidence | Averaging, manual score, or repeat of unchanged content |
| Review non-PASS | Follow normal attempt and evidence rules | Writing Jira change before a clean verdict |
| Approval absent | Keep Jira-ready artifacts internal | Uploading because user asked generally to update |

These controls preserve ticket identity, existing commitments, attachment safety, audited quality gating, and approval boundaries while allowing supported updates to proceed without inventing Jira state.

## Completion checklist for an update target

Before a target is offered to Jira handoff capability, verify and record each item: source is confirmed; description baseline was read; separate AcceptanceCriteria outcome is recorded; attachments were read exactly once; every readable attachment was considered; every skipped attachment has recorded reason; manifest preserves baseline and incremental scope; current Story has one current verbatim score record; score predicate qualifies; Test Case and SPEC have completed normal validation; review returned PASS; a one-to-one replacement mapping is resolved where attachment changes; and no unsupported Jira field is proposed. A checklist item may not be marked complete from a similar ticket's evidence.

Where ticket has no intended attachment replacement, record that explicitly rather than leaving blank mapping that could be misread as permission to add or remove attachment. Where user stops before clean pass, preserve ticket-specific state as unresolved and do not describe draft as ready for Jira. These records let later resumed workflow determine exactly which safe gate remains without re-reading assumptions into original request.
