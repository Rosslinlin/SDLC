# Evidence and source enrichment

Use all relevant conversation and workspace materials: requirements, UX/UI flows, screenshots, wireframes, FSDs, APIs, data mappings, existing artifacts, Jira keys, Confluence URLs, keywords, and prior clarifications.

Never invent behavior. Separate source facts from background context.

## Jira

For an explicit existing-ticket update request, apply Existing Jira update intake before this generic Jira enrichment rule. Do not treat an update request as a description-only read just because the user did not explicitly mention attachments.

When the user supplies a Jira key for generation, review, or non-update enrichment, use `java-base-mcp.getJiraInfos` before decomposition.

- Require the user to explicitly supply or confirm `source` as `WPB`, `ALM`, `DATA`, `FCR`, or `GO` before the first Jira call.
- Never infer or default Jira source from the ticket prefix, project key, prior tool behavior, organization context, or another ticket.
- For multiple tickets with missing sources, use one `ask_user_question` popup with one independent constrained source selector per ticket. Preserve a per-ticket mapping because tickets may belong to different Jira sources; never apply a shared default.
- A Jira URL is not a substitute for the required `WPB`, `ALM`, `DATA`, `FCR`, or `GO` selection.
- Do not call `java-base-mcp.getJiraInfos` for a ticket until its source is confirmed.
- If a read returns not-found or another source-related error, preserve the exact tool error and ask the user to reconfirm or correct that ticket's key and source. Do not probe other sources automatically.
- Retrieve `description` and `AcceptanceCriteria` by default.
- Retrieve `comments` only when needed for missing or recent clarification.
- Retrieve `changelog`, `priority`, or `assignee` only when they materially affect scope, sequencing, dependency, or Jira readiness.
- Read each required field in a separate call.

The source gate is strict: when a source is missing, the next and only action is the source-selection `ask_user_question` popup. Do not combine it with requirement clarification or other intake questions, and do not continue until every target ticket has a confirmed source.

## Confluence

For a supplied supported URL, use `java-base-mcp.fetchConfluenceContent`. Supported hosts are:

- `https://wpb-confluence.systems.uk.hsbc`
- `https://digital-confluence.systems.uk.hsbc`
- `https://alm-confluence.systems.uk.hsbc/confluence`
- `https://fcr-wiki.systems.uk.hsbc`
- `https://gbmt-confluence.prd.fx.gbm.cloud.uk.hsbc`
- `https://data-confluence.systems.uk.hsbc:5050`

For useful keywords without a full source, use targeted `java-base-mcp.fuzzyMatchingConfluence` searches. Use `queryType=true` to discover pages and `queryType=false` for a small amount of detailed background with number no higher than 5. Supply `spaceKey` when the user provides it; otherwise supply a relevant Confluence URL so the tool can infer the space. Infer source from context; map DIGITAL to WPB, GBMT to MSS, and use ALM only when no source can be inferred.

Do not turn fuzzy-search background into deliverable scope unless direct evidence supports it. Ask for pasted content or a valid URL if access fails.
