# Flow Create Issue

## Scope

Use this module when the user asks to create, generate, export, or submit a new Jira issue, including a user story, task, bug, epic, or similar issue type.

Also load:

- `common-tools-and-inputs.md`
- `common-state-and-user-info.md`
- `common-project-issue-type-validation.md`
- `common-field-assembly.md`
- `common-preview-confirm-export.md`
- `common-guardrails.md`

## Recognize New-Issue Intent

Treat these expressions as new Jira issue intent:

- create Jira issue / generate Jira / export Jira / submit Jira
- Create a user story about ...
- write a user story for ...
- generate a story / task / bug / epic from a business request

After recognizing new-issue intent, generate content and a preview first. Final submission still requires explicit confirmation.

## Known-State Shortcut

If conversation state already has `staffId`, `almType`, and selected `projectKey`:

1. Do not ask for source or project keyword again.
2. Do not call `queryJiraProjectsByName` again.
3. Directly call `queryJiraIssueTypesByProject` with only the selected `projectKey`.
4. Continue with issue type selection, metadata, field assembly, preview, and confirmation.

Follow `common-state-and-user-info.md` before any popup.

## Standard Flow

1. Collect or reuse `staffId`, Jira source, and project keyword according to `common-state-and-user-info.md`.
2. Validate and select the project according to `common-project-issue-type-validation.md`.
3. Query and select issue type according to `common-project-issue-type-validation.md`.
4. Call `queryJiraCreateMetaFields` with `staffId`, `almType`, selected `projectKey`, and selected `issueType` or `issueTypeId`.
5. Assemble required fields, optional fields, summary, description, labels, and `dynamicFieldsJson` according to `common-field-assembly.md`.
6. Write `jira-preview.md`, show the conversational preview, ask for confirmation, submit, and handle results according to `common-preview-confirm-export.md`.

## User Story Generation Requirements

When the user asks to create a user story, generate Jira-friendly content:

- `summary`: Cover the core business capability clearly and concisely.
- `description`: Use `As a [persona], I want [feature], so that [benefit].`.
- `acceptance criteria`: Cover key paths while avoiding unsupported business assumptions.
- metadata-derived fields: Assemble automatically from metadata.
- labels: Always include `CEAIA_GEN`.
