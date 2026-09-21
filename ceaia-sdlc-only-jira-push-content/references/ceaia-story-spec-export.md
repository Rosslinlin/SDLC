# CEAIA Story/SPEC creation

For CEAIA creation read ceaia-jira-write-gate.md. Generic callers retain common-jira-write-gate.md and may reuse shared JSON mechanics here, but not the CEAIA readiness, mandatory SPEC or C3/C5 workflow gates.

## E1 — Ready inputs

For every selected candidate require current complete Story/score/Test Case/SPEC/review state and PASS gates. Check global source coverage and identity; actual current Story is the description, not the full SPEC. Titles/summaries come from reviewed Story.

CEAIA issueType is Story. A broad requirement is not permission to export Epic or Task instead. New Epic creation is a separately explicit generic request. Existing Epic linking is optional, with known destination and explicit actual key.

Every Story gets exactly its own reviewed named SPEC. Do not export Test Case independently or reference internal score/planning/review. Attachment filename is the reviewed actual basename (lowercase kebab-case by default, or a recorded safe exact user-requested name), path workspace-relative.

## E2 — Payloads

Minimal single-Story example (replace every example value from validated current artifacts):
```json
{
  "source": "WPB",
  "projectKey": "EXAMPLE",
  "issueType": "Story",
  "summary": "Review application details",
  "description": "Actual complete reviewed STORY.md content",
  "descriptionFormat": "markdown",
  "attachments": [
    {
      "fileName": "review-application-spec.md",
      "relativePath": "outputs/ceaia/review-application/review-application-spec.md"
    }
  ]
}
```

Multiple Stories sharing source/project:
```json
{
  "source": "WPB",
  "projectKey": "EXAMPLE",
  "stories": [
    {
      "issueType": "Story",
      "summary": "Review application details",
      "description": "Actual complete reviewed STORY.md content",
      "descriptionFormat": "markdown",
      "attachments": [
        {
          "fileName": "review-application-spec.md",
          "relativePath": "outputs/ceaia/review-application/review-application-spec.md"
        }
      ]
    }
  ]
}
```

Group differing source/project destinations into separate supported payloads; approval binds every group in execution order. Do not apply a shared inferred source. Optional epicKey applies only to explicitly selected Story/Epic mapping. Other optional links/priority/assignee/custom fields must be supported by actual schema, evidenced and displayed; do not copy example defaults. Do not send label/labels: the existing pushJiraContent contract adds CEAIA_GEN.

Generic Epic/Task creation and explicitly requested combined Epic-plus-Stories are preserved in [work-item-field-mapping.md](work-item-field-mapping.md). Do not infer that request; use the combined branch only with explicit scope, actual supported schema and a SPEC on every CEAIA Story.

## E3 — Preview and approval

Show mode, selected candidate count, source/project, issueType, exact summary/description per Story, Epic decision/mapping, each SPEC filename/path and readable content or change summary, no unresolved blockers, and complete request object(s). State that standalone Test Case is not uploaded. Do not expose its path or internal audit fields in the user export manifest.

Serialize full real payload(s) and follow C3 approval binding with final attachment content and recorded file revisions. Validate every Story's description and SPEC against current review, not just file names. Use actual request_user_approval schema; action target is source/project and tool is java-base-mcp.pushJiraContent.

## E4 — Execute and recover

Send only {"requestJson": "<actual approved serialized object>"} to pushJiraContent. Verify arguments and file bytes immediately before each group. Capture each returned issue and attachment result.

If only some items succeeded, do not re-export the original batch. Map confirmed items using service-provided identifiers/order per its contract, not guessed similar summaries. Unknown correspondence is a recovery blocker. Follow C5 reconciliation and obtain approval for any safe supported remaining operation.

Current-ready artifacts do not prove Jira success. Return individual keys/links, attachment outcomes, remaining targets and exact recovery action.
