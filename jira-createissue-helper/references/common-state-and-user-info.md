# Durable user configuration and current request facts

Read at entry and post-write completion for **every** create/update/export route, including complete upstream JSON, Test Case files and batch manifests. A complete trustworthy file skips unnecessary preparation queries; it never skips user-Workspace lookup or successful-write persistence. [The JSON contract](common-json4jira-contract.md) controls file contents.

## Resolve current facts first

Read `ceaia-sdlc-jira/profiles.json` in the platform's **user Workspace** using actual platform-supported file/folder operations: ordinary tools or native save/open/read actions executable by the current agent. Ordinary tools are sufficient when their actual selector, documented implicit user namespace or an observed operation in this same user destination establishes where they act. Reuse the real binding from a successful user-Workspace operation in this session; do not require specially named user-level tools, a separate read-back API, a returned scope field or database proof. Read-back means reading the saved file with the normal reader. The same basename in conversation/output/history storage does not establish user persistence. Inspect actual supported operations, including native actions, and attempt the profile read before declaring unavailable. A missing visible call or tool-name marker proves neither inability nor saving. Chat creation claims, code blocks and paths alone are not file evidence; verify the actual user file. Follow supported schemas/actions without inventing selectors, identity arguments, hidden APIs, native directives or results.

An actual file/parent not-found means first use: create a populated profile when verified facts are available. It is not unsupported storage or permission to disable saving. Use supported automatic parent creation or an available folder tool for missing parents. Distinguish not-found from denied/unsupported access, malformed content and uncertain reads. Preserve an existing damaged/uncertain file rather than overwrite it with an empty array. A successful write proves writing, not reading; if no supported reader exists, report that specific limitation and do not overwrite an unknown existing profile.

Keep current source/project/type, selected paths/revisions, operation/target, approved changes, actual attachment mappings, any supplied assessment evidence, preview confirmation and observed write outcomes in existing preview/result/context. These are request facts, not reusable defaults; a profile does not impose a scoring prerequisite.

For Test Case source export, collect `uploadType` once for every new export request through [its constrained choice](flow-testcase-source-export.md). An explicit mode selection in that same request already supplies it. Do not reuse a prior request's mode or persist mode, selected cases or source content in profiles.

## Match the correct user's configuration

Retain the compatible root `profiles` array. Match authenticated user scope, confirmed lowercase Jira source, actual project key, actual issue type ID and evidenced capability. Multiple projects/types are separate records, not one active singleton. Distinct IDs remain distinct even when names match. Never choose the first/most recent profile or a historical `lastSuccess` as the current destination.

Write canonical `issueType: {id, name}` for every type. Story also retains an identical `storyType: {id, name}` alias for existing consumers. Read legacy Story-only `storyType` records compatibly; if both properties exist they must agree. Never infer an ID from a name or overwrite Story facts with Epic/Bug/Test metadata. Stable matching components can be shared across create/update without promoting create defaults or permissions to edit authority.

Trusted opaque platform identity has priority; never read, parse or store JWTs, credentials or raw headers. A genuinely user-isolated workspace may use `identityScope.kind=user-workspace-isolated` without inventing a value. A real platform-provided staff ID remains query-only identity inside that authenticated user's scope, never a cross-user configuration key. Do not routinely ask for staffId or Conversation-ID; missing required identity injection is a platform/tool gap.

| Reusable component | Rule |
| --- | --- |
| Source/project, actual type name/ID, field identities/types and applicable option encodings | Reuse matching verified components across create/update. A missing separate update entry does not invalidate stable facts. |
| Requiredness, operations, permissions and defaults | Preserve their evidenced capability and true/false/unknown values. Create metadata does not verify edit permission or authorize target changes. |
| Confirmed reusable preference | Subordinate to current explicit input. Save only after the user agrees to remember it for this source/project/type and operation; a one-off issue value is not a preference. |
| Target, content, attachments, score or confirmation | Obtain from current evidence, never profile defaults. |

Do not refresh by time, operation name or habit. Query only a genuinely missing/inapplicable component, explicit refresh/changed destination or precise actual schema error under [discovery](common-project-issue-type-validation.md). Current target content/inventory remains separate from durable configuration.

## Persist verified facts without losing prior entries

Retain a compact verified configuration snapshot in existing request context when reading profiles, discovering metadata or receiving an established upstream assembly. It contains real identity/source/project/type binding, verified descriptors, applicable explicitly remembered defaults and evidence scope. Complete upstream JSON can establish verified routing/type facts; it does not prove schema, requiredness or permissions. Save useful known components with `metadata.complete=false` rather than inventing metadata or rerunning discovery solely to fill a profile.

Save compact facts as soon as successful discovery verifies them, even before export. **After each actually successful issue create or update, profile persistence is a required completion step**, including each confirmed successful manifest child. Set `profilePersistence.status=pending` in existing result/context, merge its verified snapshot and record the actual issue result. Do not wait for attachments, Test Details, relationships, database linkage or queue completion: preserve their separate failed/pending/unknown states. A failed/unknown issue cannot create fictitious success evidence, although independently verified configuration may still be saved.

For initial creation and later updates use the same lifecycle:

1. Re-read the current real user-level file immediately before merging. Established not-found permits an in-memory `profiles` array, then a populated record; saving only `{"profiles":[]}` is not completion. Denied/uncertain/malformed reads never authorize overwrite.
2. Upsert only the matching identity/source/project/type/capability record or its verified components. Retain every other project/type/capability, unknown property and earlier usable fact. Missing snapshot facts never erase richer verified schema or downgrade its completeness merely because this run omitted metadata. Failed/incomplete discovery never replaces good facts or sets `metadata.complete=true`; actual inapplicability/schema-error evidence stays scoped. Keep `confirmedDefaults` operation-scoped: omit run-only overrides, preserve remembered values absent explicit change/removal, and never resurrect a deliberately cleared default from an older entry. Stable technical facts remain reusable across modes.
3. Write the merged `ceaia-sdlc-jira/profiles.json` with an actual executable file operation targeting that same established user Workspace; a supported native save route is valid. Create missing parents through its supported behavior or the actual folder tool. The selected/implicit user destination is sufficient; do not demand a separate returned scope field or invent a mkdir API.
4. Use the actual supported reader or native file view on that saved user destination. Verify its populated matching binding, schema/default facts, actual success evidence and preserved unrelated entries. Only this verified read-back permits `profilePersistence.status=saved`; a write acknowledgement or filename alone is insufficient. Retain the actual tool/destination/read result in existing context, without repeating capability verification each phase.

This incomplete shape illustration is not reusable evidence:

```json
{
  "profiles": [{
    "identityScope": {"kind": "user-workspace-isolated"},
    "jiraSource": "wpb",
    "project": {"key": "<actual project key>", "name": "<actual name>"},
    "issueType": {"name": "<actual type>", "id": "<actual type ID>"},
    "capability": "create",
    "metadata": {"complete": false, "fields": []},
    "confirmedDefaults": {}
  }]
}
```

For Story additionally retain the identical `storyType` alias and verified `metadata.acceptanceCriteriaFieldId`; do not impose these on unrelated types. Compact descriptors keep fieldId/name/schema type/items and evidenced required/operations/default/used-option representation. `metadata.complete` describes the evidenced capability, not all Jira operations. Persist technical facts, not full query responses or business-bearing field values.

Preserve real `lastSuccess`; update it only from a confirmed issue create/update and include its actual operation/key/status. It means **issue success only**, not complete attachment/linkage/queue success. Record those observed effects separately, including unknown/unreported. Query success, a score or a profile save is not Jira-write success. A historical ticket is never the next update target.

Never persist business prose, input JSON, source/attachment contents or paths, current target defaults, assessment records, session/conversation IDs, tokens or headers. A user-confirmed stable Epic Link or option ID can be a reusable preference for the exact project/type/operation; one-off use alone is insufficient. Non-secret staffId may be retained only when actually exposed by trusted context and needed by a query schema in the same user's scope; it never enters issue JSON or score/export arguments.

On failed/unknown write or read-back, retain the snapshot, actual attempted tool/destination/error and true Jira effects in existing context. Use `unavailable` only after inspecting actual platform-supported operations establishes that the specific operation cannot target user Workspace, or an actual unsupported-operation result proves it; never infer it from naming or a missing scope property. A write followed by unavailable/failed reading is unverified persistence, not saved; distinguish that from writing being unavailable. A transient fault permits at most one profile-only retry, re-reading before any merge. Resume only the missing save/read-back, never discovery, scoring, uploads or Jira. An unknown write is read before any retry; an uncertain initial read never permits overwriting an existing profile. Use `not_eligible` only when no verified reusable binding can be saved, with its actual reason. Do not finish a successful export silently leaving persistence unattempted.

## Guide configuration before creating

Read profiles before asking. Reuse already answered steps; one focused question may combine unresolved facts without repeating them.

1. Resolve Jira source, for example the supported WPB/ALM/FCR/GO/DATA sources; never infer it from a key.
2. Resolve project and actual requested issue type. Generic export supports Story, Epic, Bug or another actual returned type. If the request does not select a destination, show matching saved source/project/type combinations; ambiguity uses real names/keys/IDs. Do not silently pick a profile, hide same-name types or invent a fixed project/type menu.
3. After applicable field facts are available, offer optional additional fields in business terms: “还需要带上 Epic Link、Story Type、优先级或其他项目字段吗？可以填写，也可以跳过。哪些只用于本次，哪些希望记为这个项目和类型的默认值？” Offer only fields supported by actual metadata for this project/type/operation. Distinguish a custom Story Type field from Jira issue type. Resolve supplied values using actual field IDs, options and target evidence; users need not invent technical IDs. Skipped optional preferences do not block progress or erase existing defaults. A supplied unsupported mapping stays precisely pending, never guessed or silently dropped.

When a matching profile exists, show its effective source/project/type and remembered fields **before creating/authoring** and ask once per request: “本次使用【来源 / 项目 / 类型】，并沿用这些字段【实际字段和值】，可以吗？可直接沿用、修改，或只对本次调整。” An explicit current “按上次配置/沿用这些字段” answers this check if its exact matching configuration is clear. Batch requests can confirm each distinct configuration group together. Do not repeat a check already answered upstream for the same unchanged settings.

For a supplied complete JSON, display its actual effective settings; do not inject profile defaults or rewrite it to match a profile. Resolve real conflicts before proceeding. For updates preserve the selected target and unrequested baseline fields; remembered create values never overwrite them, and optional-field guidance covers only requested supported changes. Settings agreement is separate from [final file/content confirmation](common-preview-confirm-export.md), which remains after the actual package is available for review.
