# Common Project And Issue Type Validation

## Project Lookup

When collecting or validating a project, first load reusable values from conversation context, user workspace context, and `jira-user-info.md`.

If the user already provided a project name, project key, or project keyword, still call `queryJiraProjectsByName` first to validate that the project exists unless an already selected project remains valid and the user did not ask to change it.

When calling `queryJiraProjectsByName`, use only these fixed conditions:

- `almType`
- `projectName`
- `staffId`, only when required by tool authentication/context

Do not dynamically load project query parameters or append other search conditions.

## Project Selection

After project query results return:

- If the user's provided project clearly matches one returned project, store that project and continue without asking for a project popup again.
- If multiple returned projects could match the user's provided project, show a concise numbered list and ask the user to select from the returned projects.
- If there are zero results, ask for another keyword and do not continue to issue type lookup.
- If there is exactly one result, still ask the user to confirm it.
- When selection is needed, include at least project key and project name for each item.

Store selected `projectKey` and project name.

Do not invent or accept a project key that is not returned by `queryJiraProjectsByName`, unless the user explicitly provides a known exact project key and accepts Jira validation risk.

## Issue Type Lookup

When calling `queryJiraIssueTypesByProject`, pass only:

- selected `projectKey`

Do not pass source, project name, staff id, or other dynamic conditions unless the MCP runtime requires authentication context outside tool arguments.

## Issue Type Selection

After results return:

- If the user clearly provided an issue type and it exists in the returned issue type list, store the returned issue type name and id when available, then continue directly without showing the issue type popup.
- If the user clearly provided an issue type but it does not exist in the returned issue type list, show the dynamic issue-type popup from all returned issue types.
- If the user did not provide an issue type, generate a dynamic issue-type popup from the actual returned results.
- If no issue types are returned, tell the user that issue types could not be found for the selected project and ask whether they want to choose another project.

Any issue-type popup must:

- include every returned issue type
- preserve `id` and `name` for each option
- avoid filtering, truncating, hiding, or de-duplicating by display name

Do not use a fixed issue type list. Do not proceed with a user-provided issue type until it has been validated against `queryJiraIssueTypesByProject`.

Store selected issue type name and issue type id, if available.
