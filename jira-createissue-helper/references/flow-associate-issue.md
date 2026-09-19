# Flow Associate Issue

## Scope

Use this module when the user asks to link, associate, relate, or add a relationship between Jira issues.

Also load:

- `common-tools-and-inputs.md`
- `common-state-and-user-info.md`
- `common-field-assembly.md`
- `common-preview-confirm-export.md`
- `common-guardrails.md`

## Association Information Extraction

Extract:

- `source`: Jira source, for example `wpb`.
- `sourceIssueKey`: Source Jira key.
- `targetIssueKey`: Target Jira key.
- `linkType`: Relationship type.

Example:

> https://wpb-jira.systems.uk.hsbc/browse/AIWPB-6705 to AIWPB-7799, under Relates to

Parse as:

- `source`: `wpb`
- `sourceIssueKey`: `AIWPB-6705`
- `targetIssueKey`: `AIWPB-7799`
- `linkType`: `Relates`

Normalize user wording such as `Relates to` to the Jira link type `Relates`.

## Association Update Order

Execute in this exact order:

1. Collect or reuse missing `staffId` according to `common-state-and-user-info.md`.
2. Call `getJiraInfos` to read current information for the source `sourceIssueKey`.
3. Identify source project key and issue type from the source Jira information.
4. Read target Jira information as needed for preview display.
5. Call `queryJiraCreateMetaFields` with the source Jira project key and issue type to validate association fields/metadata.
6. Assemble the association update payload, keeping `linkedIssueKeys` and `linkType` as dedicated top-level parameters; never place them in `dynamicFieldsJson`.
7. Write `jira-preview.md` and show source, target, normalized link type, and complete payload.
8. Await explicit confirmation.
9. After confirmation, call `exportJiraByDynamicFields` with `issueIdOrKey = sourceIssueKey`.

Do not call project search or issue-type discovery for association updates.

## Association Payload Example

```json
{
  "staffId": "<staffId>",
  "almType": "wpb",
  "issueIdOrKey": "AIWPB-6705",
  "linkedIssueKeys": ["AIWPB-7799"],
  "linkType": "Relates",
  "dynamicFieldsJson": "{\"fields\":{\"labels\":[\"CEAIA_GEN\"]}}"
}
```
