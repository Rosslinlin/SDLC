# Jira A, database binding B and recovery

Execute only with a current validated handoff, applicable field evidence, settings confirmation and preflight. The tool is `java-base-mcp.exportJiraByDynamicFields`; use its live direct-argument schema. No scripts or new tools.

## A — Write the Story and required attachments

| Argument | Use |
| --- | --- |
| staffId, almType | Real nonempty staff ID and confirmed lowercase source. |
| projectKey, issueType, summary, description | Create values; updates include only applicable authorized changes. issueTypeId is not an exposed export argument. |
| issueIdOrKey | Exact reviewed update target. |
| dynamicFieldsJson | Once-serialized supported fields, including separately mapped AC. |
| attachmentsJson | Once-serialized authorized attachment plan; omit for none. |
| issuesJson | Once-serialized nonempty item array for supported batch mode. |
| conversationId | Real current context for Workspace attachment transport/binding. |
| epicLink, parentLink | Dedicated direct relationship fields; omit Epic for explicit no-Epic. Updates change only reviewed authorized relationships. |
| linkedIssueKeys, linkType | Dedicated direct fields only for reviewed authorized relationships; keys are a native string array. No standalone association flow. |

Single create uses direct fields; multiple creates use issuesJson with each item's own fields/attachments. Ordinary updates run one ticket at a time in preview order; stop on the first failed/partial/unknown operation. Only an explicitly requested mixed create/update batch may use integrated issuesJson; otherwise use separate previews. Never invent issues, requestJson or testDetailsJson arguments. **A never includes markdownReviewRelativePath.**

Before A:

1. Write/read back the single current `jira-preview.md`: candidate/operation/destination/update keys, applicable profile and settings confirmation, full effective Summary/non-AC Description/AC field mapping, metadata provenance, attachment operations/IDs/paths/names and serialized strings, and the exact complete direct-arguments payload. Do not duplicate full business bodies outside the payload.
2. Save the exact preview, payload, current file bindings and preflight in the next numbered immutable A-attempt record under `.ceaia-work`. Preserve the complete actual response in that record after the call. Never overwrite a completed attempt.
3. Immediately before calling, reread the exact payload and intended upload files; verify they match the preview. A change invalidates affected preparation: rebuild the current preview, retain prior attempt evidence and repeat preflight with a new numbered attempt. No internal handoff/score/state/profile metadata enters tool arguments.
4. Call A directly after preflight; no additional final-approval popup follows settings confirmation. Inspect the returned issue and each requested attachment result separately. A's confirmed successful upload must never be uploaded again.

## B — Bind each eligible ticket to its final saved score

A successful issue **and all requested attachments** are prerequisites. For each eligible item, match its actual returned ticketKey to the reviewed candidate and final Workspace score path; an update's returned key must equal its intended key. A missing, numeric-only, ambiguous or mismatched key blocks B; never infer keys from URLs or batch positions.

**Call the same tool separately for every eligible ticket, with exactly these five nonempty direct strings:**

```json
{
  "staffId": "<real current staff ID>",
  "almType": "<confirmed lowercase source>",
  "issueIdOrKey": "<A's actual returned Ticket Key>",
  "conversationId": "<real current conversation ID>",
  "markdownReviewRelativePath": "<this candidate's final scored STORY.md relativePath>"
}
```

The path is the final scoring input Story, never a score-record file or another candidate's path. Match it to current score/state/review evidence. B has no content, attachment, dynamic-field, batch or relationship argument; do not substitute the standalone reviewer's four-field sync.

The current numbered export attempt binds the final scoreRecordPath/scoreAttempt. A prior binding for the same path/Key is historical and cannot complete a new assessment/content operation. Keep the old outcomes; mark the new operation's binding pending until its own eligible B succeeds. Resuming the unchanged existing operation reuses confirmed A/attachments and performs only its missing B, without another score call.

Preserve A's preview/payload/result in its attempt before replacing current jira-preview.md with B's exact five-field preview. Allocate/read back a separate numbered B-attempt record, call B and preserve its complete response. Batch A success still requires one B per eligible returned key/path; no top-level batch Story path is inherited. **B succeeds only when returned markdownReview explicitly confirms association.** Generic success or A success is insufficient. Item completion requires A, every required attachment, and B; profile persistence is reported separately.

## Failure and recovery

- Preserve distinct issue, per-attachment and binding results in writeResults and numbered attempt evidence. Report every real key/URL, pending operation and sanitized error; failed/partial/unknown is never complete.
- Do not automatically resend external writes, uploads or bindings. Reconcile partial/unknown outcomes through supported read-only means before a bounded recovery decision. Numeric status=400 is business failure even when isError=false.
- A success plus B failure/unknown keeps the ticket/attachments intact and binding pending. Recovery is B-only with a fresh exact five-field preview and new B-attempt; never recreate/rewrite the issue or re-upload to repair binding.
- Issue success plus failed/unknown attachment preserves the issue, reconciles only that attachment, and does not enter B. For replace, old deletion waits for confirmed new upload. Never replay confirmed success.
- Only documented field-validation failure proving no external write permits one affected metadata refresh. Rebuild/preview; any further write waits for the user's bounded recovery decision and a new read-back/preflight attempt. No automatic retry or generic fallback.
- Persist eligible reusable profile facts only after issue plus all required attachments succeed. B pending may coexist with that valid profile; profile-write failure never causes replay. Follow workspace-profile.md.

If the tool contract cannot establish the result or a supported recovery method, record the exact WAITING_TOOL/user decision; never fabricate service IDs, database success or a replacement schema. Local records are audit evidence, not proof of a remote operation. Whether create metadata establishes edit capability, which final score row B selects, and unknown-operation idempotency require actual platform evidence; do not remove calls or invent capabilities to resolve these uncertainties.
