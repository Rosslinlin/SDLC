# Persist the upstream Jira assembly profile after success

Generation owns profile loading, project/type/metadata discovery, field-value precedence and settings confirmation before scoring. Its Skill resource references/jira-json-assembly.md is authoritative; read through supported Skill-name/resource access or use the parent's full applicable text. Do not repeat discovery at export or assume sibling filesystem access.

## Eligibility and identity

Save only the already verified exportContext.assembly snapshot for this exact candidate/JSON revision, after actual issue and every required attachment operation succeed. Failed/partial/unknown issue or required-attachment outcomes never overwrite an older successful profile. Database linkage/queue/async results remain separately reported; pending linkage does not erase a confirmed issue or independently eligible profile. A profile-save failure never causes Jira replay.

Use the real persistent user workspace ceaia-sdlc-jira/profiles.json, not conversation storage. Loading matches authenticated identity, Jira source, project and actual Story type ID first, then reuses applicable components across create/update. Saving additionally scopes operation-specific facts to their evidenced capability; preserve every other profile and capability. Trusted JWT subject has priority without reading/parsing/storing a token. Use a real staffId fallback only within the same user's identity scope, or a genuinely user-isolated platform workspace. Do not share by staffId alone. A profile may retain staffId for actual discovery-tool schemas and identity resolution; it is not copied to the Story JSON root or added to score/export arguments. Session/conversation IDs are not reusable defaults and never belong here.

If the platform cannot persist user-level files, report persistence unavailable and keep the actual operation result. If the upstream snapshot is missing, incomplete or mismatched, return that profile issue to generation; do not query or reconstruct facts from successful business content.

## Compact reusable facts

Keep source/project and Story-type binding, applicable metadata descriptors, operation-specific verified capability and confirmed reusable defaults. Stable project/type IDs, AC field ID, field identities/types and applicable option encodings can be reused across modes. Retain descriptors with fieldId/name/schema.type/items/required/operations, observed default and used option ID/representation, but preserve the evidence scope of requiredness, writable operations, permission and defaults. A create descriptor is not edit permission; a create default never overrides an existing target value. Preserve exact true/false/unknown values rather than promoting a successful create into blanket update capability.

Never cache Story/AC text, payloads, attachment contents/paths, source registers, credentials or conversation paths. The file's root acceptanceCriteria must equal the actual metadata-confirmed AC field ID, and its business value lives at fields[that ID]. Cache this mapping, not its content. Neither old profile defaults nor identity metadata authorize adding fields.reporter to Story JSON. A legacy profile missing that actual ID is incomplete for this format: generation resolves the missing metadata before scoring, without guessing an ID or borrowing the screenshot value. Export never repairs it by querying or editing the scored file.

Use the existing compact profiles-array shape from [empty template](../templates/profile-template.json), updating the matching identity/source/project/type/capability entry in place without erasing other entries:

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

This incomplete example is not reusable evidence. Its capability=create scopes operation-specific facts; it does not prevent an update from reusing confirmed stable components. metadata.complete describes the recorded capability, not all Jira operations. Populate descriptors and actual nonempty acceptanceCriteriaFieldId from upstream evidence before complete=true; that ID must match the file's root acceptanceCriteria and mapped fields property. A descriptor marked acceptanceCriteriaMapping=true agrees with it. identityScope.kind may be jwt-sub, staff-id-fallback or user-workspace-isolated; omit value for the last kind unless a real opaque identifier is exposed. Leave unsupported optional values null/omitted. selectedOption retains only an observed ID and exact transportFormat, never a guessed option.

lastSuccess records only actual ticket/status and separately observed linkage/queue state. Retain an older bindingStatus only as historical data, never as a command to send B. Unknown results stay unknown. Confirm the saved user-level file by read-back; report an unavailable or failed save separately.

Generation reads this file before create/update discovery, combines reusable components with actual baseline facts and performs zero discovery queries when those facts are sufficient. A mode change or missing separate update entry does not invalidate stable cached components. Query only a genuinely missing/inapplicable component, explicit refresh/changed destination or precise actual schema error; never rerun all three queries merely to fill an update capability. queryJiraCreateMetaFields cannot verify edit permission. Actual getJiraInfos/readJiraAttachments target reads remain necessary when the baseline/inventory is not already available. A saved profile never chooses an update target, authorizes changed relationships or replaces current content/upload confirmation.
