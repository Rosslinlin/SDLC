# Resolve JSON routing and fields from user profiles

Read only for local JSON preparation. Direct existing-Jira assessment uses its ticket/source and does not run these discovery queries, prepare a JSON or collect query-only identity. Complete trustworthy JSON can reuse its actual upstream schema evidence without repeating discovery.

## 1. Read known facts first

Use current confirmed context and the real persistent user-workspace `ceaia-sdlc-jira/profiles.json`, not a conversation-local cache. Before any discovery query or question asking the user for Jira source, project or AC-field metadata, read that file through an actual supported user-Workspace file operation, or reuse a verified read of the same file from the current creation handoff/preparation. This also applies when a creation workflow loads the reviewer before producing its final JSON. Resolve the user-Workspace destination separately from the session Workspace; a missing session copy, a path mentioned in chat or an unattempted read is not a profile cache miss. Keep the read result for the current batch so each candidate does not repeat it. Match authenticated user, confirmed Jira source, project and actual issue type ID. Read either legacy `storyType` or compatible `issueType` records; if both exist they must agree, otherwise resolve that conflict instead of choosing a convenient ID. For Story facts, retain the existing `storyType` shape when saving so SDLC readers keep working; preserve unrelated records and do not rename the shared file or rewrite its schema.

Reuse components independently across create/local-update preparation:

| Component | Reuse rule |
| --- | --- |
| Source/project, issue type name/ID, actual AC field ID, field identities/types and applicable option encodings | Reuse matching same-user facts, including a successful create profile during local-update preparation. Missing a separate update entry is not a whole-cache miss. |
| Requiredness, writable operations, permissions and defaults | Retain their evidenced operation scope. Create metadata is not edit permission; a create default cannot overwrite a local revision or target value. |
| Story content, actual update target, attachments and assessment | Take from the selected current input/confirmed mapping, never from profile defaults. A profile is not a current score or a Jira-content snapshot. |

Trusted opaque platform identity has priority; never parse/store tokens. A staff-ID fallback must remain inside the same authenticated user's scope, not match another user by staff ID alone. A genuinely isolated user workspace may use `identityScope.kind=user-workspace-isolated` without inventing a value. If real persistence is unavailable, report that limitation and reuse current verified facts for this run; do not pretend a conversation file persists across sessions.

With sufficient input/profile facts, call none of the three discovery tools. Do not refresh by TTL, request mode or habit. A confirmed not-found or no applicable profile permits querying only missing facts. If the user-Workspace read fails or is unavailable, retain that actual limitation and use verified current-run facts before querying only what remains missing; do not silently treat a skipped or uncertain read as not-found. Query only a genuinely missing/inapplicable component, an explicit refresh/changed destination or a precise actual schema-error recovery. Record the missing fact and avoid re-querying already known project/type merely to complete an operation-capability label.

## 2. Query only the missing component

Confirm source as WPB/ALM/DATA/FCR/GO from explicit context, an applicable confirmed preference or a focused choice. Do not infer it from a ticket prefix. JSON/query backend values use the lowercase form.

| Missing fact | Existing tool and known inputs |
| --- | --- |
| Actual project | `java-base-mcp.queryJiraProjectsByName`: `staffId`, `almType`, `projectName` |
| Actual issue type ID/name | `java-base-mcp.queryJiraIssueTypesByProject`: `staffId`, `almType`, `projectKey` |
| AC mapping or field descriptor/type/options | `java-base-mcp.queryJiraCreateMetaFields`: `staffId`, `almType`, `projectKey`, and supported `issueTypeId` or `issueType` |

Use the actual exposed schema; do not invent query arguments or pagination. A known confirmed project key can feed the type/metadata query directly; do not invent a project-key parameter for a project-name search. If a response uses `result.fields.values[]`, read those actual descriptors. Establish completeness from the response contract/closed pagination, not an arbitrary truncated list.

Ask only unresolved user facts together: source, project name/known key, an actual ambiguous project/type choice, or real staffId when a needed query requires it. Reuse trusted identity and confirmed values. Do not ask business users to invent technical IDs, retype their Story or choose export mode. Keep exact distinct type IDs even when names match. A requested Story uses the actual matching type; do not change type to obtain SKIPPED or fabricate an AC field for an inapplicable type.

The metadata query resolves field identity/representation. It is not an edit-permission check, a Jira write or proof of scoring. It does not authorize content changes or export. Use focused errors for unavailable schema; never substitute sample IDs or manufacture placeholders.

## 3. Save verified schema facts

Cache stable facts as soon as a successful actual discovery response verifies them, so a review-only request can reuse them next time. Update only the matching user's project/type entry and verified components; preserve earlier usable facts and other entries. Keep metadata requiredness/operations/defaults scoped to the actual query capability; do not mark update permission verified by a create query. An incomplete query does not set `metadata.complete=true`.

Store compact descriptors and the actual `metadata.acceptanceCriteriaFieldId`, not the whole response or Story body. Preserve legacy `storyType`, identity scope, confirmed defaults and any real `lastSuccess`; this reviewer never sets a Jira-write success merely because query/scoring succeeded. Do not overwrite a usable profile with a failed/unknown response. Store a preference only when explicitly confirmed, not inferred from a one-off field value.

Retain the shared profiles-array shape. This incomplete illustration is not reusable evidence:

```json
{
  "profiles": [{
    "identityScope": {"kind": "jwt-sub", "value": "<actual opaque identity>"},
    "jiraSource": "wpb",
    "project": {"key": "<actual key>", "name": "<actual name>"},
    "storyType": {"name": "Story", "id": "<actual type ID>"},
    "capability": "create",
    "metadata": {"complete": false, "acceptanceCriteriaFieldId": null, "fields": []},
    "confirmedDefaults": {}
  }]
}
```

Use actual field descriptors under `metadata.fields`: fieldId/name/schema.type/items plus evidenced required/operations/default and used option representation. Save a real staffId only when available within this user's identity scope. Preserve unknown requiredness instead of inventing false; `complete` refers only to the recorded capability. Use the actual opaque identity or the genuinely user-isolated workspace rule above, never the illustration's placeholder.

Read back the saved user-workspace file. A failed save retries only that local save, not discovery or scoring. Report cache persistence failure separately. Never cache credentials, raw authentication headers, session/conversation IDs, current target tickets, business prose, source attachments, JSON bodies, result records or backend scores as reusable settings. No `jira-user-info.md` is needed.

## 4. Assemble the selected file

Use the [new Story](../templates/story-json-template.json) or [local update](../templates/story-update-json-template.json) template and [source conversion](input-format.md). Templates are shapes, not source facts.

- Root `jiraInstance` is the confirmed lowercase source, `projectKey` is actual, and `issueType` is the actual type name. `fields.project.key` agrees; `fields.issuetype.id` is the actual ID string.
- Root `acceptanceCriteria` is the actual AC field-ID string; AC Jira wiki text is at `fields[acceptanceCriteria]`. Never reintroduce a `customFields` wrapper, root Summary/Description/AC array or independent BDD object.
- `fields.summary`, `fields.description` and the actual AC value are strings. Put the complete non-AC Story body, including Business Context, Pre-conditions and separately authored BDD sections, in `fields.description`; keep the complete authored AC section in the mapped AC field. Preserve source gaps using only representations allowed by the live contract; do not invent business content to pass validation. A required technical ID must be real before assembly.
- Omit root `staffId` and `fields.reporter`. Query/profile identity cannot become payload content.
- A new Story omits `issueIdOrKey`; a selected local update includes its exact confirmed target after `issueType`. Do not choose a target from a source reference or profile, and do not remove an intended target to pass validation as create.
- Other actual approved fields use their true IDs, types and options in `fields`; non-field business prose remains faithfully named within Description. `attachments` contains only supplied/authorized actual file paths, otherwise `[]`. Do not generate SPEC/Test Case files or attachment controls.

Save one real `.json4jira` object, read it back and retain the exact Workspace-relative path. Reuse a suitable existing file and its established identity rather than producing duplicate scoring copies. Complete Jira wiki encoding and needed field corrections before scoring; later export must consume the same unchanged input. Any actual scoring-file change requires a current assessment, not a fabricated checksum or old result.
