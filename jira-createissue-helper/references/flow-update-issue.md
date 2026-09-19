# Flow Update Issue

## Scope

Use this module when the user provides a Jira ticket key or Jira link and asks to modify, update, change, or add information.

Also load:

- `common-tools-and-inputs.md`
- `common-state-and-user-info.md`
- `common-field-assembly.md`
- `common-preview-confirm-export.md`
- `common-guardrails.md`

## Recognition And Extraction

1. Extract `issueIdOrKey` from an explicit key or `/browse/<KEY>` link.
2. Infer `almType` from the Jira link host when possible.
3. If `almType` cannot be inferred, collect or reuse it according to `common-state-and-user-info.md`.
4. If `staffId` is missing, collect or reuse it according to `common-state-and-user-info.md`.

Treat this as an update flow, not a new issue flow.

## Read Current Information First

Before generating any update payload, call:

- `java-base-mcp.getJiraInfos`

Query fields relevant to the requested change, for example:

- `summary`
- `description`
- `comments`
- `priority`
- `assignee`
- `changelog`
- `AcceptanceCriteria`

For broad requests, query enough fields to understand the current ticket state.

## Confirm Update Field Rules

Before generating any update payload, use the current Jira project's project key and issue type from `getJiraInfos` to call:

- `java-base-mcp.queryJiraCreateMetaFields`

Treat the returned metadata as authoritative for the fields the user wants to update. Verify each proposed field's `fieldId`, `schema`, `operations`, `allowedValues`, and required/value rules before assembling `dynamicFieldsJson`.

If the project key or issue type cannot be determined, or the metadata query fails or returns incomplete field rules, do not generate a final update payload and do not call `exportJiraByDynamicFields`. Explain what is missing and request the information or a metadata refresh.

## Generate Proposed Changes

After `getJiraInfos` succeeds:

1. Summarize the current Jira information.
2. Summarize the proposed changes.
3. Confirm the update field rules with `queryJiraCreateMetaFields` as described above.
4. Assemble only fields that need changes and conform to the confirmed metadata rules.
5. Include `CEAIA_GEN` according to `common-field-assembly.md`.
6. Write or overwrite `jira-preview.md`.
7. Wait for explicit user confirmation.
8. Call `exportJiraByDynamicFields` only after both metadata confirmation and explicit user confirmation.

Update intent itself is not submission confirmation.

## Update Payload

After `queryJiraCreateMetaFields` confirms the field rules and the user confirms the preview, call `exportJiraByDynamicFields` with parameters such as:

- `staffId`
- `almType`
- `issueIdOrKey`
- `summary`, only when updating summary
- `description`, only when updating description
- `dynamicFieldsJson`
- `testDetailsJson`
- `epicLink`
- `parentLink`

`dynamicFieldsJson` must include only the fields that need changes.
