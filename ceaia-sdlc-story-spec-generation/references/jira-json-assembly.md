# Assemble final Jira JSON during Story generation

This step moves the former export-time project/type/field discovery, value resolution and payload assembly **before final scoring**. The exporter will only transmit the saved path. Use existing tools, no scripts, speculative fields or new query APIs.

## 1. Reuse profile components, then query only missing facts

Read matching components from real persistent user workspace ceaia-sdlc-jira/profiles.json, isolated by authenticated user, Jira source, project, actual Story type ID and create/update capability. Never use a conversation file as cross-session storage or another user's profile. JWT/platform identity takes priority; staffId is a real fallback identity only. Do not parse/store tokens. Without real persistence, use actual metadata for this run and report cache persistence unavailable.

Source is explicitly confirmed WPB/ALM/DATA/FCR/GO, not inferred from ticket prefix. Query calls use its lowercase almType. Resolve real staffId from trusted context or a confirmed same-user default, asking once if required. This identity is for discovery; do not insert it or a session ID into Story JSON without an actual schema requirement. Legacy jira-user-info.md supplies routing defaults, not field schema.

| Tool | Exact known query inputs |
| --- | --- |
| java-base-mcp.queryJiraProjectsByName | staffId, almType, projectName |
| java-base-mcp.queryJiraIssueTypesByProject | staffId, almType, selected projectKey |
| java-base-mcp.queryJiraCreateMetaFields | staffId, almType, projectKey, and supported issueType or issueTypeId |

A complete matching profile skips all three queries across sessions; no TTL refresh. Reuse components independently and query only missing/inapplicable facts, explicit change/refresh or supported schema-validation recovery. One unambiguous project proceeds; multiple real choices need selection. Offer only actual Story types, preserving distinct IDs with identical names. No Story type blocks this route.

Use only live exposed query arguments. Where metadata returns result.fields.values[], read those descriptors. Establish completeness from the contract or real closed pagination; do not invent paging controls. Cache all required descriptors, AC mapping and selected optional fields with actual IDs/name/schema/required/operations/default and used option ID/encoding. Do not infer requiredness from an omitted successful value. Create metadata is not independently verified edit permission.

## 2. Resolve values before JSON assembly

For an update the approved manifest first limits changes. Apply current explicit approved values, then actual baseline (omit unchanged fields), then applicable confirmed defaults/observed Jira defaults, then directly evidenced valid options. Ask only for unresolved required facts, using actual valid choices. Omit unknown optional fields; never invent IDs, business choices or placeholders. A profile/create default never authorizes overwriting existing relationships.

Resolve create Epic/no-Epic explicitly. Validate a newly supplied Epic once through supported getJiraInfos evidence; do not reread an applicable already-confirmed Epic. Map Epic/Parent or other custom fields into **only** the property/ID/representation the active JSON/backend schema actually supports. Do not invent wrapper fields from old direct tool arguments.

Show actual source/project/Story type, operation/update target, relationships and field values for a Use/Change settings decision during assembly. Already explicit current choices need no repeated re-entry. This confirms assembly values; final upload content is previewed and confirmed separately after scoring.

## 3. Write the complete canonical file

Use [JSON template](../templates/story-json-template.json) as a structural starting point, not evidence or the complete backend schema. The screenshot sample uses jiraInstance, projectKey, issueType, summary, description, acceptanceCriteria, bddScenarios, attachments and customFields. Use these canonical sample names unless the real schema establishes otherwise. Do not add duplicate almType/jiraType aliases just because the query APIs or prose use them.

- jiraInstance is the selected supported lowercase source. projectKey/type identify the confirmed Story destination. summary is the reviewed title.
- description holds supported non-AC business content in schema-accepted form; acceptanceCriteria holds stable unique AC-### Given/When/Then entries. bddScenarios, when used/required, express those same conditions/outcomes rather than independent extra requirements.
- customFields uses only actual metadata-confirmed IDs/types/options and approved values. If the backend requires an AC custom field in addition to canonical acceptanceCriteria, derive the exact same AC content once using its documented mapping; never maintain conflicting copies or invent an acceptanceCriteriaFieldId property.
- attachments is the complete authorized upload path list under the active schema. Each listed path is uploaded by the backend. A scoring-only SPEC is provided through specFilePath and is not implicitly authorized for upload. See the update guard below.
- businessBenefit, marketsAffected, links or additional fields are included only when supported and their exact schema is known. Example percentages/markets/tickets are never defaults.

Produce the actual field representation now. Preserve Markdown where the backend JSON schema accepts Markdown; use Jira-specific encoding only where its contract requires it. Preserve all non-AC sections, including those following AC, and keep canonical AC content in its mapped field. There is no late Markdown-to-wiki conversion, field partition, dynamicFieldsJson/attachmentsJson/ issuesJson string wrapper or regenerated payload at export.

Save/read back <outputRoot>/story.json4jira. It is one real JSON object, not a quoted JSON document and not a file with .md content. Remove every template placeholder. Record the actual immutable path in paths.storyJsonPath and reuse it exactly across score/preview/export. Backend validation remains authoritative; use precise returned errors for bounded incremental correction.

## 4. Snapshot and update guard

Save a numbered jira-assembly-revision record containing actual query/profile evidence, compact schema descriptors, requiredness/option encoding, field provenance, confirmed settings, JSON path/revision and authorized attachments. Set exportContext.assembly.status=verified only after completion; the exporter receives this record/profileKey for eligible profile persistence after confirmed creation/required attachments.

An existing-ticket request never becomes a create. Before making its final JSON, establish actual backend schema support for the exact update target, omission semantics and approved none/replace operations. The screenshots do not define these properties: do not guess issueIdOrKey, operation, replace objects or deletion flags. For none, keep upload attachments empty; a local SPEC is sent via specFilePath for scoring, never added to uploads merely to satisfy a scorer. Continue only when the scoring/creation contract can validate that package without uploading the scoring-only file. For replace, require confirmed original IDs and supported upload-before-delete behavior. Missing support is WAITING_TOOL for that update, not permission to fallback to create, add or generic export. Ready independent creates can still be prepared.

Cache loading/discovery belongs here. Save/update reusable profile facts only after actual successful issue plus required attachments, through the exporter's persistence procedure. Failed/unknown operations never replace an older successful profile; a queue/linkage or persistence issue is reported separately and never causes replay.
