# Persistent user Jira configuration — required create/update closeout

`ceaia-sdlc-jira/profiles.json` is durable configuration in the authenticated **user Workspace**, shared across sessions by SDLC and ordinary Jira exports. It is not an optional cache, conversation artifact or process history. Keep this existing path for compatibility. The platform owns its database implementation; no database ID, token or direct database access is required from the user.

Generation owns configuration selection, metadata, reusable-default consent and the revision-bound Assembly snapshot. Read its `references/jira-json-assembly.md` through supported Skill-resource access or the parent's applicable full text; do not assume sibling filesystem access. Export never repeats discovery or modifies scored business files to save a profile.

## 1. Resolve the actual user storage capability

Retain the real exposed read/write tools and their authenticated user-scope selector/destination evidence from preparation. Use those user-level capabilities for lookup, fresh merge, write and read-back. A filename, generic Workspace write acknowledgement or path under conversation/history/output storage does not establish user scope. Follow the actual exposed schema; do not invent a scope argument or tool name. If the platform injects the user namespace, its capability contract is sufficient evidence; do not demand an opaque identity it does not expose.

Not-found for the user file/parent means no saved configuration yet. A supported writer may create missing parents. Permission/transport/unsupported-scope errors and malformed existing JSON are distinct failures, never an empty profile. Preserve uncertain content; do not overwrite it or fall back to conversation files. Lack of actual user persistence is reported as unavailable; independently authorized Jira work can continue with current facts and this precise closeout limitation.

## 2. Eligibility and mandatory closeout

For **both create and update**, actual unambiguous issue success triggers profile persistence before the final response or a partial-result return. Attachment, database-link, queue or asynchronous synchronization failure does not prevent saving independently verified configuration. Those effects remain partial/failed/unknown. Failed/unknown issue outcomes cannot record a new successful export; use existing supported read-only reconciliation first. Profiles never authorize replaying an issue or upload.

Retain this candidate/JSON revision's verified Assembly configuration snapshot and actual issue result. Prefer that snapshot. If incomplete, save only independently verified useful facts already available, such as authenticated scope, confirmed source/project and actual issue-type binding, with `metadata.complete=false`. Do not infer schema, requiredness, edit permission or reusable-default consent from business text or success. A populated partial configuration is useful; an empty array, placeholder IDs, `lastSuccess` alone or conversation-history pointer is not a saved profile. Missing facts stay unknown for later preparation; no discovery at export.

Use existing candidate state and the same export attempt's evidence. `profilePersistence` is additive:

| Field | Meaning |
| --- | --- |
| status | `pending`, `saved`, `failed`, `unavailable` or `not_eligible`; issue success starts `pending`. `not_eligible` needs an actual reason, such as no confirmed issue success or explicit no-Jira intent. |
| scope / path / profileKey | Actual authenticated user scope, `ceaia-sdlc-jira/profiles.json`, selected identity/source/project/type/capability tuple. |
| snapshotRecordPath | Revision-bound Assembly evidence; a local recovery pointer, never saved in the user profile. |
| writeEvidence / readBackEvidence | Actual capability/destination and result evidence pointers, including verification of preserved unrelated entries. |
| error / retryUsed | Precise outstanding problem and bounded profile-only retry state. |

Initialize missing fields in old state without resetting remote results/counters. `saved` requires the user-level read-back below. Finish with `saved`, or explicitly report the precise pending/failed/unavailable save and next action while preserving Jira results. Do not label the entire workflow fully complete with a pending save. Do not create another export attempt for profile persistence.

## 3. Fresh-read → merge → write → read-back

1. Fresh-read the actual user file immediately before each save. For confirmed not-found, use the [empty template](../templates/profile-template.json) only in memory. For a valid file retain every other profile and unrelated supported property. A denied/uncertain/malformed read does not authorize replacement.
2. Upsert by authenticated identity + normalized confirmed source + actual project key + actual issue type ID + evidenced operation capability. Read legacy Story entries with only `storyType`; new Story entries write canonical `issueType` and identical `storyType` compatibility alias. Never treat Epic/Bug as Story, duplicate a legacy entry solely due to the alias, or merge conflicting IDs. Preserve other projects/types and operation entries; applicable stable facts can be reused across create/update.
3. Merge verified routing, applicable descriptors and **explicitly confirmed reusable defaults** from the snapshot. Missing snapshot facts never erase richer verified facts in the same existing entry, downgrade its metadata completeness merely because this run omitted schema, or clear saved defaults without an explicit removal. Actual inapplicability/schema-error evidence remains scoped and is not masked by old facts. Unknowns stay unknown; omit one-off values and business content. A single export value is not automatically a future default. An explicit change/removal of a default updates only that selected operation/profile; never resurrect a cleared value from an older entry. Update baseline values never become defaults automatically.
4. Write the populated merged document through the actual user-scope writer. Verify returned scope/destination or documented injected-user namespace. Use a returned revision/conditional write if actually supported; on conflict re-read and merge once under the bounded retry rule. Do not invent locking or claim unprovided atomicity.
5. Read the same authenticated user file back. Verify the selected tuple, real nonempty routing/type facts, expected metadata/defaults and preserved unrelated entries. A write acknowledgement, matching basename, empty template or conversation read-back is insufficient. Only then set `profilePersistence.status=saved` and report saved for future sessions.

On failure/unknown write/read-back, retain the snapshot, exact destination/error and profile-only next action in existing state. A transient error permits at most one bounded profile-only retry, fresh-reading before each merge. Otherwise stop that save and report its limitation. Resume only the missing user-save/read-back; never rerun scoring, Jira, uploads or normal discovery to manufacture missing evidence. A return for another partial backend effect must not silently drop this save.

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
