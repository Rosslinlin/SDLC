# CEAIA SDLC Jira Gateway

Gateway version: 1.0.0 — v4

This is the controlling entry point for a `ceaia-sdlc-validated` handoff. It adds SDLC-specific validation, Jira rendering, preview, approval and recovery rules without changing the helper's generic create, update, batch, association or Test Case flows.

## 1. Routing boundary

Enter this gateway only when the user explicitly requests Jira export/update of reviewed CEAIA SDLC artifacts, or the handoff declares `handoffType: ceaia-sdlc-validated`.

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

If Story/SPEC content, attachment selection, ticket mapping, or a business-bearing Jira field must change, invalidate the current preview/approval and return the affected artifact to generation and independent review. Jira-wiki rendering described below is a transport transformation and must not modify the workspace source files.

## 3. Pre-export gate

For every item, validate all of the following from actual current files and state:

1. score record is complete and current; `ok` is boolean `true`; `finalScore` is a finite number from 76 through 100; no unresolved AC/parser blocker remains;
2. independent `reviewResult` is `PASS`, `jiraReady` is `true`, and update items also have `updateReady: true`;
3. `testCaseTitleValidation` is `manual-pass`;
4. every intended final SPEC path appears in the latest review's `reviewedSpecArtifacts` with PASS and read-back evidence;
5. the first two SPEC header fields are synchronized to the latest PASS and the current review attempt, and the post-write read-back is recorded as successful;
6. global source coverage for the selected batch is PASS;
7. Story, SPEC filenames, operation, Jira source, ticket mapping and attachment mapping agree.

Any failure blocks only the affected item unless an integrated batch cannot safely exclude it. Do not manufacture a PASS from a header or a state flag.

## 4. Jira-wiki transport rendering

Build a separate Jira-bound Description from the exact reviewed Story. Preserve section order, text, AC IDs, Given/When/Then wording, lists, links and meaning. Keep `STORY.md` unchanged.

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

Additional rules:

- Convert only syntax, never rewrite sentences or criteria.
- Do not treat digits inside AC IDs, examples, dates or prose as list markers.
- Do not convert headings or list markers inside code blocks.
- Preserve intentional newlines as newline characters; never insert literal `<br>` tags.
- Do not send Markdown heading markers such as `## ` in the Jira-bound Description.
- Do not prefix an already converted heading again. The rendering must be idempotent for lines beginning `h1.` through `h6.`.
- Map the Acceptance Criteria field from the same reviewed AC source and metadata-confirmed field. Do not append a second AC section to the Description.

Before preview, compare the rendered Description with the Story section-by-section and confirm that only presentation syntax changed. If a construct cannot be converted without changing meaning, stop and report the exact line instead of guessing.

## 5. Payload and attachment assembly

Use `flow-sdlc-validated-handoff.md` and metadata returned by Jira. For each item:

- Summary comes from the reviewed title unless an update explicitly preserves or authorizes another reviewed Summary;
- Description is the Jira-wiki transport rendering from Section 4;
- Acceptance Criteria uses the confirmed writable Jira field and exact stable AC order;
- required metadata follows the helper's current-value/default/evidence/user-selection order;
- attachments follow the validated reviewed mapping and `common-attachments.md` serialization contract;
- labels include `CEAIA_GEN` whenever labels are included or changed.

Never put internal paths, scores, review diagnostics, state, or approval records into Jira-visible fields.

## 6. Preview and final approval

Write or replace the single current `jira-preview.md`. Include:

- item operation and destination;
- current PASS and SPEC-header synchronization evidence;
- full Jira-rendered Summary, Description and Acceptance Criteria;
- a Markdown-to-Jira-wiki conversion status with any syntax changes listed by type;
- exact attachment add/replace/delete plan and preflight status;
- complete final `exportJiraByDynamicFields` payload;
- a statement that the displayed payload and final attachment bytes are the approval subject.

After the preview is complete, invoke `request_user_approval`. The approval request must identify the current preview, exact serialized payload, operation order and final attachment paths/bytes. `ask_user_question`, normal chat, inferred intent, earlier approval or review PASS is not authorization.

Before invoking approval, save a numbered internal record such as `.ceaia-work/jira/jira-approval-attempt-001.md` containing the preview binding, payload, attachment revisions, request status and eventual decision. Never overwrite a completed approval record. `jira-preview.md` remains the single current user preview; the numbered record is internal audit history and is never sent to Jira.

While approval is pending, yield. On reject or unavailable approval capability, keep a resumable waiting state and do not call Jira. Any payload, rendered Description, metadata value, attachment mapping/path/content, operation order, or target change invalidates approval and requires a regenerated preview plus a new approval request.

## 7. Write and recovery

Only after actual approval, call `exportJiraByDynamicFields` with the exact approved payload. Re-read the final payload and attachments immediately before the call; if they differ from the approved content, invalidate approval.

For multiple update targets, execute in the approved order and pause after the first failed, partial or unknown result before attempting later targets. Record known issue-field and attachment outcomes separately. Do not automatically retry, replay a successful create, or delete an old attachment after a failed replacement upload.

Whole-workflow success requires every intended operation to be confirmed successful, or the user to explicitly stop the remainder. Waiting, rejected, failed, partial and unknown outcomes are resumable states, not success.
