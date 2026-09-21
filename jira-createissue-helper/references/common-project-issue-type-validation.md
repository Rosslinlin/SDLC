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
- If multiple returned projects could match the user's provided project, generate a dynamic project popup from the actual returned results. Do not ask the user to choose by replying in normal chat.
- If there are zero results, collect another `projectName` search keyword through the allowed popup input and do not continue to issue type lookup.
- If there is exactly one result, still confirm it through a project popup before continuing.
- When selection or confirmation is needed, each popup option must include at least project key and project name.

Any project popup must:

- include every returned project that could match the user's provided project or keyword;
- preserve the returned project key and project name for each option;
- avoid filtering, truncating, hiding, or de-duplicating returned project options unless the backend result itself is duplicated; and
- store the selected `projectKey` and project name before issue type lookup.

Store selected `projectKey` and project name.

Do not invent or accept a project key that is not returned by `queryJiraProjectsByName`, unless the user explicitly provides a known exact project key and accepts Jira validation risk.

Do not use a fixed project list. Do not proceed with a project selected from chat text when project selection or confirmation is required by the rules above.

## Issue Type Lookup

When calling `queryJiraIssueTypesByProject`, pass only:

- selected `projectKey`

Do not pass source, project name, staff id, or other dynamic conditions unless the MCP runtime requires authentication context outside tool arguments.

## Issue Type Selection

After results return:

- If the user clearly provided an issue type and it exists in the returned issue type list, store the returned issue type name and id when available, then continue directly without showing the issue type popup.
- If the user clearly provided an issue type but it does not exist in the returned issue type list, show the dynamic issue-type popup from all returned issue types.
- If the user did not provide an issue type, generate a dynamic issue-type popup from the actual returned results.
- If no issue types are returned, tell the user that issue types could not be found for the selected project and collect the next project choice through the allowed project popup flow.

Any issue-type popup must:

- include every returned issue type
- preserve `id` and `name` for each option
- avoid filtering, truncating, hiding, or de-duplicating by display name

Do not use a fixed issue type list. Do not ask the user to choose an issue type by replying in normal chat. Do not proceed with a user-provided issue type until it has been validated against `queryJiraIssueTypesByProject`.

Store selected issue type name and issue type id, if available.
