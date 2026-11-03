# Assemble final Jira JSON during Story generation

This step moves the former export-time project/type/field discovery, value resolution and payload assembly **before final scoring**. The exporter will only transmit the saved path. Use existing tools, no scripts, speculative fields or new query APIs.

## 1. Reuse profile components, then query only missing facts

### First entry or missing setup

Keep the default Jira-backed workflow. Do not offer a delivery-route choice between preparation-only and Jira-backed or recommend no-Jira because setup is missing. Use this short path:

1. Read real user-workspace profiles, trusted identity/context and already confirmed settings first. Reuse applicable known values; multiple actual destinations may need a project/settings selection, not a workflow-mode selection.
2. Ask only unresolved user inputs through ask_user_question, together when useful: Jira source (WPB/ALM/DATA/FCR/GO), real staffId if unavailable, and project name or already known key if no destination is established. A user can supply a project name; never require technical schema, Story type IDs or AC field IDs. Do not ask again for current confirmed values.
3. With those inputs, call the existing discovery tools only for missing project/type/field facts below. An already confirmed project key can feed the supported type/metadata queries directly; do not invent a project-key argument for the project-name query. Resolve ambiguous actual choices and required business values with focused questions. A missing technical mapping/service capability is WAITING_TOOL, not a reason to switch routes or have the user invent IDs.
4. Preserve current Jira intent and pending question state while waiting: jiraExportRequested defaults to true, while an explicit user no-export request stays false. Once enough information arrives, resume discovery/assembly automatically; no extra continue or route confirmation. Missing setup is not a completed final JSON or score.

Only honor preparation-only/no-Jira when the user explicitly requested it independently. Such a request still requires real destination/field data for a final scoreable .json4jira; incomplete drafts must remain identified as drafts. Final scored-content/upload confirmation is a later separate gate, unchanged by this entry rule.

Read matching components from real persistent user workspace ceaia-sdlc-jira/profiles.json, isolated by authenticated user, Jira source, project, actual Story type ID and create/update capability. Never use a conversation file as cross-session storage or another user's profile. JWT/platform identity takes priority; staffId is a real fallback identity only. Do not parse/store tokens. Without real persistence, use actual metadata for this run and report cache persistence unavailable.

Source is explicitly confirmed WPB/ALM/DATA/FCR/GO, not inferred from ticket prefix. Query calls use its lowercase almType. Resolve real staffId from trusted context or a confirmed same-user default, asking once if required by an actual discovery query or authorized reporter field. Do not write a root staffId: the backend no longer needs it in the Story file or score/export arguments. A create's fields.reporter.name remains the actual authorized identity when required/allowed by metadata; updates preserve the baseline unless its change is approved. Session IDs remain platform-owned and do not enter the file. Legacy jira-user-info.md supplies routing defaults, not field schema.

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

The developer screenshot and subsequent user clarification on 2026-11-03 supersede the earlier flat/Markdown samples: omit root staffId and encode Jira-visible rich text as Jira wiki before scoring. Use [JSON template](../templates/story-json-template.json) and [text encoding](jira-text-format.md). This structure is a required input contract for correct field recognition during scoring, not an additional Q1–Q7 business criterion. It is one JSON object with a fields object, never a stringified Jira request.

| JSON location | Required value / relationship |
| --- | --- |
| jiraInstance | Selected supported lowercase Jira source; do not add almType/jiraType aliases. |
| projectKey and fields.project.key | The same actual confirmed project key. fields.project is an object. |
| issueType and fields.issuetype.id | Confirmed Story type name at the root, its actual queried ID string inside fields.issuetype. Preserve the exact lowercase issuetype key; never substitute its display name for the ID or hardcode the screenshot ID. |
| fields.reporter.name | Actual authorized reporter identity in the confirmed create schema when required/allowed. This does not require a root staffId. Do not invent a reporter or use a string in place of the object. |
| fields.summary | Supported reviewed title as a string. No root summary. |
| fields.description | Supported non-AC business content as one Jira wiki string, e.g. h2. User Story, with real line breaks represented by JSON escaping. No unintended Markdown headings outside code/literal examples, array or JSON-encoded inner document. |
| customFields.acceptanceCriteria | The actual AC field ID string from applicable metadata/profile, not the AC text, an array or a field descriptor object. |
| fields[customFields.acceptanceCriteria] | The actual AC Jira wiki string, e.g. * *AC-001*: Given … When … Then …, with stable unique IDs and observable behavior. The mapped property must exist and be nonempty. |
| attachments | Complete authorized upload path list. A separate score-only specFilePath does not authorize upload. |

Resolve the AC field ID first, then use that exact string as the key in fields and store AC content there. For example, only when metadata really returns customfield_27708, customFields.acceptanceCriteria is that string and fields.customfield_27708 contains the AC Jira wiki. It is not a global default. Replace both identical <actual AC field ID> placeholders in the template with the complete observed ID; do not concatenate a guessed number. Missing mapping/value or incompatible type blocks production precheck; never fall back to an old top-level AC array or put the ID into the business prose.

customFields is a semantic-name-to-field-ID mapping, not a business-value container. Additional supported mappings use only the actual backend names; actual approved business values go in fields under their real metadata-confirmed IDs/types/options. Do not guess aliases or duplicate values at the root. Preserve authorized optional fields/relationships only in their confirmed schema locations. For an update, preserve baseline reporter/other unchanged fields and omission semantics under the approved delta; the create example does not authorize overwriting them.

Do not generate top-level staffId, summary, description, acceptanceCriteria or bddScenarios. Express supported BDD detail in the AC Jira wiki instead of maintaining a separate BDD object. Preserve all non-AC sections in fields.description, including those originally following AC; AC has one authoritative mapped field. Complete rich-text encoding during assembly, before review/score; the SPEC uses a faithful Markdown view of the same content. There is no late Markdown-to-wiki conversion, field partition, dynamicFieldsJson/attachmentsJson/issuesJson wrapper or regenerated payload at export. Examples are not business facts or fixed environment IDs.

Save/read back <outputRoot>/story.json4jira. It is one real JSON object, not a quoted JSON document and not a file with .md content. Remove every template placeholder. Record the actual immutable path in paths.storyJsonPath and reuse it exactly across score/preview/export. Backend validation remains authoritative; use precise returned errors for bounded incremental correction.

## 4. Snapshot and update guard

Save a numbered jira-assembly-revision record containing actual query/profile evidence, compact schema descriptors, requiredness/option encoding, field provenance, confirmed settings, JSON path/revision and authorized attachments. Set exportContext.assembly.status=verified only after completion; the exporter receives this record/profileKey for eligible profile persistence after confirmed creation/required attachments.

An existing-ticket request never becomes a create. Before making its final JSON, establish actual backend schema support for the exact update target, omission semantics and approved none/replace operations. The screenshots do not define these properties: do not guess issueIdOrKey, operation, replace objects or deletion flags. For none, keep upload attachments empty; a local SPEC is sent via specFilePath for scoring, never added to uploads merely to satisfy a scorer. Continue only when the scoring/creation contract can validate that package without uploading the scoring-only file. For replace, require confirmed original IDs and supported upload-before-delete behavior. Missing support is WAITING_TOOL for that update, not permission to fallback to create, add or generic export. Ready independent creates can still be prepared.

Cache loading/discovery belongs here. Save/update reusable profile facts only after actual successful issue plus required attachments, through the exporter's persistence procedure. Failed/unknown operations never replace an older successful profile; a queue/linkage or persistence issue is reported separately and never causes replay.
