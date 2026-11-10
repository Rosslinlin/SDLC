# Persistent user Jira configuration — required create/update closeout

`ceaia-sdlc-jira/profiles.json` is durable configuration in the authenticated **user Workspace**, shared across sessions by SDLC and ordinary Jira exports. It is not an optional cache, conversation artifact or process history. Keep this existing path for compatibility. The platform owns its database implementation; no database ID, token or direct database access is required from the user.

The user destination in this procedure applies only to that configuration file. Do not create outputs/ceaia, .ceaia-work, business artifacts, state, snapshots or attempt/verification records there. Keep those generated files in the current **session Workspace** and use its destination binding again when recording profilePersistence evidence. The state field scope=user identifies the profile target, not the storage scope of the state file.

Generation owns configuration selection, metadata, reusable-default consent and the revision-bound Assembly snapshot. Read its `references/jira-json-assembly.md` through supported Skill-resource access or the parent's applicable full text; do not assume sibling filesystem access. Export never repeats discovery or modifies scored business files to save a profile.

## 1. Use the actual Workspace file operations

Use the platform's actual, currently executable file/folder operations at the **user Workspace** destination, whether exposed as tools or supported native save/open/read actions. A supported destination selector or implicit authenticated user namespace is sufficient; no specially named “user-level” tool, returned scope field, user/database ID or separate “read-back” API is required. Read-back is retrieving/opening the saved file through that supported mechanism. No visible tool call does not prove inability or success. Reuse an observed user-file operation only when the current agent can execute that same mechanism. Follow actual schemas/actions; never invent hidden APIs, native directives, calls or results. Chat claims, code blocks and path text alone are not saved-file evidence; conversation/output storage is not the user destination.

Check the actual platform-provided operations, including native actions, and attempt the supported lookup/read of `ceaia-sdlc-jira/profiles.json` before declaring persistence unavailable; tool naming or a missing scope marker is not failure. Not-found permits initialization, using a supported folder operation or automatic parent creation. Preserve uncertain existing content on permission/transport/malformed-data errors. Successful writing proves only write capability. A genuinely missing read operation is a specific pending step (`read-unverified` after acknowledged writing), not proof that all user storage is unavailable; do not overwrite an uninspected profile or claim saved without verification.

## 2. Eligibility and mandatory closeout

For **both create and update**, actual unambiguous issue success triggers profile persistence before the final response or a partial-result return. Attachment, database-link, queue or asynchronous synchronization failure does not prevent saving independently verified configuration. Those effects remain partial/failed/unknown. Failed/unknown issue outcomes cannot record a new successful export; use existing supported read-only reconciliation first. Profiles never authorize replaying an issue or upload.

Retain this candidate/JSON revision's verified Assembly configuration snapshot and actual issue result. Prefer that snapshot. If incomplete, save only independently verified useful facts already available, such as authenticated scope, confirmed source/project and actual issue-type binding, with `metadata.complete=false`. Do not infer schema, requiredness, edit permission or reusable-default consent from business text or success. A populated partial configuration is useful; an empty array, placeholder IDs, `lastSuccess` alone or conversation-history pointer is not a saved profile. Missing facts stay unknown for later preparation; no discovery at export.

Use existing candidate state and the same export attempt's evidence. `profilePersistence` is additive:

| Field | Meaning |
| --- | --- |
| status | `pending`, `saved`, `failed`, `unavailable` or `not_eligible`; issue success starts `pending`. `not_eligible` needs an actual reason, such as no confirmed issue success or explicit no-Jira intent. |
| scope / path / profileKey | Actual authenticated user scope, `ceaia-sdlc-jira/profiles.json`, selected identity/source/project/type/capability tuple. |
| snapshotRecordPath | Revision-bound Assembly evidence; a local recovery pointer, never saved in the user profile. |
| writeEvidence / readBackEvidence | Actual Workspace operation/destination and result pointers, including verification of preserved unrelated entries; no special scope-return property is required. |
| error / retryUsed | Precise outstanding problem and bounded profile-only retry state. |

Initialize missing fields in old state without resetting remote results/counters. `saved` requires the user-level read-back below. Finish with `saved`, or explicitly report the precise pending/failed/unavailable save and next action while preserving Jira results. Do not label the entire workflow fully complete with a pending save. Do not create another export attempt for profile persistence.

## 3. Fresh-read → merge → write → read-back

1. Fresh-read the actual user file with an actual supported Workspace read/open operation immediately before merging. For confirmed not-found, use the [empty template](../templates/profile-template.json) only in memory and create the missing parent through a supported folder operation or automatic parent creation. For a valid file retain every other profile and unrelated supported property. A denied/uncertain/malformed read does not authorize replacement.
2. Upsert by authenticated identity + normalized confirmed source + actual project key + actual issue type ID + evidenced operation capability. Read legacy Story entries with only `storyType`; new Story entries write canonical `issueType` and identical `storyType` compatibility alias. Never treat Epic/Bug as Story, duplicate a legacy entry solely due to the alias, or merge conflicting IDs. Preserve other projects/types and operation entries; applicable stable facts can be reused across create/update.
3. Merge verified routing, applicable descriptors and **explicitly confirmed reusable defaults** from the snapshot. Missing snapshot facts never erase richer verified facts in the same existing entry, downgrade its metadata completeness merely because this run omitted schema, or clear saved defaults without an explicit removal. Actual inapplicability/schema-error evidence remains scoped and is not masked by old facts. Unknowns stay unknown; omit one-off values and business content. A single export value is not automatically a future default. An explicit change/removal of a default updates only that selected operation/profile; never resurrect a cleared value from an older entry. Update baseline values never become defaults automatically.
4. Write the merged document at that actual user Workspace destination; no extra scope-return field is needed. Use revision/conditional writing only if supported; on conflict re-read and merge once under the bounded retry rule. Do not invent locking or atomicity.
5. Retrieve/open the saved file with the actual supported reader or native file view and verify its tuple, routing/type, metadata/defaults and retained unrelated entries. Only then set `profilePersistence.status=saved`. A write acknowledgement/echo alone is insufficient; unavailable retrieval after acknowledged writing stays pending with `read-unverified`, preserving the known write result.

Keep actual attempts, destination/results/errors and snapshot in existing state. Identify missing operations after checking actual platform-supported mechanisms, not their names or visible tool-call history. A transient failure permits one bounded profile-only retry, fresh-reading before merge; unknown writes are read before retry. Resume only save/verification, never scoring/Jira/uploads. Another partial backend effect cannot bypass this closeout.

## 4. Useful content and compatibility

Keep trusted platform identity scope, source/project/actual `issueType`, operation capability, applicable field descriptors, actual AC mapping and explicitly selected reusable defaults. SDLC does not collect or add `staffId` to new configuration or use it as a required/default/fallback identity key. A profile without staffId is valid when its authenticated user scope and applicable configuration are established. `storyType` is a Story-only compatibility alias. SDLC selects Story; generic exporter entries in the same file may select Epic, Bug and other actual types. Do not silently select the last-used project when several destinations apply; entry owns selection and current settings confirmation.

Descriptors retain actual fieldId/name/schema.type/items/required/operations and applicable observed default/used option representation. Requiredness, writable operations, permission and defaults retain evidence scope. A create descriptor is not edit permission; create defaults never overwrite target values. `metadata.complete` describes only the recorded capability. The actual `acceptanceCriteriaFieldId`, when known, agrees with root `acceptanceCriteria` and its mapped field in the scored SDLC JSON. Missing AC mapping makes the profile incomplete, not permission to guess or discard useful routing facts.

Example shape only; placeholders are never saved as facts:

```json
{
  "profiles": [{
    "identityScope": {"kind": "user-workspace-isolated"},
    "jiraSource": "wpb",
    "project": {"key": "<verified key>", "name": "<verified name>"},
    "issueType": {"name": "Story", "id": "<verified ID>"},
    "storyType": {"name": "Story", "id": "<same verified ID>"},
    "capability": "create",
    "metadata": {"complete": false, "fields": [], "acceptanceCriteriaFieldId": null},
    "confirmedDefaults": {}
  }]
}
```

Use a trusted opaque platform subject or a genuinely authenticated user-isolated namespace without reading/parsing/storing JWTs or inventing identity values. Never ask the user for staffId to create, match or verify a profile. Preserve legacy staffId properties and staff-based entries already in this shared file as compatibility data; do not erase other projects/operations or rewrite the store merely to remove them. Reuse their stable facts only when ownership is independently established by the authenticated platform scope; a staff number alone is not ownership proof. An actual inability to establish authenticated scope is a precise technical gap, not a reason to collect a staff number. Session/conversation IDs and rotating transport identities are not persistent profile keys. Do not copy identity into business JSON or score/export arguments.

Do not save Story/AC/description text, full payloads, attachment contents/paths, source registers, credentials, conversation paths or operation history here. `lastSuccess` may retain the actual operation/ticket/status and separately observed downstream states; issue success alone never claims full attachment/linkage completion. This profile stores reusable export configuration; canonical business JSON and full remote responses remain in existing conversation Workspace/history locations. A profile never chooses an update target, changes relationships or replaces current content/upload confirmation.
