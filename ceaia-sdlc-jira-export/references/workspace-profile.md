# SDLC Jira Export Workspace Profiles

Use this profile cache only for export of independently reviewed SDLC Stories. It avoids repeating project, Story-type, and create-metadata queries across sessions while keeping each run tied to the user's current destination and ticket choice. Field value precedence is owned by `fields-and-rendering.md`; this profile section must not introduce a competing precedence rule.

## Storage and scope

Use the real user workspace (not this conversation's workspace) at `ceaia-sdlc-jira/profiles.json`. Load only the profile matching the selected Jira source, project, Story issue type ID, operation capability (`create` or `update`), and the authenticated user's identity scope. Preserve other profiles when updating the file. The profile shape is defined below; the [profile template](../templates/profile-template.json) is an empty starter, not a ready-to-use profile.

Scope identity by the real authenticated user: JWT subject (`sub`) takes priority; use `staffId` only as a fallback when it is the actual request identity. Never parse or persist a token, invent an identity, or reuse a profile across users based only on `staffId` when actual identity is available. If identity cannot be read, rely on the platform's genuinely user-isolated workspace. If there is no real user-workspace read/write capability, do not claim cross-session persistence. Continue an otherwise valid current export using available real metadata, then report that saving/reusing the profile was unavailable.

Resolve the real `staffId` before queries/writes from trusted current identity/context or a confirmed default in this same user's profile; if missing, collect it once with a focused question. It is an allowed non-secret routing default, never a substitute for the runtime's real authenticated identity. Resolve the actual current conversation ID from platform context before attachment transport and the mandatory binding; never reuse a saved older conversation ID or manufacture one from paths/ticket keys. Missing identity/context blocks the affected operation rather than producing a placeholder.

The legacy `jira-user-info.md` may supply routing defaults only (source, project, Story type, and explicit Epic/Parent choices). It is not field schema: its absence of a schema is a cache miss. Do not migrate field definitions from it.

## Lookup policy

Treat routing and metadata as separate cache components: project/source binding, Story type binding, and operation-specific field schema. Reuse each applicable, complete, matching component independently. For example, if routing is confirmed but the field schema is missing, query only create metadata; do not repeat the project and Story-type queries. If only project binding is missing, do not refresh an already verified schema unless the changed project/type makes it inapplicable. A fully matching profile skips all three Jira query tools across sessions. Do not refresh by TTL. Once a successful export has returned the ticket and its planned attachments have succeeded, save/update the relevant components. Do not re-read an Epic just to validate a cached default.

For every export, confirm with the user the current Jira source, source Story, project, Story-versus-ticket target (create or update and the selected ticket key for update), Epic choice, and actual dynamic field values. A prior ticket key/history never determines the next target. A saved value is a default, not permission to choose the destination or update target silently. For update, read the upstream generation handoff's current-ticket baseline and attachment inventory from the current conversation workspace; preserve their fields and attachments unless the reviewed update explicitly changes them. Do not overwrite existing values with create defaults. Do not re-read Jira remotely merely because export is a new stage/session; reuse completed upstream Jira reads for the same update.

The supported `almType` values are lowercase `wpb`, `alm`, `data`, `fcr`, and `go`. Resolve the source from explicit current input, validated handoff, or applicable profile. If it is missing, ask for it; never infer it from a Ticket key prefix.

On an initial project lookup, use the actual `queryJiraProjectsByName` results. If there is one unambiguous result, retain it without a project-choice popup; show it in the normal settings confirmation. If several returned projects could match, present all relevant returned key/name choices in a dynamic popup, preserving duplicates only when the backend distinguishes them. Never invent or silently choose between ambiguous results.

For the issue-type lookup, use only the actual returned Story type(s). Do not offer another issue type as fallback. If no Story type is returned, block and report that this project has no available Story type. Preserve entries with the same display name and different IDs; do not deduplicate them. If more than one Story ID remains, show the actual ID/name choices for selection and confirmation.

For an initial create with no saved, user-confirmed Epic decision, collect either a real Epic key or an explicit no-Epic decision. Validate a newly supplied Epic key once before use. A matching profile hit must not trigger another Epic read. For an update, use the current value in the upstream baseline or preserve it as unavailable; a create default is not an implicit relationship change.

Use the cache directly for known schema and values. Query only when the profile lacks a needed field/value, the user changes project/source/Story type, the user explicitly asks to refresh, or an actual Jira validation failure identifies stale metadata. Do not query merely because a profile is old. Unknown dynamic values are not grounds to guess: query the relevant metadata/option source when available. If validation fails, refresh only the affected lookup where possible.

## Fresh-query contract

Use exactly these parameters; do not invent filters or infer other query arguments:

| Lookup | Parameters |
| --- | --- |
| `queryJiraProjectsByName` | `staffId`, `almType`, `projectName` |
| `queryJiraIssueTypesByProject` | `staffId`, `almType`, selected `projectKey` |
| `queryJiraCreateMetaFields` | `staffId`, `almType`, `projectKey`, and `issueType` or `issueTypeId` (use the supported selected Story identifier) |

Parse wrapped metadata responses before using them. Treat `result.fields.values[]` as the field list. Record completeness only when the actual tool contract explicitly guarantees a complete result or the returned pagination evidence establishes that all pages are closed/read. Do not assume the tool always paginates. If completeness cannot be established from the response or documented contract, record `complete: false` and the specific evidence limitation; do not claim data is complete or reusable as a complete schema. Use only pagination controls present in the live tool schema; if no paging parameters are exposed, do not invent them. Preserve the exact `required` flag from metadata. Never infer requiredness from a successful payload, an omitted field, a default, or prior ticket contents.

## Required and optional fields

Build the payload from a complete, validated metadata snapshot. Retain every required field descriptor, even when the previous successful payload omitted it because Jira supplies a default, plus the AC field and selected optional fields. For every cached field retain its actual field ID, display name, full schema type and `items` type when present, exact required flag, exact operations, and the selected option ID plus transport encoding for every option actually used (such as an ID object or ID array). Also retain the schema-compatible Jira default only when explicitly returned and applicable. Do not store just a link/reference to another schema document; the profile must contain the compact descriptors needed to assemble fields without another query. The precedence for required and optional field values, provenance labels, and unresolved-value handling is exactly the order in `fields-and-rendering.md`; use it without alteration. This profile stores schema evidence and reusable confirmed defaults, it does not override that value resolution order.

Optional fields are omitted unless allowed by `fields-and-rendering.md`. Cache reusable confirmed defaults and selected option transport only after the relevant export succeeds.

Cache only compact field descriptors and the small option facts needed to assemble confirmed values. Do not save full raw metadata, all allowed values, Story text, acceptance-criteria text, attachment content, or conversation/workspace file paths. Do not fabricate hidden custom-field meaning from a display name.

## Acceptance-criteria mapping

Record the confirmed writable text-compatible Jira field mapping for Story acceptance criteria in the profile's field descriptors: field ID, display name, full schema (`type` and `items` where present), exact required flag, and exact operations proving that the field supports writing. A select/option field cannot hold the reviewed AC body and is not a valid substitute. Keep the Story's AC content in its own Jira field as required by the SDLC export contract; do not move it into Description. If no writable AC mapping is known, query complete create metadata. If still unavailable, report the blocker instead of guessing a field. Store the mapping only after a successful issue export and attachment outcome; a later binding failure does not erase that success.

## Profile lifecycle and status

Save/update a profile only from confirmed facts and a successful export whose issue result and planned attachments succeeded. A Markdown-review binding failure may be recorded as `bindingPending` while retaining the successfully exported issue/profile. Failed, partial, or unknown issue/attachment outcomes must not replace an older successful profile. Do not label incomplete metadata or incomplete query pagination as reusable/complete. Keep status and last successful result compact; never store secrets or full payloads.

If saving fails, retain the successful Jira result and tell the user the profile was not persisted. Never claim that a conversational value will survive sessions.

## Profile shape

`profiles.json` is an object with a `profiles` array. Keep one profile per identity/source/project/Story-type/operation-capability tuple; update that tuple in place and preserve the rest. Suggested structure:

```json
{
  "profiles": [
    {
      "identityScope": { "kind": "jwt-sub", "value": "<actual opaque user identity>" },
      "staffId": "<confirmed actual staff ID for this user>",
      "jiraSource": "wpb",
      "project": { "key": "PROJECT", "name": "Project name" },
      "storyType": { "name": "Story", "id": "<actual Jira issue type ID>" },
      "capability": "create",
      "metadata": {
        "complete": true,
        "fields": [
          {
            "fieldId": "summary",
            "name": "Summary",
            "required": true,
            "schema": { "type": "string" },
            "operations": ["set"],
            "acceptanceCriteriaMapping": false,
            "selectedOption": null,
            "confirmedDefault": null
          }
        ],
        "acceptanceCriteriaFieldId": "<confirmed field ID>"
      },
      "confirmedDefaults": {},
      "lastSuccess": {
        "status": "success",
        "bindingStatus": "pending",
        "ticketKey": "<returned ticket key>"
      }
    }
  ]
}
```

Use `identityScope.kind` values `jwt-sub`, `staff-id-fallback`, or `user-workspace-isolated`. For the last kind, omit `value` unless the platform supplies a real opaque user-scope identifier. Each field descriptor must carry a concrete `fieldId`, `name`, `schema` (`type`, plus `items` when present), exact `required`, and exact `operations`. `selectedOption` is either null/omitted or a selected, observed `{ "id": "<actual option ID>", "transportFormat": "id-object" }` / `{ "id": "<actual option ID>", "transportFormat": "id-array" }` (or another precisely observed format). Keep only options actually selected/used, not all `allowedValues`. `acceptanceCriteriaFieldId` must identify the same field marked `acceptanceCriteriaMapping: true`. Represent missing/unknown values as null or omit the property; never fill in example IDs or defaults. `confirmedDefaults` may hold only confirmed reusable field values in compact Jira-compatible form. Do not add paths, Story bodies, full option catalogs, tokens, or raw responses.
