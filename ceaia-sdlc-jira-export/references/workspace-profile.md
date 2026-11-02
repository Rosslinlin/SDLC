# User-workspace Jira profiles

Profiles cache verified routing/schema and user-confirmed defaults; they never authorize business changes. Field-value precedence belongs to fields-and-rendering.md.

## Scope and lookup

Use the real persistent user workspace at `ceaia-sdlc-jira/profiles.json`, not the conversation workspace. Select by authenticated identity, Jira source, project, actual Story type ID and operation capability (`create`/`update`); preserve other profiles. JWT subject takes identity priority without reading/parsing or storing a token. Use real request staffId as identity fallback, or a genuinely user-isolated platform workspace when identity is unavailable. Never share profiles across users merely because a staffId matches.

Resolve real nonempty staffId from trusted current identity/context or a confirmed same-user default; ask once if missing. Resolve the actual current conversation ID from platform context before Workspace attachment transport or B. Never save/reuse an older conversation ID or invent either identity value. Missing context blocks the affected operation. Without real user-workspace capability, use available real metadata for this export and report persistence unavailable; never claim a conversation file survives sessions.

Legacy `jira-user-info.md` supplies routing defaults only, not schema. Source is an explicit/current confirmed or applicable-profile value: WPB/ALM/DATA/FCR/GO maps to lowercase almType. Never infer it from a key prefix.

Reuse matching components independently: source/project binding, Story-type binding and operation-specific schema. A complete hit skips all three query tools and cached-Epic rereads across sessions. No TTL refresh. Query only missing/inapplicable components, explicit user change/refresh, or affected schema facts after a documented validation failure. Unknown selected options need supported lookup, not guessed IDs. A prior ticket never selects the next update target.

## Discovery

| Tool | Exact known inputs |
| --- | --- |
| java-base-mcp.queryJiraProjectsByName | staffId, almType, projectName |
| java-base-mcp.queryJiraIssueTypesByProject | staffId, almType, selected projectKey |
| java-base-mcp.queryJiraCreateMetaFields | staffId, almType, projectKey, and supported issueType or issueTypeId |

Use actual returned choices. One unambiguous project proceeds to normal settings confirmation; multiple plausible projects require selection. Offer Story types only, retaining different IDs even with identical display names. No available Story type blocks this route; never substitute another type.

Parse wrapped metadata (`result.fields.values[]` where returned). Complete means the live contract guarantees completeness or returned pagination evidence closes all pages. Otherwise record complete=false and the limitation; never invent paging controls. Assemble only from a complete validated applicable schema. `queryJiraCreateMetaFields` is the available schema query, not proof of edit permission: create requiredness/defaults do not authorize update changes, and create operations must not be described as independently verified edit capabilities. Resolve an actual edit/schema limitation through supported evidence or bounded recovery; do not invent an edit-metadata tool.

## Compact schema and defaults

Retain every required field, the writable AC mapping and selected optional fields. Each descriptor retains actual fieldId/name, schema.type and items when present, exact required flag and operations, an applicable observed default, and selected option ID/transport representation when used. A writable text-compatible AC field is mandatory; a select field is not a substitute. If unknown, query metadata; if still unavailable, report the blocker instead of moving AC into Description.

Keep only used option facts and confirmed reusable defaults, not full raw metadata/allowedValues. Never infer requiredness from a successful payload or an omitted field. Do not cache Story/AC text, secrets, payloads, attachment content or conversation paths. Omitted/unavailable update fields remain untouched.

Every export obtains one settings confirmation under fields-and-rendering.md. New creates resolve an explicit Epic/no-Epic decision; newly supplied Epic keys are verified once with supported getJiraInfos evidence. Applicable saved confirmed Epics need no reread. Update relationships are preserved unless reviewed changes authorize them.

## Save and reuse

Save/update a profile only after that item's issue and all required attachment operations are confirmed successful. Failed/partial/unknown issue or attachment results cannot replace an older successful profile. B failure may leave a valid profile with bindingStatus=pending; it does not erase A success. A profile-write failure is reported separately and never causes Jira replay.

Keep the existing compact shape: one object with a profiles array; update the matching tuple in place. The [empty template](../templates/profile-template.json) initializes storage, not verified values.

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

This incomplete shape is not reusable schema: populate fields with actual descriptors and establish completeness before complete=true. identityScope.kind is jwt-sub, staff-id-fallback or user-workspace-isolated; the last kind omits value unless the platform provides a real opaque identifier. A descriptor marked acceptanceCriteriaMapping=true must match acceptanceCriteriaFieldId. selectedOption is absent/null or an observed ID plus exact transportFormat (for example id-object/id-array); preserve actual Jira-compatible values without fabricated defaults. Unknowns are null/omitted. After eligible success, lastSuccess records the actual status/ticketKey and bindingStatus; never initialize it as a fictional success.
