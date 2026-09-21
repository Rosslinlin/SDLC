# Work-item fields and explicit Epic-plus-Stories creation

Read for requested optional fields, generic work items, or explicit Epic-plus-Stories creation. UAT Test Case export does not use this document.

## Mode and scope

CEAIA candidates create Story issues with matching reviewed SPECs. Generic Task/Epic creation remains available for explicit non-CEAIA work-item requests. Do not silently convert an oversized CEAIA Story into a Task/Epic to evade decomposition or scoring.

When the user explicitly requests a new Epic together with reviewed Stories, preserve the original combined push capability. It is not the same as linking an existing Epic. Never create an Epic just because an optional link field is empty.

Distinguish:
- no Epic: omit epic/epicKey and unrequested parent fields;
- existing Epic: ask/confirm actual epicKey after source/project are known;
- new Epic plus Stories: user must authorize creating the Epic as well as Stories; collect evidenced Epic summary/description/epicName and preview the entire combined object.

## Optional field map

| Field | Original supported purpose | Safe collection |
|---|---|---|
| epicKey | Existing Epic link | Actual selected key; no guessed parent |
| epicName | New Epic board name | Evidenced/requested name when required |
| parentLink | Source-specific Parent Link | Explicit supported parent; do not equate automatically with epicKey |
| priority | Creation priority | Actual selected/evidenced value; never example default |
| assignee | Creation assignee | Actual supported identity, no guessed account |
| customFields | Supported creation fields | Real schema and user/evidence values |
| linkedIssueKeys | Standard Jira associations | Exact selected issue keys |
| linkType | Association type | User choice; original Relates default only for an explicitly requested association and when supported |

Do not send label/labels; original push tool applies CEAIA_GEN. Omit optional fields not applicable to the request. Update mode does not inherit these creation-only fields.

## Combined payload example

Values are illustrative; the actual schema, complete reviewed content and final approval govern the call. Unlike the old incomplete example, every CEAIA Story has its own SPEC attachment.

```json
{
  "source": "WPB",
  "projectKey": "EXAMPLE",
  "epic": {
    "summary": "Requested application review programme",
    "description": "Actual approved Epic scope",
    "epicName": "Application review",
    "attachments": []
  },
  "stories": [
    {
      "issueType": "Story",
      "summary": "Review application details",
      "description": "Complete reviewed STORY.md content",
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

Original push contract links Stories to an Epic created in the same request through the source-specific Epic Link field. Do not manufacture the not-yet-returned Epic key or combine this with conflicting existing-epic mappings. Validate supported live tool shape before writing.

Preview explicitly states the additional Epic, each Story, all fields, relationships and attachments. Approval binds the full serialized object. Record Epic success separately from Story/attachment success; a partial result never authorizes replaying the original combined create request.
