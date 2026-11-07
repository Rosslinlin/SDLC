# Preview, one-path submission and recovery

Use after final files are prepared. [Assessment evidence](story-assessment-evidence.md) is optional upstream context, not a universal Story gate. [The JSON contract](common-json4jira-contract.md) defines the only content invocation; [Test Case](flow-testcase-source-export.md) owns its mode-specific integrity/shape.

## Read and preview the actual package

Read the actual saved issue file, or manifest plus every child and intended upload. Validate the selected operation, source/project/type, exact update target, actual metadata types and supported attachment/association plan. Every issue must contain native `fields.project.key` and `fields.issuetype.id` with nonempty real values consistent with root routing and the verified target/type. A root-only file or a delta that removed either returns to preparation before confirmation; upstream origin never exempts it. Reuse verified unchanged reads and completed source checks; never regenerate business content from a summary or rendered view.

Show the platform's native file/content preview with full final JSON available. It must identify create/update, actual paths, targets, generated versus preserved content, approved changes/intentional clears, fields and attachments. For a batch, show every selected child, order, per-item operation/target and any actual relevant assessment evidence, distinguishing individual Story calls from non-Story manifests. Test Case adds source/selected IDs, mode, source-to-file/table mapping, applicable steps/data/results, explicit labels, newline checks and deliberate single-mode column exclusions. Visual abbreviations must be labeled and must not shorten saved values.

A single optional derived `jira-preview.md` can retain these facts, exact invocation, score/confirmation bindings and result evidence. It is an audit view, not a second payload or scoring input. Never substitute its path for an issue/manifest path. Do not require extra HTML, state, handoff or duplicated source files.

Resolve blocked fields/operations before requesting final confirmation. Unsupported attachment replacement/relationship encoding is not a reason to omit that requested effect silently; show the precise gap and retain the prepared package.

## Current explicit confirmation

Obtain real explicit content/write confirmation through supported platform UI after the current package is available for review. Current user selection of source/destination alone is not final content approval. Reuse a prior actual confirmation only when it covers these same unchanged JSON/manifest children, target, selected order, scoring SPEC and intended uploads/operations. Do not reopen confirmation merely to read saved configuration or proceed to the next already approved item.

Use a friendly review prompt tied to actual deliverables, for example: “请先查看这些文件和字段，确认内容是否 OK；没问题就按上面的项目和类型导出。需要修改也可以直接告诉我。” Include real file links, create/update target and intended uploads/relationships. The earlier settings check is not this file/content approval, and silence or opening a file is not confirmation.

If the user changes content, destination, type, target, selected subset/order or participating file/attachment plan, return to preparation, mark affected old assessments historical, then show the changed package and obtain renewed confirmation. Do not mutate, rename, reserialize or rich-text-render a scored/confirmed file at submission.

If the platform lacks supported preview/confirmation, retain actual files and report that exact capability gap. An agent-written approved flag, previous version's approval or broad initial export intent does not substitute for current content confirmation.

## Submit each approved unit once

Immediately before submission read back the approved issue/manifest and children, confirm both mandatory routing objects remain present and correctly bound in every issue, with no intervening change, target switch or participating-file mismatch, and preserve the exact request in current context/the existing record. Missing/null/empty/mismatched project/type values block submission and return to preparation, never a last-minute patch or automatic retry. For inputs reported as assessed, preserve the actual current/historical distinction. Missing scoring is not a global blocker and does not trigger scoring. Reject a manifest containing a Story; submit Stories individually under the approved plan. No silent omission or type relabeling.

Call `java-base-mcp.exportjirabydynamicfield` once per approved submission unit with exactly the actual `story_json_workspace_path`. A non-Story batch, including Test Case multiple, submits its manifest once. Multiple Stories use one individual file call each, in planned order. Follow [batch planning](flow-batch-export.md), stop further units after a failed/partial/unknown required synchronous outcome and preserve remaining selections. No other business/routing/identity/SPEC/batch arguments are sent.

After a call, retain its complete returned text/object and transport wrapper before interpreting it. Preserve unknown fields/errors/IDs and raw text when available; otherwise serialize the complete decoded response and label that representation. Do not truncate or replace it with selected success fields. An unknown timeout is an actual unknown attempt, not proof that no write occurred. A missing local record never authorizes blind remote replay.

Save received results to the next unused `jira-export-result.md`, `jira-export-result-v2.md`, etc. alongside the selected package, or append this actual attempt to the existing operation record. Preserve prior attempts and exact path/input/confirmation evidence. A result is written only for an actual invocation/outcome; local save failure retries that save only, while conversation evidence retains the received result. Do not invent IDs or a fictitious successful response.

## Interpret each effect separately

Inspect business status inside the actual transport result; MCP `isError=false` or a generic success envelope does not override a nested rejection such as actual status=400. Use the real response schema/guarantees, not mandatory invented property names:

| Effect | Evidence/reporting |
| --- | --- |
| Issue create/update | Actual success with unambiguous ticket key/URL when returned; an update's returned target must agree. A mismatch is a material failed/unknown binding, never a guessed success. |
| Test Details | Actual step-upload result or a documented guarantee covering the selected native array. Single-mode Test Case has none by design. A created issue alone does not prove steps uploaded. |
| Attachments/relationships | Each requested effect's actual result or explicit documented atomic guarantee; none is N/A only when no such operation was requested. |
| Story database association | Actual response/contract evidence that this JSON assessment was linked to its ticket. A conflicting same-kind assessment ID/path leaves this association unconfirmed; preserve the actual issue result and mismatch, without replay. Non-Story and unscored/exempt projects do not need score association. |
| Score-sync queue where applicable | Actual acceptance/enqueue evidence or documented guarantee; retain returned ID when available. Missing an optional ID or an exempt project's queue evidence is not failure. |
| Jira score fields | Actual completed/failed/pending/unreported evidence. Queue acceptance proves scheduling, not finished Jira UI updates. |

Report each observed state, including unknown/unreported; don't manufacture database/queue success from a ticket key. Conversely, a missing optional downstream property is not proof of failure and does not trigger another call. No polling loop, old four/five-field binding, ticket/source sync or repeated export is used to nudge asynchronous processing.

For batch item results, map actual returned indexes/identities to the approved child order. Report each child/source Test Case ID, path/index, actual key/URL, status and error. Do not invent sequential keys, collapse them into a range, or assign a top-level key to every child. Partial/truncated or ambiguous item results leave unmatched items unknown; never resubmit the whole manifest.

After each confirmed issue create/update, including each confirmed successful manifest child, complete the mandatory [user-level profile lifecycle](common-state-and-user-info.md): set profilePersistence pending, re-read/merge the current populated configuration, write and verify a real user-scope read-back, preserving all other project/type/capability records. This applies even to complete upstream files and separately failed/pending attachment, Test Details, association or queue effects; record those effects honestly. `lastSuccess` means the actual issue success, not whole-package success. Useful routing/type facts may be saved with metadata.complete=false without new discovery or inferred schema. Report the actual persistence status/read-back or precise pending cause independently. Saving profiles/results never causes a Jira replay.

## Resume only the unresolved layer

| Actual condition | Next action |
| --- | --- |
| Local result/profile save failed | Retry that save from the received evidence, without discovery, rescoring or export. |
| Issue succeeded but steps/attachment/link/Story association/queue failed | Retain ticket and every successful effect. Use only an actually supported focused recovery for the failed layer; never recreate or resend confirmed content/files. |
| Request timed out or ended without a known outcome | Preserve unknown. Reconcile with supported read-only evidence before any user-directed retry; no automatic replay. Jira reads can establish ticket/attachment facts, not database/queue state. |
| Manifest partially succeeded | Preserve exact per-item outcomes and unresolved subset. Never rerun all children. Any supported subset retry needs known unapplied operations, an explicit selected subset and its current preview/confirmation. |
| Backend rejects missing/stale score, checksum, threshold or schema | Preserve exact error and any actual effect evidence. Return the exact missing preparation or project-specific assessment requirement; do not drop required fields, guess a score, change type or repeatedly score/export. Any later attempt needs an explicitly resolved prerequisite/correction, no prior uncertain effect and current confirmation. This helper does not automatically assess or impose a global scoring prerequisite. |
| Accepted queue is still pending | Report pending; use a supported read-only status capability only when actually available/requested. Do not wait endlessly, score again or re-export. |
| No supported recovery/encoding exists | Report the precise pending effect/capability, with all confirmed outcomes retained. A vague continue does not establish safe replay. |

The backend remains authoritative for stored score/currentness, supported mutations, association and queue effects. Local records are audit evidence, not remote locks or proof of database writes.
