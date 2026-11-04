# Reuse user profiles and current request facts

Read only when destination/schema preparation or profile persistence is needed. Complete trustworthy upstream JSON keeps its actual path and verified facts; do not query merely because another skill authored it. [The JSON contract](common-json4jira-contract.md) controls file contents.

## Resolve current facts first

Use the latest explicit request, selected current files and verified conversation evidence first. For missing reusable configuration, read the real persistent user Workspace file `ceaia-sdlc-jira/profiles.json`. Retain this shared filename for compatibility; a conversation file is not a cross-session cache.

Keep current source/project/type, selected paths/revisions, operation/target, approved changes, actual attachment mappings, any supplied assessment evidence, preview confirmation and observed write outcomes in existing preview/result/context. These are request facts, not reusable defaults; a profile does not impose a scoring prerequisite.

For Test Case source export, collect `uploadType` once for every new export request through [its constrained choice](flow-testcase-source-export.md). An explicit mode selection in that same request already supplies it. Do not reuse a prior request's mode or persist mode, selected cases or source content in profiles.

## Match the correct user's configuration

Match authenticated user scope, confirmed lowercase Jira source, project and actual issue type ID. Distinct IDs remain distinct even when names match. Read compatible `storyType` or `issueType` records; if both exist they must agree. Preserve `storyType` when saving Story facts so existing consumers continue to work. Non-Story entries may use `issueType`; do not overwrite a Story profile with Test/Bug metadata or rename the shared schema.

Trusted opaque platform identity has priority; never read, parse or store JWTs, credentials or raw headers. A genuinely user-isolated workspace may use `identityScope.kind=user-workspace-isolated` without inventing a value. A real platform-provided staff ID remains query-only identity inside that authenticated user's scope, never a cross-user cache key. Do not routinely ask for staffId or Conversation-ID; missing required identity injection is a platform/tool gap.

| Reusable component | Rule |
| --- | --- |
| Source/project, actual type name/ID, field identities/types and applicable option encodings | Reuse matching verified components across create/update. A missing separate update entry is not a complete cache miss. |
| Requiredness, operations, permissions and defaults | Preserve their evidenced capability and true/false/unknown values. Create metadata does not verify edit permission or authorize target changes. |
| Confirmed reusable preference | Subordinate to current explicit input; a one-off issue value is not automatically a preference. |
| Target, content, attachments, score or confirmation | Obtain from current evidence, never profile defaults. |

Do not refresh by time, operation name or habit. Query only a genuinely missing/inapplicable component, explicit refresh/changed destination or precise actual schema error under [discovery](common-project-issue-type-validation.md). Current target content/inventory remains separate from cached configuration.

## Persist verified facts without losing prior entries

Save compact schema facts as soon as successful discovery verifies them, even before export. Retain the root `profiles` array and update only the matching identity/source/project/type/capability record or its verified components. Preserve unrelated records, unknown properties and earlier usable facts. Failed/incomplete responses must not replace a good profile or set `metadata.complete=true`.

This incomplete shape illustration is not reusable evidence:

```json
{
  "profiles": [{
    "identityScope": {"kind": "user-workspace-isolated"},
    "jiraSource": "wpb",
    "project": {"key": "<actual project key>", "name": "<actual name>"},
    "issueType": {"name": "<actual non-Story type>", "id": "<actual type ID>"},
    "capability": "create",
    "metadata": {"complete": false, "fields": []},
    "confirmedDefaults": {}
  }]
}
```

For Story retain `storyType` and actual `metadata.acceptanceCriteriaFieldId`; do not impose an AC mapping on unrelated types. Compact descriptors keep fieldId/name/schema type/items and evidenced required/operations/default/used-option representation. Cache applicable technical facts, not full query responses or business-bearing field values.

Preserve real `lastSuccess`; update it only from confirmed Jira issue and required attachment outcomes. Query success, a score or a profile save is not Jira-write success. A historical ticket in `lastSuccess` is audit data, never the next update target. Linkage/queue observations remain separate.

Never cache business prose, input JSON, source/attachment contents or paths, current target defaults, assessment records, session/conversation IDs, tokens or headers. Non-secret staffId may be retained only when actually exposed by trusted context and needed by a query schema in the same user's scope; it never enters issue JSON or score/export arguments.

Read the saved profile back. Save failure retries only that save, not discovery, scoring or Jira writes. If persistent user storage is unavailable, use verified current-run facts and report that future runs cannot rely on a saved cache.

## Ask only unresolved facts

Batch missing source/project facts and actual ambiguous project/type choices into a focused platform question. Build selections from actual returned IDs/names; do not hide same-name types or invent fixed projects. Reuse already resolved values without another settings-confirmation popup. Unresolved required business values need a focused clarification rather than a guess. [Final content confirmation](common-preview-confirm-export.md) remains separate.
