# Generic Work Item Export Mode

Use `generic_work_item_export` only when the user explicitly asks to create Jira work items outside testcase export and outside CEAIA Story/SPEC export.

Supported issue types include:

- Story
- Task
- Epic

Common supported fields include:

- `issueType`
- `summary`
- `description`
- `descriptionFormat`
- `epicName`
- `epicKey`
- `parentLink`
- `priority`
- `assignee`
- `customFields`
- `linkedIssueKeys`
- `linkType`
- `attachments`

When creating an Epic, provide `epicName` when required or recommended by the Jira project.

When `description` is Markdown and should be converted to Jira wiki markup before creation, set `descriptionFormat: "markdown"` or `descriptionFormat: "md"`.

For Jira wiki markup descriptions, omit `descriptionFormat`.

Follow the same label, attachment, path safety, preview, approval, and result rules as `ceaia_story_spec_export`.
