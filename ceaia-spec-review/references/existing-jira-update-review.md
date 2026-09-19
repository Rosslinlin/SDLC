# Gate 6: Existing Jira update integrity

Apply this gate only when user asks to update one or more existing Jira tickets.

For each ticket, verify:

- update manifest identifies exactly one source, ticket key, original Jira description retrieval result, and incremental requirement set;
- source was explicitly supplied or confirmed by user for that ticket and was not inferred from its key, project prefix, another ticket, or prior tool behavior;
- separate Acceptance Criteria retrieval result is recorded; `empty-or-unavailable` is valid when description is non-empty and was reviewed for embedded acceptance statements;
- `java-base-mcp.readJiraAttachments` completed for ticket before generation, and manifest contains its complete attachment inventory or an explicit empty attachment list;
- every user-provided incremental requirement is traceable to proposed Story, ACs, FRs, executable test cases, and SPEC where applicable;
- proposed Jira description and SPEC's embedded `## Jira Story *(mandatory)*` content match exactly;
- baseline behavior is preserved unless user explicitly asked to modify or remove it;
- changes do not silently broaden scope, invent behavior, or convert unrelated readable attachments into delivery scope;
- every readable imported attachment is used as source evidence or explicitly recorded as applicable or not applicable with evidence-based reason;
- skipped attachments and their reasons are visible in internal update manifest; no requirement is claimed from unreadable content;
- selected attachment replacement mapping has actual imported attachment ID, workspace-relative edited file path, and reviewed CEAIA SPEC file;
- if more than one attachment could be a SPEC target, manifest records user's explicit selection and one-to-one replacement mapping;
- user selected attachment filenames from attachment-reader candidate list; attachment IDs were retained internally from tool result and were not collected as free-text user input;
- no attachment is deleted and no selected attachment is replaced by different file unless user explicitly approved that mapping;
- summary is proposed only when user explicitly supplied it or reliable source establishes expected summary; priority, assignee, labels, custom fields, and other unsupported update fields are absent.

If target attachment is non-CEAIA but readable, verify proposed replacement is valid CEAIA SPEC and preserves all relevant evidence from original attachment. If attachment reading was unavailable, failed, incomplete, unreadable in potentially material way, or intended target is ambiguous, return `NEEDS_HUMAN_CLARIFICATION`. Do not return PASS for existing-ticket update without completed attachment-reader result. Do not return `NEEDS_HUMAN_CLARIFICATION` solely because separate Acceptance Criteria field is empty when description is non-empty; assess actual evidence gap through normal review gates.

For multi-ticket request, review and report every ticket independently. A clean ticket must not conceal unresolved ambiguity or review failure on another ticket.
