# Existing-ticket updates

Read workflow-contract.md for source-qualified latest-output paths and retained internal records. Preserve each target's baseline plus supported requested delta; never merge tickets or implicitly create new tickets.

## U1 — Intake

1. Confirm source independently for each ticket before reads.
2. Request description and AcceptanceCriteria separately through java-base-mcp.getJiraInfos.
3. Read all attachment inventory through java-base-mcp.readJiraAttachments before planning/selecting replacements. Reuse one completed intake result per target/current baseline, not one call forever. A technical failure or reconciliation after partial write may require a supported, bounded re-read.
4. Read every returned imported readable attachment at its workspace-relative path. Record relevance or reason for non-applicability for each; preserve every skipped file and reason.
5. Store original description and AC result under <internalRoot>/intake/. Imported files remain at returned paths, unmodified as source evidence.

Reader input follows the live schema, with ticketKey, source, and current conversationId only when runtime requires it. It returns relativePath, attachment ID, name, MIME, size, import/skip status; do not invent missing fields. Never use download URLs, guessed IDs or user-entered IDs as substitutes.

If description exists but separate AC is empty/unavailable, record empty-or-unavailable and inspect embedded criteria; continue. If AC was returned, preserve that actual status/value. Unusable description blocks baseline replacement. Failed/incomplete inventory blocks update until resolved; zero attachments is a valid completed inventory.

Potentially relevant skipped attachments require readable content or an explicit evidence-supported handling decision. Do not equate unsupported file type with irrelevance.

## U2 — Change scope and manifest

Before generating a replacement, build <internalRoot>/update-manifest.md with:
- source, exact ticketKey, source-confirmation evidence and retrieval statuses;
- original baseline paths, incremental requirements and relevant imported/skipped inventory;
- preservation matrix: baseline obligation → preserved/explicitly changed/clarification, with evidence;
- summary decision (unchanged unless replacement is explicitly supplied or reliably supported);
- attachmentAction, selected filename/returned ID, replacement SPEC path/name or none;
- proposed current Story path, concise business change summary and unresolved decisions.

Score paths/values/checksums/audit IDs do not belong in this manifest. They are in state.json and the score record, accessible to review via statePath.

The current tool contract does not guarantee current summary retrieval. Do not invent it. Never update priority, assignee, labels, custom fields, status, links or unsupported fields in the bundled update mode.

Inherited broad baseline scope is not by itself a reason to split into newly created tickets. Preserve commitments; flag scope adjustments for a user decision when needed. Do not delete old requirements to improve the score.

## U3 — Attachment action

Decide before Story scoring:
- `none`: no attachment write intended (including description-only/zero-attachment updates). Record an empty replacement mapping. Story Attachments says no attachment changes; it must not name the generated workspace-only SPEC as an uploaded attachment. Still generate and review the local SPEC and tests.
- `replace`: replace each explicitly selected existing SPEC using its corresponding reviewed output SPEC. Propose a uniquely evidenced target; when ambiguous show filenames, MIME and purpose in a selector, keeping returned IDs internal. Final preview and export preflight confirm each selection.

One ticket has one current Story and Test Case set, with a primary SPEC and additional distinct replacement SPECs when requested. Do not ask the user to discard a requested replacement merely to fit a single-SPEC assumption. Different content requires separate reviewed files, not separate Jira tickets.

Before Story scoring, establish all intended final basenames and explicit original attachment ID → replacement relativePath/fileName mappings. No future SPEC file is created yet. Story Attachments lists only the intended uploads. Use unique filenames under the same outputRoot.

Record every current SPEC in state.specArtifacts (including the primary paths.specPath) with path, fileName, uploaded, bodyRevision, fileRevision. Keep attachmentReplacementMapping entries with attachmentId, relativePath, fileName. IDs come only from the reader. If multiple originals intentionally receive one file, require explicit many-to-one confirmation; otherwise mappings are one-to-one. Document per-original baseline preservation.

Each final SPEC follows the active template, embeds current shared Story and test table, declares shared FR coverage, and preserves its own source-specific details. Review every file and mapping; bind every SPEC body in reviewedSpecArtifacts and every final uploaded file in export preparation. A primary SPEC PASS is never sufficient for an unreviewed secondary file.

No safe existing target when action=replace is a blocker. Do not silently switch to none, add a new attachment or delete an attachment. A requested new attachment is outside this bundled update surface and needs an explicitly chosen supported workflow.

Reader evidence is immutable; write replacement content under outputRoot. Upload replacement first and remove the selected original only through the approved replacement API, not a separate inferred deletion.

## U4 — Generation, review, handoff

Use normal planning, Story scoring and repair gates. Story changes preserve baseline and update only supported behavior. Review checks original description, available AC, every relevant readable attachment, preservation matrix and action-specific mapping.

A none mapping is resolved and can yield updateReady=true. A replace mapping must be fully resolved before PASS. Send statePath and reviewed manifest to the `jira-createissue-helper` SDLC gateway; generation never calls the Jira export tool directly.

If a ticket changed externally before submission, reconcile the current baseline and rerun affected preservation/content review. Do not overwrite concurrent user changes from stale intake. Maintain ticket/source-specific status and exact affected paths.
