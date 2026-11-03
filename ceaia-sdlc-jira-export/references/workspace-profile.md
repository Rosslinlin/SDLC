# Persist the upstream Jira assembly profile after success

Generation owns profile loading, project/type/metadata discovery, field-value precedence and settings confirmation before scoring. Its Skill resource references/jira-json-assembly.md is authoritative; read through supported Skill-name/resource access or use the parent's full applicable text. Do not repeat discovery at export or assume sibling filesystem access.

## Eligibility and identity

Save only the already verified exportContext.assembly snapshot for this exact candidate/JSON revision, after actual issue and every required attachment operation succeed. Failed/partial/unknown issue or required-attachment outcomes never overwrite an older successful profile. Database linkage/queue/async results remain separately reported; pending linkage does not erase a confirmed issue or independently eligible profile. A profile-save failure never causes Jira replay.

Use the real persistent user workspace ceaia-sdlc-jira/profiles.json, not conversation storage. Match authenticated identity, Jira source, project, actual Story type ID and verified create/update capability; preserve every other profile. Trusted JWT subject has priority without reading/parsing/storing a token. Use a real staffId fallback only within the same user's identity scope, or a genuinely user-isolated platform workspace. Do not share by staffId alone. Session/conversation IDs are not reusable defaults and never belong here.

If the platform cannot persist user-level files, report persistence unavailable and keep the actual operation result. If the upstream snapshot is missing, incomplete or mismatched, return that profile issue to generation; do not query or reconstruct facts from successful business content.

## Compact reusable facts

Keep source/project and Story-type binding, operation-specific verified capability, complete applicable metadata descriptors and confirmed reusable defaults. Retain every required descriptor, the actual AC field-ID mapping required by the file contract, and selected optional fields with fieldId/name/schema.type/items/required/operations, observed default and used option ID/representation. Preserve exact true/false/unknown requiredness; success does not establish omitted-field rules or edit permission.

Never cache Story/AC text, payloads, attachment contents/paths, source registers, credentials or conversation paths. The file's customFields.acceptanceCriteria must equal the actual metadata-confirmed AC field ID, and its business value lives at fields[that ID]. Cache this mapping, not its content. A legacy profile missing that actual ID is incomplete for this format: generation resolves the missing metadata before scoring, without guessing an ID or borrowing the screenshot value. Export never repairs it by querying or editing the scored file.

Use the existing compact profiles-array shape from [empty template](../templates/profile-template.json), updating the matching tuple in place:

```json
{
  "profiles": [{
    "identityScope": {"kind": "jwt-sub", "value": "<real opaque identity>"},
    "staffId": "<real staff ID>",
    "jiraSource": "wpb",
    "project": {"key": "<actual key>", "name": "<actual name>"},
    "storyType": {"name": "Story", "id": "<actual ID>"},
    "capability": "create",
    "metadata": {"complete": false, "fields": [], "acceptanceCriteriaFieldId": null},
    "confirmedDefaults": {}
  }]
}
```

This incomplete example is not reusable evidence. identityScope.kind may be jwt-sub, staff-id-fallback or user-workspace-isolated; omit value for the last kind unless the platform exposes a real opaque identifier. Populate descriptors and the actual nonempty acceptanceCriteriaFieldId from upstream evidence before complete=true; that ID must match the file's customFields.acceptanceCriteria and mapped fields property. A descriptor marked acceptanceCriteriaMapping=true agrees with it. Leave unsupported optional values null/omitted. selectedOption retains only an observed ID and exact transportFormat, never a guessed option.

lastSuccess records only actual ticket/status and separately observed linkage/queue state. Retain an older bindingStatus only as historical data, never as a command to send B. Unknown results stay unknown. Confirm the saved user-level file by read-back; report an unavailable or failed save separately.

Generation reuses matching components across sessions without TTL refresh, querying only missing/inapplicable facts, explicit change/refresh or supported validation recovery. A saved profile never chooses an update target, authorizes changed relationships or replaces current content/upload confirmation.
