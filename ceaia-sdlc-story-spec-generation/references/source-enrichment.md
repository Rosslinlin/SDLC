# Evidence intake

Use supplied requirements, UX/UI, FSD/API/data materials, ticket fields, attachments, Confluence, workspace evidence and explicit clarifications. Separate direct facts from background. Record locations internally, never in Jira-facing documents.

## Jira

Explicit update requests use existing-jira-update.md before generic enrichment.

Before first Jira read require explicit per-ticket source: WPB, ALM, DATA, FCR or GO. If missing, use ask_user_question with one independent constrained selector per unresolved ticket; wait. Do not infer from prefix/project/another ticket, probe alternate sources or ask for a URL in place of source confirmation.

Use java-base-mcp.getJiraInfos for supplied tickets before decomposition. Read description and AcceptanceCriteria separately. Read comments only for missing/recent clarification; other supported fields only if materially relevant. Use actual tool schema; do not assume summary or arbitrary fields are exposed.

A separate AC field that is empty/null/unavailable/not-found/field-error does not block a successfully retrieved description. Record empty-or-unavailable and inspect the complete description for embedded acceptance statements. A genuine ticket/source error is different: show the exact relevant error, key and confirmed source in a correction/retry/stop popup. Never expose credentials or unrelated sensitive transport data.

Missing description or relevant unreadable evidence blocks that candidate, not unrelated candidate generation. Follow current-state recovery; successful intake is reused, not repeated because another Skill was invoked.

## Confluence

For supplied supported URLs use java-base-mcp.fetchConfluenceContent:
- https://wpb-confluence.systems.uk.hsbc
- https://digital-confluence.systems.uk.hsbc
- https://alm-confluence.systems.uk.hsbc/confluence
- https://fcr-wiki.systems.uk.hsbc
- https://gbmt-confluence.prd.fx.gbm.cloud.uk.hsbc
- https://data-confluence.systems.uk.hsbc:5050

For useful keywords use targeted java-base-mcp.fuzzyMatchingConfluence, queryType=true to discover and queryType=false for a small detail result (number <=5). Use a supplied spaceKey or relevant Confluence URL. For this Confluence search only, infer source from supported context (DIGITAL→WPB, GBMT→MSS; ALM fallback when the service contract permits). This is not permission to infer Jira source.

Fuzzy background does not become delivery scope without direct evidence. If access fails, ask for readable content or a valid supported URL.
