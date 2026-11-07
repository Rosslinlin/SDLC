# Evidence intake

Use supplied requirements, UX/UI, FSD/API/data materials, ticket fields, attachments, Confluence, workspace evidence and explicit clarifications. Separate direct facts from background. Record locations internally, never in Jira-facing documents.

## Jira

Classify each supplied ticket's actual role before choosing what to read. A key/link alone does not make it a requirements source:

| Role in the current request | Required read / use |
| --- | --- |
| Requirements source | Read the relevant business evidence before decomposition, including Description and separate Acceptance Criteria when supported. |
| Explicit Story update target | Use [existing-ticket intake](existing-jira-update.md) for its baseline and relevant attachment inventory. |
| Epic/Parent or another relationship target only | Verify the requested target's identity/existence and applicable type through the lightweight check below. Do not require Description, AC, SPEC, comments or attachments. |

One ticket can have more than one explicitly requested role. Reuse the actual lookup evidence across those roles, but distinguish a missing requirement from an invalid relationship. Merely hanging a Story under an Epic does not authorize importing that Epic's scope or expanding its linked documents/attachments into intake.

Before first Jira read require explicit per-ticket source: WPB, ALM, DATA, FCR or GO. If missing, use ask_user_question with one independent constrained selector per unresolved ticket; wait. Do not infer from prefix/project/another ticket, probe alternate sources or ask for a URL in place of source confirmation.

For requirements-source tickets, use java-base-mcp.getJiraInfos before decomposition. Read Description and AcceptanceCriteria separately. Read comments only for missing/recent clarification; other supported fields only if materially relevant. Use actual tool schema; do not assume summary or arbitrary fields are exposed. Reuse complete applicable reads rather than retrieving the same content for planning, assembly and review independently.

A separate AC field that is empty/null/unavailable/not-found/field-error does not block a successfully retrieved description. Record empty-or-unavailable and inspect the complete description for embedded acceptance statements. A genuine ticket/source error is different: show the exact relevant error, key and confirmed source in a correction/retry/stop popup. Never expose credentials or unrelated sensitive transport data.

An unusable required Description or relevant unreadable evidence blocks that candidate's requirements intake, not unrelated generation. This business-evidence rule does not apply to relationship-only targets. Follow current-state recovery; successful intake is reused, not repeated because another Skill was invoked.

### Relationship-only Epic/Parent validation

Use one supported getJiraInfos lookup for a newly supplied, not-yet-verified target. Request only identity/existence and issue type when the actual tool schema permits selecting them; otherwise use its supported lookup without inventing arguments or compensating with extra content reads. A nonempty Description is never a validation criterion. Do not fetch AC, comments, attachments or related Confluence content solely to validate the link.

Interpret the actual business result, not body length or the transport envelope alone:

| Observed evidence | Decision |
| --- | --- |
| Explicit business success for the single requested key/source, by returned identity or the lookup's target-binding success semantics | Target exists/is readable; it need not echo the key when the successful lookup itself is bound to that exact submitted target. Empty, null or absent Description/AC is valid for this role; an error retrieving an unneeded body field does not erase independently established identity. If issue type is returned, check it is suitable for the requested Epic/Parent relation; an unreported type is not a reason to invent it, demand body content or repeat the lookup. |
| Explicit ticket-not-found, permission/source error, mismatched target, or an actual returned type incompatible with the requested relation | Report that precise condition. An existing non-Epic ticket is a type mismatch, not a broken URL; permission failure is not proof the ticket does not exist. Do not probe other sources or silently choose another target. |
| Only transport success, a generic empty/truncated response, or ambiguous target binding with no documented lookup guarantee | Verification is unknown, not invalid. Preserve the exact evidence and request only the missing supported verification/clarification; do not fabricate success or retry the same content query in a loop. |

Keep the confirmed source/key and actual lookup/type evidence in existing context or the assembly record, without another validation file. Reuse it across candidates and stages while the target/source and applicable evidence remain unchanged; query again only for an explicit refresh, a changed target/source, or an actual target-related error requiring recovery. A later supported backend relationship rejection is reported precisely rather than reclassified as missing Description.

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
