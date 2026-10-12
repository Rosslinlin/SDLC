# Jira binding and reusable user information

Read this only for a Story with its own existing Jira ticket. This standalone route reuses SDLC's user-info filename and field labels, and makes the same five-argument binding call. It does not invoke the export skill or inherit SDLC's score gate, project/Epic collection, preview files or settings confirmation.

## Resolve the five values

Keep each Story's own full ticket key and Jira source together. A key mentioned as a reference, parent, dependency or example is not the target.

Before asking for staff ID or source, inspect:

1. The user's current explicit input and trusted current conversation/platform context, including actual Jira retrieval results.
2. Existing user-workspace identity context, including `user-info.md` when available.
3. The actual user-workspace `jira-user-info.md`, particularly its single `## CEAIA SDLC Jira Defaults` section.

Read the established user-workspace path through available tools; do not assume the conversation workspace is the user workspace. Read once and reuse within the batch unless the user changes the information or a failure requires correction. Prefer current explicit per-ticket values over saved defaults. Use saved values when they apply and are not contradicted by known ticket/source evidence; do not ask the user to reconfirm them.

Map the existing profile labels exactly:

| Saved label | Binding argument |
| --- | --- |
| Staff ID | staffId |
| Jira source | almType |

Keep staffId as the actual string, including leading zeros. Supported almType values are `wpb`, `alm`, `data`, `fcr`, `go`. Normalize a known source label's case; never derive source solely from a ticket-key prefix. An unknown or contradictory source is a missing fact, not permission to guess.

Use the real active conversationId from trusted current platform context or the scoring response from this exact active invocation when available. Do not load conversationId from saved user defaults, invent it, or derive it from the file path or ticket key.

If staffId or almType is still missing, ask once for only the missing inputs, with independent controls via `ask_user_question` or the available input UI. Offer supported source choices. This is first-time data collection, not confirmation of known values. If the input UI is unavailable, state the exact missing information in chat and keep binding pending. Keep the completed score/result while waiting.

Do not send empty strings or placeholders simply to provoke an error. Do not collect project, issue type, Epic or Parent fields for this binding call. Use Jira read tools only when actual retrieval or target disambiguation is necessary, not as a mandatory settings-confirmation step for a clearly identified ticket.

## Call directly

After a completed numeric assessment of the unchanged input, with a clear target and all five real values, call `java-base-mcp.exportJiraByDynamicFields` directly:

```json
{
  "staffId": "actual staff ID",
  "almType": "verified or applicable saved Jira source",
  "issueIdOrKey": "AIWPB-123",
  "conversationId": "actual active conversation ID",
  "markdownReviewRelativePath": "outputs/user-story-reviews/aiwpb-123/AIWPB-123-score-input.md"
}
```

Use exactly these five direct non-empty string arguments. No `request` or `requestJson` wrapper. The path must be the exact input path passed to the scoring tool, including any version suffix or older directory layout; never pass the score-result path. Use a full ticket key, never a numeric-only ID.

Do not include summary, description, projectKey, dynamicFieldsJson, attachmentsJson, issuesJson or any other Jira-change field. Do not create a Jira issue or rewrite Story content through this call. Make one call per Story with its own scored path; never attach a shared batch path to several tickets.

No "Are these settings correct?", "Use these settings", approval popup or separate preview file is required for this standalone binding. Reuse complete values and proceed, even for a low score. Recognition concerns remain visible in the result; do not treat binding as a quality pass.

## Confirm outcome and recover

Determine success from the actual `markdownReview` result and any returned ticket identity, not transport success alone. A conflicting returned key, numeric status=400, partial result, missing confirmation, timeout or unknown outcome is not confirmed binding.

Save the complete actual call/response in the same score-result file. Mark it bound only when the response confirms the intended association. Never change the raw scoring JSON. If an identical ticket/source/input association is already confirmed, do not call again.

On failure, keep the score and input path, explain the actual error and ask only for the correction or recovery choice it requires. After corrected identity/source data arrives, retry the binding step without re-scoring or creating a ticket. Reconcile unknown outcomes when possible; do not blindly replay or loop automatically. Batch items with independent valid identities may continue, while items depending on the same failed identity wait for correction.

If a new ticket is created later by another export skill, hand over the original input/result paths and current binding status. That skill can bind the new key after successful creation. Do not rename old input files or repeat an already confirmed association.

## Persist compatible defaults after confirmed success

Only after confirmed binding, create or update user-workspace `jira-user-info.md` through the actual user-workspace tools. This file is reusable user context, outside the two per-Story assessment files. If it is unavailable, report that persistence is pending; do not create a misleading substitute inside the conversation output folder.

Use the same canonical section and labels as SDLC. Read the whole existing file before editing; retain all unrelated content and a single defaults section. For a new file, use:

```markdown
## CEAIA SDLC Jira Defaults

- Staff ID: <actual successfully used staff ID>
- Jira source: <actual successfully used almType>
- Project key:
- Project name:
- Issue type:
- Issue type ID:
- Epic Link:
- Epic Link decision:
- Parent Link:
- Last successful SDLC export:
```

Replace the two identity placeholders with real values. Leave other values empty unless they already exist in the file; do not invent routing defaults just to fill the template.

For an existing file, update only the known Staff ID/Jira source values compatible with its saved destination. Preserve project, issue type, Epic/Parent, explicit no-Epic decision, successful SDLC ticket history, export date and any other fields exactly. A score binding is not an SDLC export; never set Last successful SDLC export or claim a successful Jira content update.

Keep the stored source and its project/type/Epic/Parent settings coherent. If this review targets a different source from the existing SDLC destination defaults, use the current per-ticket source for binding but retain the saved default source and its dependent fields. Record that decision in score-result; do not silently switch the shared SDLC destination merely because another source was scored. If a differing default source has no dependent routing values, it may be replaced only when the user explicitly selected the new source as their reusable default.

Do not store tokens, passwords, authentication headers, conversation IDs, full Story content or scoring JSON in the user-info file. Do not update defaults from a failed or uncertain binding. Read back a successful profile update and record its outcome in the result.

Future SDLC runs still validate/collect their own destination fields and confirmations. This skill's permission to reuse the profile and bind directly is local to standalone scoring; it does not change the export skill's behavior.
