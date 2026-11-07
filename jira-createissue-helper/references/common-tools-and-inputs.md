# Production tools and input ownership

Read when preparing a call or diagnosing a schema gap. [The JSON contract](common-json4jira-contract.md) owns content export, [assessment evidence](story-assessment-evidence.md) explains optional upstream scoring, and the platform owns authenticated request headers.

| Tool | Use and input source |
| --- | --- |
| `java-base-mcp.exportjirabydynamicfield` | Content create/update/supported manifest: exactly `story_json_workspace_path`, a real saved Workspace-relative issue/manifest path. |
| `java-base-mcp.queryJiraProjectsByName` | Missing project: actual source and user project-name search keyword; reuse trusted/injected identity required by its live schema. |
| `java-base-mcp.queryJiraIssueTypesByProject` | Missing actual type ID/name: confirmed source/project key and actual identity context required by its schema. |
| `java-base-mcp.queryJiraCreateMetaFields` | Missing field identities/types/options: confirmed source/project and supported actual type ID/name. It describes create metadata, not proof of update permission or available relationship types. |
| `java-base-mcp.getJiraInfos` | Actual selected target/source and relevant fields under its exposed schema; read current update/relationship/attachment facts when needed. |

The established discovery schemas may expose `staffId`, `almType`, `projectName`, `projectKey`, and `issueTypeId` or `issueType` as applicable. Follow the actual production schema, including platform-supplied JWT identity; do not invent parameter names, decode tokens, or turn an obsolete manual-staff-ID prompt into a prerequisite. A real exposed required identity unavailable through trusted context is an integration gap to report, not a placeholder. Query-only identity never enters content JSON, score arguments or path-only export arguments.

Sources are confirmed WPB/ALM/DATA/FCR/GO, represented lowercase in JSON `jiraInstance` and discovery `almType` where required. A ticket prefix or unverified URL hostname does not establish a source; an authoritative configured host/source mapping can supply actual evidence. Follow [the configuration guidance](common-state-and-user-info.md): read durable profiles first, ask only unresolved source/project/type, offer supported optional fields and obtain/reuse the per-request saved-settings check before creating. Final saved-file confirmation remains separate.

Ordinary JSON preparation needs actual source/project/type, selected business content and current requested operation. Updates additionally need the confirmed exact target and sufficient current baseline for the requested delta. Attachment deletion/replacement needs real original IDs and supported operation encoding; an update target alone is insufficient. Association needs actual endpoints, direction/type and supported encoding. These facts do not become extra tool arguments.

Platform file capabilities must read full actual files, prepare valid native JSON, persist profiles in real user-level Workspace and verify that same destination through read-back, and retain full score/write responses. A conversation/history/output writer alone cannot fulfill cross-session persistence. Preview/confirmation must bind the actual saved files. Do not invent tool IDs, scripts, a native-preview endpoint or recovery/status API when the platform does not expose one. Missing capability blocks only the affected operation; preserve already prepared artifacts and known outcomes.

This helper does not invoke scorer/reviewer as an export prerequisite. Preserve supplied assessment evidence when relevant; actual project/backend requirements govern acceptance. `user-story-reviewer` owns requested assessment and existing-Jira ticket/source score synchronization. This helper uses neither that mode nor a legacy binding after JSON content export.
