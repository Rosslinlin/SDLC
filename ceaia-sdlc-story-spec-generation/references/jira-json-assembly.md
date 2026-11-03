# Assemble final Jira JSON during Story generation

This step moves the former export-time project/type/field discovery, value resolution and payload assembly **before final scoring**. The exporter will only transmit the saved path. Use existing tools, no scripts, speculative fields or new query APIs.

## 1. Reuse profile components, then query only missing facts

### First entry or missing setup

Keep the default Jira-backed workflow. Do not offer a delivery-route choice between preparation-only and Jira-backed or recommend no-Jira because setup is missing. Use this short path:

1. Read real user-workspace profiles, trusted identity/context and already confirmed settings first. Reuse applicable known values; multiple actual destinations may need a project/settings selection, not a workflow-mode selection.
2. Ask only unresolved user inputs through ask_user_question, together when useful: Jira source (WPB/ALM/DATA/FCR/GO), real staffId only if an actually needed query requires it, and project name or already known key if no destination is established. A user can supply a project name; never require technical schema, Story type IDs or AC field IDs. Do not ask again for current confirmed values.
3. With those inputs, call the existing discovery tools only for missing project/type/field facts below. An already confirmed project key can feed the supported type/metadata queries directly; do not invent a project-key argument for the project-name query. Resolve ambiguous actual choices and required business values with focused questions. A missing technical mapping/service capability is WAITING_TOOL, not a reason to switch routes or have the user invent IDs.
4. Preserve current Jira intent and pending question state while waiting: jiraExportRequested defaults to true, while an explicit user no-export request stays false. Once enough information arrives, resume discovery/assembly automatically; no extra continue or route confirmation. Missing setup is not a completed final JSON or score.

Only honor preparation-only/no-Jira when the user explicitly requested it independently. Such a request still requires real destination/field data for a final scoreable .json4jira; incomplete drafts must remain identified as drafts. Final scored-content/upload confirmation is a later separate gate, unchanged by this entry rule.

For both create and update, read real persistent user workspace ceaia-sdlc-jira/profiles.json first. Match authenticated user, confirmed Jira source, project and actual Story type ID, then reuse applicable components; do not discard the whole profile because its capability says create while this request is update. Existing-target reads can supply missing actual project/type facts. Never use a conversation file as cross-session storage or another user's profile. JWT/platform identity takes priority; staffId is a real fallback identity only. Do not parse/store tokens. Without real persistence, use supported current evidence for this run and report cache persistence unavailable.

Source is explicitly confirmed WPB/ALM/DATA/FCR/GO, not inferred from ticket prefix. Query calls use its lowercase almType. Resolve real staffId from trusted context or a confirmed same-user default, asking once if required by an actual discovery query. Query/profile identity is not a Story field: omit root staffId and fields.reporter for both create and update. Do not recover reporter from old templates, profiles or baseline copies. Session IDs remain platform-owned and do not enter the file. Legacy jira-user-info.md supplies routing defaults, not field schema.

| Tool | Exact known query inputs |
| --- | --- |
| java-base-mcp.queryJiraProjectsByName | staffId, almType, projectName |
| java-base-mcp.queryJiraIssueTypesByProject | staffId, almType, selected projectKey |
| java-base-mcp.queryJiraCreateMetaFields | staffId, almType, projectKey, and supported issueType or issueTypeId |

Reuse components independently:

| Component | Create/update reuse |
| --- | --- |
| Confirmed source/project, Story type name/ID, AC field ID, field identity/type and applicable option encodings | Reuse across modes for the same user/source/project/type. Combine profile facts with the actual baseline; no query merely because the mode changed. |
| Requiredness, writable operations, permissions and reusable defaults | Keep their evidenced operation scope. A create-only flag/default is not update permission or authorization to overwrite a baseline value. |
| Target Story content and attachments | Read the actual target baseline/inventory under the update procedure; profiles never replace these reads or select a target. |

When the profile plus existing verified evidence supplies the needed components, call none of the three discovery queries. For example, a saved create profile with the actual project/type/AC mapping can serve an update; absence of a separate update profile is not a reason to run discovery again. There is no TTL refresh. Query only a genuinely missing/inapplicable component, an explicit refresh or changed destination, or a precise actual schema error. Record which fact is missing and query only for it. One unambiguous project proceeds; multiple real choices need selection. Preserve actual distinct Story type IDs; no Story type blocks this route.

Do not rerun queryJiraProjectsByName/queryJiraIssueTypesByProject/queryJiraCreateMetaFields to manufacture an update capability record. queryJiraCreateMetaFields discovers create metadata; it cannot verify edit permission. Normal updates use the confirmed JSON update contract and actual target evidence, retaining operation-specific limits. Required getJiraInfos/readJiraAttachments baseline reads are separate from field discovery and still run when that target baseline has not been read.

Use only live exposed query arguments. Where metadata returns result.fields.values[], read those descriptors. Establish completeness from the contract or real closed pagination; do not invent paging controls. Cache all required descriptors, AC mapping and selected optional fields with actual IDs/name/schema/required/operations/default and used option ID/encoding. Do not infer requiredness from an omitted successful value. Create metadata is not independently verified edit permission.

## 2. Resolve values before JSON assembly

For an update the approved manifest first limits changes. Apply current explicit approved values, then actual baseline (omit unchanged fields), then applicable confirmed defaults/observed Jira defaults, then directly evidenced valid options. Ask only for unresolved required facts, using actual valid choices. Omit unknown optional fields; never invent IDs, business choices or placeholders. A profile/create default never authorizes overwriting existing relationships.

Resolve create Epic/no-Epic explicitly. Validate a newly supplied Epic once through supported getJiraInfos evidence; do not reread an applicable already-confirmed Epic. Map Epic/Parent or other custom fields into **only** the property/ID/representation the active JSON/backend schema actually supports. Do not invent wrapper fields from old direct tool arguments.

Show actual source/project/Story type, operation/update target, relationships and field values for a Use/Change settings decision during assembly. Already explicit current choices need no repeated re-entry. This confirms assembly values; final upload content is previewed and confirmed separately after scoring.

## 3. Write the complete canonical file

The developer's latest user-confirmed contract supersedes the earlier nested-map/Markdown samples: acceptanceCriteria is the root AC field-ID string, with no customFields wrapper; omit root staffId and fields.reporter. Use the [create template](../templates/story-json-template.json) or [update template](../templates/story-update-json-template.json). Updates add root issueIdOrKey after issueType; creates omit it entirely. Encode rich text using [Jira text rules](jira-text-format.md) before scoring. This is a required input contract for correct field recognition, not an additional Q1–Q7 business criterion. It is one JSON object with a fields object, never a stringified Jira request.

| JSON location | Required value / relationship |
| --- | --- |
| jiraInstance | Selected supported lowercase Jira source; do not add almType/jiraType aliases. |
| projectKey and fields.project.key | The same actual confirmed project key. fields.project is an object. |
| issueType and fields.issuetype.id | Confirmed Story type name at the root, its actual queried ID string inside fields.issuetype. Preserve the exact lowercase issuetype key; never substitute its display name for the ID or hardcode the screenshot ID. |
| issueIdOrKey (updates only) | Exact nonempty existing ticket key confirmed by the user and actual baseline, placed after issueType. The I in Id is uppercase. It must match this candidate's confirmed source/target; never use an evidence-only ticket, cached target or another batch item. Creates omit this key, rather than send null/empty. |
| fields.summary | Supported reviewed title as a string. No root summary. |
| fields.description | Supported non-AC business content as one Jira wiki string, e.g. h2. User Story, with real line breaks represented by JSON escaping. No unintended Markdown headings outside code/literal examples, array or JSON-encoded inner document. |
| acceptanceCriteria | Root-level actual AC field ID string from applicable metadata/profile, not AC text, an array or a field descriptor object. Do not wrap it in customFields. |
| fields[acceptanceCriteria] | The actual AC Jira wiki string, e.g. * *AC-001*: Given … When … Then …, with stable unique IDs and observable behavior. The mapped property must exist and be nonempty. |
| attachments | Complete authorized upload path list. A separate score-only specFilePath does not authorize upload. |

Resolve the AC field ID first, then use that exact string as the key in fields and store AC content there. For example, only when metadata really returns customfield_27708, acceptanceCriteria is that string and fields.customfield_27708 contains the AC Jira wiki. It is not a global default. Replace both identical <actual AC field ID> placeholders in the template with the complete observed ID; do not concatenate a guessed number. Missing mapping/value or incompatible type blocks production precheck; never fall back to an old top-level AC array or put the ID into the business prose.

The root acceptanceCriteria maps only the AC field ID; it is not business text. Additional approved business values go in fields under their real metadata-confirmed IDs/types/options. Do not introduce a customFields wrapper, guess root aliases or duplicate values. Include only supported writable fields for this operation; reporter is excluded from both modes. Preserve authorized optional fields/relationships in their confirmed schema locations. Updates preserve unchanged remote values through approved omission semantics rather than copying all baseline fields into the request.

Do not generate top-level staffId, summary, description, bddScenarios or customFields. The required root acceptanceCriteria is only the field-ID string, never the retired top-level AC text/array. Express supported BDD detail in the mapped AC Jira wiki instead of maintaining a separate BDD object. Preserve all non-AC sections in fields.description, including those originally following AC; AC has one authoritative mapped field. Complete rich-text encoding during assembly, before review/score; the SPEC uses a faithful Markdown view of the same content. There is no late Markdown-to-wiki conversion, field partition, dynamicFieldsJson/attachmentsJson/issuesJson wrapper or regenerated payload at export. Examples are not business facts or fixed environment IDs.

Save/read back <outputRoot>/story.json4jira. It is one real JSON object, not a quoted JSON document and not a file with .md content. Remove every template placeholder. Record the actual immutable path in paths.storyJsonPath and reuse it exactly across score/preview/export. Backend validation remains authoritative; use precise returned errors for bounded incremental correction.

## 4. Snapshot and update guard

Save a numbered jira-assembly-revision record containing actual query/profile evidence, compact schema descriptors, requiredness/option encoding, field provenance, confirmed settings, JSON path/revision and authorized attachments. Set exportContext.assembly.status=verified only after completion; the exporter receives this record/profileKey for eligible profile persistence after confirmed creation/required attachments.

An existing-ticket request never becomes a create. The user has confirmed root issueIdOrKey as the backend update selector: use that exact nonempty target key, verified against exportContext.target.ticketKey, baseline, confirmed source and project/type. Do not keep a general target-encoding WAITING_TOOL for this confirmed format. A missing/mismatched update key blocks the candidate; never remove it to retry as create. The same scored JSON path goes to the same score/export tools, without a separate issueIdOrKey tool argument or invented operation flag.

Preserve unchanged Summary/other baseline values and approved field omission semantics; the template's full business view does not authorize unrelated field changes. Do not treat create metadata as edit permission or omit a field required by the file contract. Apply [automatic Story/SPEC synchronization](existing-jira-update.md) before review/score: synchronize local SPECs/tests for a business change even when the user did not mention them. The update template includes a SPEC upload path; derive the actual remote plan from inventory: related SPECs default to replace, a first SPEC to add, explicit remote preservation to none with attachments=[]. None never disables local synchronization; specFilePath still points to the current local SPEC without an extra capability-proof gate. Replacement requires actual original IDs, supported encoding and confirmed upload-before-delete behavior. An unsupported replacement or actual backend rejection remains precise pending work; never silently skip SPEC, invent controls, override explicit none or fall back to create. Ready unrelated candidates may continue preparation.

Cache loading/discovery belongs here. Save/update reusable profile facts only after actual successful issue plus required attachments, through the exporter's persistence procedure. Failed/unknown operations never replace an older successful profile; a queue/linkage or persistence issue is reported separately and never causes replay.
