# Direct call examples

Structural examples only. Every ID, key, identity, path and field mapping below is an example, not evidence or an allowed default. Use the actual current values from the reviewed handoff and applicable profile. The examples assume settings confirmation and all content/attachment gates already passed. A one-item array below demonstrates nesting; real multiple creates include every selected item separately.

## A: one Story create and one SPEC

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "conversationId": "example-current-conversation",
  "projectKey": "DEMO",
  "summary": "View registration status",
  "issueType": "Story",
  "description": "h1. View registration status\n\nh2. User Story\nAs a member, I want to view my registration status.\n\nh2. Attachments\nregistration-status-spec.md",
  "dynamicFieldsJson": "{\"fields\":{\"customfield_12345\":\"AC-001: Given a saved status, when I open registration, then I see that status.\"}}",
  "attachmentsJson": "{\"add\":[{\"relativePath\":\"outputs/ceaia/registration-status/registration-status-spec.md\",\"fileName\":\"registration-status-spec.md\"}]}"
}
```

## B: database binding after A returns DEMO-101 and confirms attachments

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "issueIdOrKey": "DEMO-101",
  "conversationId": "example-current-conversation",
  "markdownReviewRelativePath": "outputs/ceaia/registration-status/STORY.md"
}
```

## Update: only approved changed fields, no attachment operation

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "issueIdOrKey": "DEMO-99",
  "description": "h1. View registration status\n\nh2. User Story\nAs a member, I want to view my registration status.\n\nh2. Attachments\nregistration-status-spec.md",
  "dynamicFieldsJson": "{\"fields\":{\"customfield_12345\":\"AC-001: Given a saved status, when I open registration, then I see that status.\"}}"
}
```

## Batch nesting: each dynamic/attachment value is still a JSON string

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "conversationId": "example-current-conversation",
  "issuesJson": "[{\"projectKey\":\"DEMO\",\"summary\":\"View registration status\",\"issueType\":\"Story\",\"description\":\"h1. View registration status\\n\\nh2. User Story\\nAs a member, I want to view my registration status.\\n\\nh2. Attachments\\nregistration-status-spec.md\",\"dynamicFieldsJson\":\"{\\\"fields\\\":{\\\"customfield_12345\\\":\\\"AC-001: Given a saved status, when I open registration, then I see that status.\\\"}}\",\"attachmentsJson\":\"{\\\"add\\\":[{\\\"relativePath\\\":\\\"outputs/ceaia/registration-status/registration-status-spec.md\\\",\\\"fileName\\\":\\\"registration-status-spec.md\\\"}]}\"}]"
}
```

A has no markdownReviewRelativePath. B has exactly five non-empty strings, contains no Jira content/attachments, and uses the scored Story input path. A batch result still requires one B call per eligible returned key and its own scored Story path. Omission of epicLink in a create is valid here only because the user explicitly chose no Epic.
