# Associate existing Jira issues

Use for an explicit relationship request. A relationship's source issue, target issues, type and direction are current-operation facts; they are not project defaults or ordinary Jira field values.

## Resolve the actual relationship

1. Reuse/confirm the selected Jira source, exact source ticket and target ticket(s). A key mentioned as business context does not select an association. Preserve the intended direction for asymmetric relationships.
2. Read `java-base-mcp.getJiraInfos` for unavailable source/target identity and current relationship facts. Reuse verified relevant reads when still applicable. Check actual target existence and identify an already-present identical relationship before proposing a duplicate.
3. Resolve the actual supported Jira link type and direction from available authoritative relationship information. Map user wording only when that match is established; do not invent a link type or infer direction from a convenient label.

Association alone does not need project search or issue-type discovery. `queryJiraCreateMetaFields` is create-field metadata, not a relationship-type validator or proof that a link operation is supported.

## Use the supported JSON encoding

Read [the JSON file contract](common-json4jira-contract.md). Reuse an already prepared association/update file only when its actual target and supported relationship encoding match the user's selected operation. Otherwise prepare it only from the production JSON operation contract or actual supported tool schema.

The earlier direct-argument association shape does not establish where link controls belong in a path-based file. Do not guess JSON property names, move link controls into `fields`, or pass extra arguments beside `story_json_workspace_path`. Do not change business text or create a new ticket merely to carry a link.

If the new association encoding is unavailable, preserve the resolved relationship plan in existing preview/context and report exactly what is missing: source, target(s), actual type/direction and the unsupported JSON representation. Keep unrelated prepared work. Do not claim the relationship was added or substitute a different operation. If the actual runtime exposes the supported encoding, use it; no separate capability gate is needed merely because the static example is absent.

For a submitted Story issue file, retain any upstream [assessment evidence](story-assessment-evidence.md) accurately; changing the relationship input invalidates an old current-assessment claim. Scoring requirements are project/backend-owned, not a helper prerequisite, and no scoring is invoked automatically. A Story issue file always uses an individual path call, never a manifest, even for a relationship-only change or scoring-exempt project. A standalone documented relationship operation outside issue-file export follows its actual operation contract; never reclassify a Story or fabricate content to obtain another route.

## Preview and result

Follow [preview, confirmation and submission](common-preview-confirm-export.md). Show the exact source, targets, supported link type/direction, existing relationship evidence and actual saved file. Reuse current confirmation only for this same operation/package.

Submit only that file's `story_json_workspace_path` through `java-base-mcp.exportjirabydynamicfield`. Preserve the full response and actual per-relationship result. A content-update success or a generic accepted response does not independently prove the link exists. Unknown outcomes require supported read-only reconciliation; do not automatically replay, rescore or repeat a successful content update to repair a link.
