# CEAIA SDLC Jira Gateway

Gateway version: 1.3.0 — v5

This is the controlling entry point for a `ceaia-sdlc-validated` handoff. It adds SDLC-specific validation, Jira rendering, preview, direct export, post-write Markdown review ticket binding and recovery rules without changing the helper's generic create, update, batch, association or Test Case flows.

## 1. Routing boundary

Enter this gateway when the handoff declares `handoffType: ceaia-sdlc-validated`. The bundled SDLC workflow creates that handoff automatically after review PASS whether or not the user's opening input mentioned Jira export. An explicit preparation-only/no-Jira request or explicit stop suppresses the handoff.

`handoffType` is an internal routing discriminator. Retain it in workflow state/handoff validation, but strip it before every Jira MCP call. It is not exposed by the Jira tool schema and must never appear in tool arguments, `dynamicFieldsJson`, `attachmentsJson`, or `issuesJson`.

Load in this order:

1. `sdlc-gateway.md`;
2. `sdlc-handoff-validation.md`;
3. `flow-sdlc-validated-handoff.md`;
4. the reusable common modules named by that flow.

Do not load a generic business flow as a substitute. A failed SDLC gate remains an SDLC blocker; never downgrade it to generic create, update or batch export.

## 2. Immutable handoff boundary

The current reviewed workspace artifacts are authoritative:

- `STORY.md` and every final story-named SPEC in `outputs/ceaia/...` are the latest deliverable files;
- the complete current score record and numbered review records under `.ceaia-work/...` are internal evidence only;
- `TEST_CASE.md`, plans, score records, review records, state and manifests never become Jira attachments;
- Jira preparation must not repair, regenerate, summarize or silently omit approved business content.

Every intended SPEC upload must already be named `<name>-spec.md`, with the reviewed lowercase kebab-case Story/business-function name and fixed suffix defined in `sdlc-handoff-validation.md` Section 2.1. This is a mandatory handoff condition, not an upload-time display-name transformation. A noncompliant name or stale filename mapping blocks export and must return to upstream preparation and independent review; do not silently rename the file or change only `fileName` in Jira arguments.

If Story/SPEC content, attachment selection, ticket mapping, or a business-bearing Jira field must change, invalidate the current preview/export binding and return the affected artifact to generation and independent review. Jira-wiki rendering described below is a transport transformation and must not modify the workspace source files.

## 3. Pre-export gate

For every item, validate all of the following from actual current files and state:

1. score record is complete and current; `ok` is boolean `true`; `finalScore` is a finite number from 76 through 100; no unresolved AC/parser blocker remains;
2. independent `reviewResult` is `PASS`, `jiraReady` is `true`, and update items also have `updateReady: true`;
3. `testCaseTitleValidation` is `manual-pass`;
4. every intended final SPEC path appears in the latest review's `reviewedSpecArtifacts` with PASS and read-back evidence;
5. the first two SPEC header fields are synchronized to the latest PASS and the current review attempt, and the post-write read-back is recorded as successful;
6. global source coverage for the selected batch is PASS;
7. Story, SPEC filenames, operation, Jira source, ticket mapping and attachment mapping agree.
8. every `add` or replacement upload satisfies the mandatory `<name>-spec.md` contract in `sdlc-handoff-validation.md` Section 2.1, with identical actual filename, reviewed path and upload `fileName`. Existing retained/deleted/replaced source attachments are not renamed by this check.

Any failure blocks only the affected item unless an integrated batch cannot safely exclude it. Do not manufacture a PASS from a header or a state flag.

## 4. Story field partition and Jira-wiki transport rendering

Read the exact current reviewed `STORY.md`; keep it unchanged. Its single standalone `## Acceptance Criteria` heading starts the AC section, which ends immediately before the next `# ` or `## ` heading outside a code fence, or at end of file. Lower-level headings within that section remain with the ACs. If this boundary or the approved `AC-###` declarations are ambiguous, block export instead of guessing.

Partition the Story once before rendering: Description source is every section outside that AC section in original order, including any sections after it; Acceptance Criteria source is only the AC section body, without its heading. The Jira field label supplies the removed heading. Preserve all business wording, AC IDs, Given/When/Then text, lists and links in their assigned fields. Do not add, omit, reorder or paraphrase content. This partition applies equally to SDLC create, update and each batch item in Task mode and the normal conversation route.

Apply the non-Test transport rules in `common-jira-text-rendering.md` plus the stricter SDLC rules in this section. If they overlap, this gateway's reviewed-Story preservation and Acceptance Criteria mapping rules control. The common module's Test Case exclusion remains absolute.

Outside fenced code blocks, convert Markdown presentation syntax deterministically:

| Workspace Markdown | Jira wiki payload |
| --- | --- |
| `# Title` | `h1. Title` |
| `## Title` | `h2. Title` |
| `### Title` through `###### Title` | `h3. Title` through `h6. Title` |
| `- item` or `* item` | `* item` |
| nested unordered list | repeat `*` for the nesting level |
| `1. item` | `# item` |
| nested ordered list | repeat `#` for the nesting level |
| fenced code block | `{code}` block, preserving its body exactly |
| inline code `` `value` `` | `{{value}}` |
| `[label](https://example)` | `[label|https://example]` |
| Markdown block quote | `bq. ` line |
| Markdown table header/body | Jira `|| header ||` and `| cell |` rows; omit the Markdown separator row |

Additional rules:

- Convert only syntax, never rewrite sentences or criteria.
- Do not treat digits inside AC IDs, examples, dates or prose as list markers.
- Do not convert headings or list markers inside code blocks.
- Preserve intentional newlines as newline characters; never insert literal `<br>` tags.
- Do not send Markdown heading markers such as `## ` in the Jira-bound Description.
- Do not prefix an already converted heading again. The rendering must be idempotent for lines beginning `h1.` through `h6.`.
- Render the Description source and AC source independently. Map the latter to the metadata-confirmed writable Acceptance Criteria field through `dynamicFieldsJson`; never put that field in the direct `description` argument.
- Description must contain neither the extracted AC section heading nor its AC declarations or Given/When/Then bodies. The AC field must contain every extracted declaration exactly once and no non-AC Story section. A reference to an AC ID elsewhere in the Story is not an AC declaration to remove.

Before preview, compare both rendered fields with their respective Story sources section-by-section. Confirm that the two fields jointly account for the full reviewed Story, with only the AC heading represented by the Jira field label and presentation syntax converted. If a construct cannot be converted without changing meaning, stop and report the exact line instead of guessing.

## 5. Payload and attachment assembly

Use `flow-sdlc-validated-handoff.md` and metadata returned by Jira. For each item:

- Summary comes from the reviewed title unless an update explicitly preserves or authorizes another reviewed Summary;
- Description is the Jira-wiki rendering of the non-AC Story sections from Section 4;
- Acceptance Criteria is the Jira-wiki rendering of only the AC section body in the confirmed writable Jira field, in exact stable AC order. On an update, an existing AC field may remain untouched only when its separately retrieved current value matches the reviewed AC IDs, wording and order after allowed presentation normalization; otherwise include the field replacement;
- required metadata follows the helper's current-value/default/evidence/user-selection order;
- attachments follow the validated reviewed mapping and `common-attachments.md` serialization contract;
- labels include `CEAIA_GEN` whenever labels are included or changed.

Never put internal paths, scores, review diagnostics, state, or export-attempt records into Jira-visible fields.

### Live Jira tool argument boundary

Call tools with direct named arguments, never a `requestJson` wrapper:

- `queryJiraProjectsByName`: `staffId`, `almType`, `projectName`.
- `queryJiraIssueTypesByProject`: `staffId`, `almType`, `projectKey`.
- `queryJiraCreateMetaFields`: `staffId`, `almType`, `projectKey`, plus at least one of `issueType` or `issueTypeId`.
- `exportJiraByDynamicFields`: applicable exposed fields such as `staffId`, `almType`, `projectKey`, `summary`, `issueType`, `description`, `projectName`, `epicName`, `epicLink`, `parentLink`, `dynamicFieldsJson`, `testDetailsJson`, `issueIdOrKey`, `conversationId`, `attachmentsJson`, `linkedIssueKeys`, `linkType`, `issuesJson`, or `markdownReviewRelativePath`. The five-field SDLC binding call in Section 7 requires real values for all five of its fields regardless of generic optionality.

For a single create, use direct create fields and a once-serialized `dynamicFieldsJson`; add the reviewed SPEC through a once-serialized `attachmentsJson`. For a single update, send `issueIdOrKey` plus only authorised changed fields. For multiple SDLC items, use one once-serialized JSON array string in `issuesJson`; do not send an `issues` argument that the exposed tool does not provide. Batch attachment operations stay inside their corresponding item. The first create/update call must omit `markdownReviewRelativePath`: this v5 route explicitly waits for the returned ticket key and then makes a separate per-ticket binding call. Never include `handoffType`, workflow status, score, review data or preview metadata in either call's arguments.

## 6. Final preview and export binding

Write or replace the single current `jira-preview.md`. Include:

- item operation and destination;
- current PASS and SPEC-header synchronization evidence;
- full Jira-rendered Summary, non-AC Description and separate Acceptance Criteria, plus the section-to-field partition check;
- a Markdown-to-Jira-wiki conversion status with any syntax changes listed by type;
- exact attachment add/replace/delete plan and preflight status;
- for every upload, the exact `<name>-spec.md` filename and passing filename-contract/path/review consistency status;
- complete final `exportJiraByDynamicFields` payload;
- a statement that the displayed payload and final attachment bytes are the exact content that will be submitted.

No approval capability is part of this workflow. Do not replace it with a final `ask_user_question` popup. The user's SDLC export intent is established by the bundled end-to-end workflow, and the reviewed handoff plus this final preflight authorise the direct tool call within that requested scope. `ask_user_question` remains available only for missing required values, the SDLC defaults confirmation, ambiguity and recovery choices.

The SDLC defaults confirmation required for a later run is not a final-write approval popup. Complete it before generating the final preview. After that routing confirmation and final preview/read-back succeed, call Jira directly without another confirmation.

Before export, save a numbered internal record such as `.ceaia-work/jira/jira-export-attempt-001.md` containing the preview binding, exact tool arguments, serialized payload values, attachment revisions, preflight result and eventual Jira response. Never overwrite a completed export-attempt record. `jira-preview.md` remains the single current user preview; the numbered record is internal audit history and is never sent to Jira.

Immediately before the tool call, re-read the payload and every upload artifact. Any change to the rendered Description, metadata value, attachment mapping/path/content, operation order or target invalidates the preview binding. Regenerate `jira-preview.md`, repeat all affected checks and allocate the next export-attempt record before calling Jira.

## 7. Jira write, second binding call and recovery

After the final read-back/preflight succeeds, call `exportJiraByDynamicFields` immediately with the exact direct Jira-write arguments shown in `jira-preview.md`. Do not add `handoffType` or `markdownReviewRelativePath`, do not wrap the arguments, and do not add omitted optional parameters after preview.

For each item whose Jira issue write and requested attachment operations are confirmed successful, read the actual returned `ticketKey` and bind it to that item's current scored Story in a second, separate `exportJiraByDynamicFields` call. For an update, verify the returned key equals the intended update ticket key. A missing, numeric-only, ambiguous or mismatched key blocks binding; do not guess from a URL or use a batch index as a key. For a batch, use each successful resource's own returned key and Story path; call separately for each eligible item, never use a top-level batch path or associate another item's result.

The second call has **exactly five direct string arguments**, all non-empty and validated: `staffId`, `almType`, `issueIdOrKey` (the confirmed returned Jira ticket key), `conversationId` (the current conversation identity), and `markdownReviewRelativePath` (the same Workspace-relative `STORY.md` path submitted to `score_requirement_markdown` for this candidate's current passing score). Match the path against the current score record/raw response, candidate state and reviewed Story. Use the actual current conversation identity; request conversation headers may take precedence inside the tool, so do not invent an ID from a path or ticket. Do not pass `summary`, `description`, `dynamicFieldsJson`, `attachmentsJson`, `issuesJson`, links or any other Jira-change field. Omit unused arguments rather than filling them with empty placeholders. This five-field call is exclusive to the validated SDLC route.

Once the first result supplies the key, replace the single current `jira-preview.md` with the exact five-field binding payload and its candidate/key/path association. Preserve the original Jira-write preview and result in its numbered internal export-attempt record, allocate a separate numbered record for the binding attempt, read back the binding preview, and then make the second call directly. Do not request another final confirmation popup. Store Jira-write/attachment and Markdown-review-binding outcomes separately in `writeResults`; never overwrite the original successful Jira response.

Treat a binding response as successful only when its `markdownReview` result confirms the association. A `partial`, failed, malformed or unknown binding result leaves the already-created/updated Jira ticket intact and the candidate pending. Preserve its key/URL and exact binding attempt; never replay the first Jira write or attachment upload to repair only the binding. Do not automatically retry an uncertain second call. Reconcile the binding state, correct a documented validation error, and use a newly previewed binding-only attempt only after a supported recovery decision. A numeric `status=400` is a business validation failure even if the MCP envelope does not set `isError`.

If a user reports that Jira displays different Story content, compare three read-only sources for that ticket: the current reviewed `STORY.md`, the preserved first-call Description/AC payload, and separately retrieved Jira Description/Acceptance Criteria values from `getJiraInfos`. Compare the combined Jira fields with the source partition, allowing only Markdown-to-Jira presentation differences. A source-to-payload difference is an SDLC assembly defect; a payload-to-Jira difference needs tool/Jira investigation. Report the exact field and difference. Preserve the existing ticket and do not replay a successful create/update as a diagnostic step.

For multiple update targets, execute in the exact order shown in the final preview and pause after the first failed, partial or unknown result before attempting later targets. Record known issue-field and attachment outcomes separately. Do not automatically retry, replay a successful create, or delete an old attachment after a failed replacement upload.

Whole-workflow success requires every intended Jira issue/attachment operation and every corresponding Markdown review ticket binding to be confirmed successful, or the user to explicitly stop the remainder. Waiting, failed, partial and unknown outcomes are resumable states, not success. Report the Jira key/URL and the two result categories separately for each item.
