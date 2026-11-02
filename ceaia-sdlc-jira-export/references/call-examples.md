# Structural call examples

All IDs, keys, paths and fields below are examples, never defaults. Use actual reviewed values after confirmation/preflight. The examples do not replace the live tool contract.

## A: create Story and SPEC

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

Epic omission assumes the user explicitly chose no Epic. A never carries markdownReviewRelativePath.

## B: only after A returns DEMO-101 and confirms required attachments

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "issueIdOrKey": "DEMO-101",
  "conversationId": "example-current-conversation",
  "markdownReviewRelativePath": "outputs/ceaia/registration-status/STORY.md"
}
```

Exactly five nonempty strings, no content or attachments. Only an explicit successful markdownReview association completes this item.

## A: authorized update with no attachment change

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "issueIdOrKey": "DEMO-99",
  "description": "h1. View registration status\n\nh2. User Story\nAs a member, I want to view my registration status.\n\nh2. Attachments\nNo attachment changes.",
  "dynamicFieldsJson": "{\"fields\":{\"customfield_12345\":\"AC-001: Given a saved status, when I open registration, then I see that status.\"}}"
}
```

The local reviewed SPEC remains available for scoring without being uploaded. Omit unchanged AC only when its baseline matches and the reviewed plan allows omission. After confirmed A success this update still requires B with returned DEMO-99 and its own scored Story path.

## Batch A nesting

Each item's dynamicFieldsJson/attachmentsJson is still a JSON string inside issuesJson. This one-item array only demonstrates encoding; an actual batch includes every selected item.

```json
{
  "staffId": "12345678",
  "almType": "wpb",
  "conversationId": "example-current-conversation",
  "issuesJson": "[{\"projectKey\":\"DEMO\",\"summary\":\"View registration status\",\"issueType\":\"Story\",\"description\":\"h1. View registration status\\n\\nh2. User Story\\nAs a member, I want to view my registration status.\\n\\nh2. Attachments\\nregistration-status-spec.md\",\"dynamicFieldsJson\":\"{\\\"fields\\\":{\\\"customfield_12345\\\":\\\"AC-001: Given a saved status, when I open registration, then I see that status.\\\"}}\",\"attachmentsJson\":\"{\\\"add\\\":[{\\\"relativePath\\\":\\\"outputs/ceaia/registration-status/registration-status-spec.md\\\",\\\"fileName\\\":\\\"registration-status-spec.md\\\"}]}\"}]"
}
```

Match each returned resource to its own candidate; call B separately for every eligible returned key with that candidate's final scored Story path. Do not bind failed/unknown items or inherit a top-level path/attachment plan.
