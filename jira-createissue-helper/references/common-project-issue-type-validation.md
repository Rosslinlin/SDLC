# Discover only missing project, type and field facts

Use after [profile lookup](common-state-and-user-info.md), only when selected input/upstream evidence and applicable cache cannot establish required facts. A complete trustworthy file does not need a new query chain. Configuration discovery does not replace a current update/attachment baseline read.

## Discovery calls

Production authentication comes from platform context. Use the actual exposed query schema; historically the tools name these arguments:

| Missing fact | Tool | Known query arguments |
| --- | --- | --- |
| Actual project | `java-base-mcp.queryJiraProjectsByName` | `almType`, `projectName`; `staffId` only if exposed/required and actually available from trusted context |
| Actual issue type binding | `java-base-mcp.queryJiraIssueTypesByProject` | `almType`, `projectKey`; same identity rule |
| Applicable field IDs, schemas, requiredness or option encoding | `java-base-mcp.queryJiraCreateMetaFields` | `almType`, `projectKey`, and supported `issueTypeId` or `issueType`; same identity rule |

Use confirmed `wpb`, `alm`, `fcr`, `go` or `data`; do not infer source from a ticket prefix. Never put query-only identity in issue JSON or exporter arguments. If a live query still requires unavailable staffId, report the exact schema/injection gap rather than fabricate it or collect authentication credentials.

A known exact project key can feed type/metadata discovery directly. Do not search projects merely to validate a key already established by current input, an actual target baseline or an applicable profile. Project-name search takes a keyword, not an invented projectKey parameter. Query type only when its binding is missing and metadata only when applicable field facts are missing; reuse every already known component.

## Resolve real choices and completeness

Read the complete actual response. An exact requested key/name matching one returned project can continue without another selection. Ambiguity needs choices showing all plausible returned keys/names. No result needs a corrected search/destination, not a guessed project.

Match a requested type to actual returned name/ID or verified cached binding. Same-name IDs remain distinct. Do not use a fixed type list, misclassify a Story as Test/Bug to place it in a non-Story manifest, or assume one type ID works in every project. A Test Case's dedicated flow controls its shape; discovery does not add Story AC/SPEC fields.

Use supported pagination where available and needed; never invent pagination parameters. A truncated list does not prove a missing item does not exist. Unknown completeness stays explicit. A complete upstream Test file can establish type through its verified root binding; do not add redundant `fields.issuetype.id` solely because a Story template contains it.

## Interpret metadata at its evidenced scope

Decode the actual outer response first. If `result` is a JSON string, decode that returned string once. Where `result.fields.values[]` exists, read its actual descriptors. Preserve wrapper/business errors; MCP transport success is not proof of successful metadata discovery.

Read fieldId/name, schema.type/items/system/custom, required, operations, defaults and allowed values where returned. Establish full-list completeness from the actual response contract or closed pagination such as total/isLast. Missing requiredness, operations or options remain unknown, not fabricated false/empty values.

`queryJiraCreateMetaFields` describes create metadata. Reuse stable IDs, schemas and applicable option encodings on update while preserving operation-specific limits. It is not an edit-permission check. Do not require a synthetic update capability record or treat create defaults as authority to overwrite target values.

Save each verified compact component under the profile rules immediately; failed/unknown discovery never overwrites usable facts. Record the missing fact and actual evidence in existing context/preview, without extra query-state files.

Use [native assembly](common-field-assembly.md) and [the file contract](common-json4jira-contract.md). Unresolved required technical mappings or unsupported encodings stop that preparation with precise diagnostics. Never borrow sample IDs, choose the first allowed option, ask users to invent hidden IDs or manufacture business content to satisfy a field.
