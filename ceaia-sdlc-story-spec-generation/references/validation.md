# Text-only verification checklist

Use this checklist with the actual saved files and the platform's existing tools. No code execution, command line, local parser, checksum calculation or additional verification tool is required. Read candidate-by-candidate; record actual findings rather than claiming automated validation.

## Before the score call

1. Confirm candidate identity and full workspace-relative paths; reject absolute paths, URL schemes and traversal.
2. Read the complete saved Story. Check exactly one heading `## Acceptance Criteria`, distinct AC-001-style IDs and Given/When/Then for every criterion. Count the declared criteria by reading them, not by counting cross-references.
3. Check source support, scope, final SPEC filename and attachment action. Record the observed storyRevision; invalidate any result associated with changed content.
4. Submit this saved Story using the real scoring tool. Keep it unchanged during the call.

## After the score call

1. Follow scoring-tool-contract.md: read the documented single JSON text response, preserve its entire content and unknown fields, and read back the saved score record.
2. Check genuine invocation association, candidate/path/revision, boolean ok=true and numeric finalScore from 76 through 100. Do not coerce a numeric string or infer a score from labels.
3. Inspect parseErrors and missing-AC warnings. Compare any returned top-level/legacy AC counts with the declared criteria. Missing optional counts alone are not failure.
4. Record actual result and currentness in state. Unsupported envelopes, incomplete response or uncertain association require recovery, not invented PASS.

## Before independent review

1. Read Story, Test Case and every primary/additional SPEC using their full paths. Confirm the intended inventory and per-original replacement mapping.
2. Check active template headings and supplied metadata. Compare embedded Story and test table with the saved sibling text section-by-section/row-by-row, not just by filename.
3. Check SR→AC→FR→TC references, missing requirements, duplicate IDs and unsupported additions. Validate original 11 test columns, scenario types and priorities against the template.
4. Read every title for standalone “or” and semantic alternatives. Split only supported distinct executable combinations. Give each AC exactly one negative disposition; unresolved To Confirm blocks readiness.
5. Check preconditions, data, steps and observable expected results are executable. Tables use one physical line per row, <br> within cells and escaped literal pipes.
6. Record observed revisions and concise evidence. These are read-based checks, not a claim of deterministic automated validation.

## Before Jira preparation and sending

1. Read the latest full independent report: identity, actual score, all required gates, unresolved issues, every SPEC's preservation conclusion and reviewed revisions must agree with current content.
2. Synchronize only the review header after substantive PASS. Check body content did not change, advance the file revision and record current full upload paths.
3. Reconcile all selected candidates against original global source coverage. Never treat a missing candidate as implicitly excluded.
4. Apply the shared workflow's read-back/approval procedure to the complete request and every attachment immediately before sending. A content change invalidates affected gates and approval.
5. If content cannot be read or safely associated, pause with the precise missing item; lack of a script engine or hash is not a blocker.

Independent source review, real score provenance, final approval and confirmed Jira outcomes remain separate requirements. Manual/read-based checks have weaker change-detection guarantees than an immutable platform version binding; never describe them as cryptographic verification.
