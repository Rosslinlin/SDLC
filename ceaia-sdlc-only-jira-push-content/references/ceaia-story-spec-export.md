# CEAIA Story/SPEC Export Mode

## Contents

- Required fields and Epic linking
- Story-named SPEC attachment rules
- Work item payload construction
- Export manifest
- Approval payload
- Pre-write validation

Use `ceaia_story_spec_export` when the available source artifacts are reviewed CEAIA `STORY.md` and story-named SPEC files, or when the user asks to create Jira Stories with SPEC attachments.

For CEAIA workflow, Jira export includes only:

- Jira Story created from `STORY.md`.
- Matching story-named SPEC file attached to that Jira Story.

Do not export, attach, upload, reference, or include standalone `TEST_CASE.md` as a separate Jira artifact. Test Case content may be included inside the story-named SPEC attachment.

Read CEAIA creation artifacts from the user-visible deliverable folder:

```text
outputs/ceaia/<story-slug>/STORY.md
outputs/ceaia/<story-slug>/TEST_CASE.md
outputs/ceaia/<story-slug>/<story-named-spec>.md
```

Only `STORY.md` and the matching story-named SPEC are Jira-facing. Do not use `.ceaia-work` planning, scoring, or review artifacts as Jira payload content or attachments.

## CEAIA Required Fields

Common root fields:

- `source`
- `projectKey`

Required fields for every Story:

- `issueType`
- `summary`
- `description`

Use these safe defaults only when defensible:

- `issueType: "Story"` for user-facing requirements.
- `issueType: "Task"` for implementation work.
- `issueType: "Epic"` for large grouped scope.
- `linkType: "Relates"` unless the user gives another Jira link type.

When a Story description is copied from Markdown content such as `STORY.md`, set `descriptionFormat: "markdown"`.

Do not paste full SPEC content into the Jira description. Keep the story-named SPEC as an attachment only.

## Epic Link Information

Only after Jira `source` and `projectKey` are known, ask whether generated Stories need to link to an Epic when that information is not already known.

If the user says no:

- Record `epic_link_required: false` in the manifest details.
- Omit `epicKey` and parent Epic fields from the Jira payload unless another explicit parent field is already valid.

If the user says yes:

- Ask for the Epic ticket key.
- Validate that the Epic key is non-empty and plausibly Jira-shaped, such as `PROJECT-123`.
- Include the Epic key in every applicable Story payload as `epicKey` unless the Jira source requires a different supported parent field.
- Include the Epic ticket in the Jira Export Manifest and `request_user_approval` details.

Do not invent an Epic ticket.

If Epic linking is requested but the Epic ticket is missing, do not request final Jira approval.

## Story-Named SPEC Attachment Rule

When creating any Jira Story, include the generated story-named SPEC file as an attachment on that Story.

Use the attachment object:

```json
{
  "fileName": "<actual-story-named-spec-file-name>.md",
  "relativePath": "<workspace-relative path to this story's story-named SPEC file>"
}
```

Rules:

- `fileName` must match the actual SPEC file name.
- `fileName` must not default to `SPEC.md` unless the user explicitly requested that exact name.
- `relativePath` is mandatory.
- `relativePath` must point to the matching Story's own story-named SPEC workspace file.
- Do not attach one Story's SPEC to another Story.
- Do not attach old review attempts as SPEC attachments.
- Use workspace-relative paths only.

Do not pass artifact IDs, Story paths as attachment content, review report paths, conversation IDs as file references, HTTP URLs, `blob:` URLs, `data:` URLs, `file:` URLs, temporary URLs, absolute filesystem paths, or paths containing `..` as attachment references.

## Work Item Payload Construction

Construct the Jira payload object before serialization using one of these supported shapes.

### Single issue

```json
{
  "source": "WPB",
  "projectKey": "AIWPB",
  "issueType": "Story",
  "summary": "Short summary",
  "description": "Full Markdown description from STORY.md",
  "descriptionFormat": "markdown",
  "epicKey": "AIWPB-100",
  "parentLink": "AIWPB-100",
  "priority": "Medium",
  "assignee": "staff",
  "linkedIssueKeys": ["AIWPB-1"],
  "linkType": "Relates",
  "attachments": [
    {
      "fileName": "review-term-deposit-spec.md",
      "relativePath": "outputs/ceaia/review-term-deposit/review-term-deposit-spec.md"
    }
  ]
}
```

### Multiple Stories

```json
{
  "source": "WPB",
  "projectKey": "AIWPB",
  "stories": [
    {
      "issueType": "Story",
      "summary": "Review term deposit details before confirmation",
      "description": "Full Markdown description from STORY.md",
      "descriptionFormat": "markdown",
      "epicKey": "AIWPB-100",
      "attachments": [
        {
          "fileName": "review-term-deposit-spec.md",
          "relativePath": "outputs/ceaia/review-term-deposit/review-term-deposit-spec.md"
        }
      ]
    }
  ]
}
```

### Epic plus Stories

```json
{
  "source": "WPB",
  "projectKey": "AIWPB",
  "epic": {
    "summary": "Epic summary",
    "description": "Epic description",
    "epicName": "Board epic name",
    "attachments": []
  },
  "stories": [
    {
      "summary": "Story summary",
      "description": "Markdown story description from STORY.md",
      "descriptionFormat": "markdown",
      "attachments": []
    }
  ]
}
```

Association rules:

- If an Epic is created in the same request, every Story in `story` or `stories` is linked to that new Epic by the correct Epic Link field for the Jira source.
- If no Epic is created but `epicKey` is supplied, Story issues are linked to that existing Epic.
- `parentLink` is applied through the source-specific Parent Link custom field.
- `linkedIssueKeys` creates standard Jira issue links using `linkType`, default `Relates`.

Label rules for work item export:

- Do not provide `label` or `labels` in the request.
- `java-base-mcp.pushJiraContent` sets Jira labels to `["CEAIA_GEN"]` for every created work item.

## CEAIA Jira Export Manifest

Before final approval, prepare and show:

```markdown
## Jira Export Manifest

- Export mode: ceaia_story_spec_export
- Export trigger: Automatic post-review handoff or explicit user request
- Story count:
- Spec count:
- Jira source:
- Project key:
- Issue type:
- Description format:
- Epic link required: [Yes/No]
- Epic ticket or per-story Epic mapping: [None / <EPIC-KEY> / mapping]
- Story to story-named SPEC workspace mapping:
- SPEC attachment names:
- Standalone Test Case export:
  - The standalone `TEST_CASE.md` will not be exported to Jira.
  - The standalone `TEST_CASE.md` will not be attached to Jira.
  - The standalone `TEST_CASE.md` will not be referenced in Jira.
  - Test Case content may be included in the attached story-named SPEC.
- Known unresolved items:
```

Do not include standalone `TEST_CASE.md` workspace paths in the export manifest.

For `ceaia_story_spec_export` and `generic_work_item_export`, use this approval payload shape:

```json
{
  "title": "Approve Jira export?",
  "reason": "Explicit authorization is required before creating Jira issue(s) and uploading attachment(s).",
  "action_type": "jira_export",
  "target": "<source>/<projectKey>",
  "summary": "Create <issue_count> Jira issue(s) in <projectKey> and upload approved attachment(s) where applicable.",
  "risk_level": "medium",
  "details": {
    "export_mode": "ceaia_story_spec_export_or_generic_work_item_export",
    "issue_count": "<number>",
    "jira_source": "<source>",
    "project_key": "<projectKey>",
    "issue_type": "<issueType>",
    "description_format": "markdown_or_wiki_or_none",
    "epic_link_required": "<true|false>",
    "epic_key_or_mapping": "<none|EPIC-123|per-story mapping>",
    "attachment_mapping": [
      {
        "issue": "<issue title or id>",
        "attachment_file_name": "<actual-file-name>",
        "relative_path": "<workspace-relative path>"
      }
    ],
    "known_unresolved_items": "none"
  },
  "approve_label": "Approve Jira export",
  "reject_label": "Reject",
  "require_reason": true,
  "action_binding": {
    "tool_name": "java-base-mcp.pushJiraContent",
    "target": "<source>/<projectKey>",
    "arguments_preview": {
      "requestJson": "{\"source\":\"<source>\",\"projectKey\":\"<projectKey>\",\"stories\":[{\"issueType\":\"Story\",\"summary\":\"<summary>\",\"description\":\"<description>\",\"descriptionFormat\":..."
    }
  }
}
```

For `ceaia_story_spec_export` and `generic_work_item_export`, additionally validate:

- Every issue has `issueType`, `summary`, and `description` where required.
- Every Markdown description has `descriptionFormat: "markdown"`.
- Epic link requirements match the user's answer and approved manifest.
- Every attachment `fileName` matches the actual file name.
- Every attachment `relativePath` points to the intended workspace file.
- No standalone CEAIA `TEST_CASE.md` appears as an attachment or separate Jira export item.
- No `label` or `labels` field is included in work item payloads.
