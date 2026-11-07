# Create a Jira issue

Use for a new ordinary Jira issue or an authorized request to generate its content. Structured Test Case sources use [their own flow](flow-testcase-source-export.md).

## Reuse a prepared issue file

Read the complete selected JSON and its actual Workspace path under [the file contract](common-json4jira-contract.md). If its create intent, destination, type and required field facts are established, retain it unchanged and skip project/type/metadata discovery. Do not regenerate its Summary, Description, AC or Jira-wiki presentation.

A create file omits root `issueIdOrKey`. A file that already names an actual update target needs the [update flow](flow-update-issue.md) and matching user intent; never delete its target merely to make it export as create. Resolve a destination or operation conflict before submission.

## Prepare only missing inputs

1. Read current confirmed context and the real user-workspace profile under [configuration reuse](common-state-and-user-info.md).
2. Resolve only missing project, issue type or field facts under [discovery](common-project-issue-type-validation.md). A known complete file/profile does not need a fresh query chain.
3. Assemble one real `.json4jira` under [field preparation](common-field-assembly.md). Preserve supplied export-ready business content. Generate new content only within the user's actual generation request; identify it as generated in the preview. Do not invent unknown business rules to satisfy required fields.
4. Complete applicable non-Test [Jira text rendering](common-jira-text-rendering.md) during this preparation. Save and read back the final file before preview and confirmation.

## Confirm and submit

Use [assessment evidence](story-assessment-evidence.md) when an upstream Story result is present. Keep current evidence accurate and identify an old result as historical; the project/backend decides whether scoring is required. This helper does not preblock an unscored Story, impose a threshold, or automatically invoke scoring/reviewer before export.

Use [attachments](common-attachments.md) only for actual selected uploads; ordinary issue creation does not generate or require a SPEC. Establish the final upload list before current-content confirmation. If it changes an assessed input, do not claim that earlier assessment is still current.

Follow [preview, confirmation, submission and recovery](common-preview-confirm-export.md). Present the actual saved file, obtain/reuse confirmation for this current content, and call `java-base-mcp.exportjirabydynamicfield` with only its `story_json_workspace_path`. Every Story uses its own issue-file path and individual call; multiple Stories follow [the ordered submission plan](flow-batch-export.md), never a manifest. Preserve the actual response and each observed outcome. A backend score-required rejection remains precise pending work; refer to reviewer when the user requests assessment, without an automatic score/export loop. No additional score-binding or ticket/source synchronization call follows this JSON export.
