# Jira Create Issue Helper

Version 3.0.0-json-preview.2 prepares and exports ordinary Jira issues and source Test Cases through saved Workspace JSON. CEAIA SDLC uses its dedicated exporter; this package has no legacy SDLC gateway.

Read [SKILL.md](SKILL.md) first. It routes to the selected business flow and loads preparation references only when needed.

- Every route reads durable user-Workspace `ceaia-sdlc-jira/profiles.json`; complete upstream `.json4jira` files remain unchanged and skip unnecessary discovery. Keep multiple project/type/operation records, canonical issueType and Story's compatible storyType alias.
- Missing setup resolves source, project/type, then optional supported fields. Existing profiles get one effective-settings check before creating; final file/content approval remains separate.
- No global scoring prerequisite or automatic assessment is imposed. Preserve actual upstream scores when supplied; the backend decides project-specific requirements. No mandatory SPEC.
- Preview actual files, obtain/reuse current explicit content confirmation and call production `java-base-mcp.exportjirabydynamicfield` with only `story_json_workspace_path`.
- Multiple Stories submit individually in approved order. Ordinary non-Story batches use a path manifest; mixed plans preserve selection order.
- Test Case multiple uses one native issue file per case plus one path manifest; single uses one five-column aggregate file with no Test Details. Both explicitly include `CEAIA_GEN` in native labels.
- Preserve supplied content, supported extra fields and exact update targets. Render non-Test Markdown during preparation, before Story scoring.
- Preserve full actual responses and separate issue, steps, attachment/relationship, Story association and queue outcomes. No post-export score-binding call or blind retry.
- Each confirmed successful create/update completes populated profile merge/write/read-back in real persistent user Workspace, including successful batch children and upstream files. Separate downstream failure never suppresses this save. Report persistence status independently; a failed save resumes only that save.

The exact shared file/call format is in [the JSON contract](references/common-json4jira-contract.md). See [optional assessment evidence](references/story-assessment-evidence.md), [Test Case source export](references/flow-testcase-source-export.md), [profiles](references/common-state-and-user-info.md) and [submission/recovery](references/common-preview-confirm-export.md) for their conditional details.

Production must expose the exporter, three query tools, Jira reads and the platform file/user-Workspace/preview/input capabilities listed in SKILL.md. Request authentication/session headers remain platform-owned. Test-environment tool aliases do not replace the confirmed production tool name.

Only supported operation encodings may be submitted. Non-Story path batching is confirmed; Stories never enter manifests. Standalone association and attachment delete/replace still require actual production encoding where not already established. Their missing schema is reported precisely; old direct parameters are never used as a fallback. Runtime is text/file-tool based for GPT-5.6 Luna or GPT-6 Luna; no scripts or sibling filesystem access are required.
