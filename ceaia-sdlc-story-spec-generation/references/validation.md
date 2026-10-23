# Text-only verification checklist

Use this checklist with the actual saved files and the platform's existing tools. No code execution, command line, local parser, checksum calculation or additional verification tool is required. Read candidate-by-candidate; record actual findings rather than claiming automated validation.

## Before independent content review

1. Confirm candidate identity and full workspace-relative paths; reject absolute paths, URL schemes and traversal.
2. Read the complete saved Story. Check exactly one heading `## Acceptance Criteria`, distinct AC-001-style IDs and Given/When/Then for every criterion. Count the declared criteria by reading them, not by counting cross-references.
3. Check source support, scope, every planned SPEC filename against the mandatory `<name>-spec.md` rule, and attachment action. Record the observed storyRevision; invalidate stale downstream content and old scores associated with changed content.
4. Generate Test Case and SPEC from the supported Story. Read the actual files for the following review precheck; no score is required yet.

## Before independent review

1. Read Story, Test Case and every primary/additional SPEC using their full paths. Confirm every actual saved basename ends in the required `-spec.md` form, is unique where content differs, and matches the SPEC File Name field, state, planning, sibling references, intended upload plan and per-original replacement mapping. This also applies to a workspace-only update SPEC with no attachment change. Reuse these reads for all same-stage mechanical checks.
2. Check active template headings and supplied metadata. Compare embedded Story and test table with the saved sibling text section-by-section/row-by-row, not just by filename.
3. Check SR→AC→FR→TC references, missing requirements, duplicate IDs and unsupported additions. Validate original 11 test columns, scenario types and priorities against the template.
4. Read every title for standalone “or” and semantic alternatives. Split only supported distinct executable combinations. Give each AC exactly one negative disposition; unresolved To Confirm blocks readiness.
5. Check preconditions, data, steps and observable expected results are executable. Tables use one physical line per row, <br> within cells and escaped literal pipes.
6. Record observed revisions and concise evidence. These are read-based checks, not a claim of deterministic automated validation.

## Before the post-review score call

1. Read the numbered independent content-review PASS report, verify its current Story, Test Case and primary SPEC bindings, and resolve all findings. Do not require a pre-review score.
2. Synchronize the primary SPEC Review Result header to PASS and the actual review attempt, then read back the complete file. Verify the body is unchanged and record its fileRevision, exact Result string and workspace-relative path.
3. Confirm global source coverage and read back the unchanged final Story. Reuse the just-verified complete SPEC read-back if no write intervened; otherwise read it again. Call the scorer with Story `relativePath`, exact `specFileStatus: Pass` mapped from verified SPEC `Result: PASS`, and that SPEC's `specFilePath`; for an update use the modified Workspace Story, not its Jira key. Omit force_review/metadata normally.

## After the score call

1. Follow scoring-tool-contract.md: preserve the documented single complete JSON text response and unknown fields inside the numbered Markdown score record. Keep a concise readable summary and record the actual Story/SPEC inputs separately; read back the complete record.
2. Check genuine invocation association, candidate, Story and SPEC paths/revisions, review attempt, boolean ok=true and numeric finalScore from 76 through 100. Do not coerce a numeric string or infer a score from labels.
3. Inspect parseErrors and missing-AC warnings. Compare any returned top-level/legacy AC counts with the declared criteria. Missing optional counts alone are not failure.
4. A cache hit can reuse the score while storing the current SPEC metadata. Record actual result/currentness in state. Unsupported envelopes, incomplete response or uncertain association require recovery, not invented PASS. Content repair returns through affected independent review before another final score call.

## Before Jira preparation and sending

1. Read the latest independent content-review report and the later final score record: identity, all content gates, score result, exact submitted SPEC status/path, unresolved issues, preservation conclusions and reviewed revisions must agree with current files.
2. Confirm every intended SPEC header is still synchronized with the latest content-review PASS. The synchronization and full read-back occurred before the score call; a later body/status/path change invalidates final score metadata and Jira readiness. One targeted header-only retry is allowed after a write/read mismatch; a second mismatch is WAITING_TOOL.
3. Reconcile all selected candidates against original global source coverage. Never treat a missing candidate as implicitly excluded.
4. Apply the shared workflow's final preview/read-back procedure to the complete request and every attachment immediately before sending. A content change invalidates affected gates and the export binding.
5. If content cannot be read or safely associated, pause with the precise missing item; lack of a script engine or hash is not a blocker. Before the final user-facing response, read back each current SPEC path and display exactly that saved basename/path, with no stale planned filename.

Independent source review, real score provenance, final export preflight and confirmed Jira outcomes remain separate requirements. Manual/read-based checks have weaker change-detection guarantees than an immutable platform version binding; never describe them as cryptographic verification.
