# Export several ordinary Jira issues

Use for a selected group of independent ordinary creates/updates. Test Cases from a structured source use [the dedicated Test Case flow](flow-testcase-source-export.md), including its own `single`/`multiple` choice and fixed business shapes.

## Prepare the selected items

Read each selected actual issue file under [the file contract](common-json4jira-contract.md). Reuse complete files unchanged. Prepare missing files through [create](flow-create-issue.md) or [update](flow-update-issue.md), sharing applicable [profile facts](common-state-and-user-info.md) without inheriting business content, targets or attachment plans between items.

Keep an explicit source-item-to-file mapping and stable selection order. Every child contains its own actual source/project/type and fields; every update contains its own exact root `issueIdOrKey`. No defaults may overwrite unrequested update fields. Missing targets block their updates rather than converting them to creates.

Establish actual type identity before planning calls. Scoring requirements are project/backend-owned: do not preblock an unscored Story or invoke a scorer automatically. Retain any supplied [assessment evidence](story-assessment-evidence.md) accurately; an old result is not current evidence after an input change. A manifest is not a scoring input.

## Build the deterministic submission plan

Preserve the user's original selection order and use this fixed rule:

| Selected types | Submission plan |
| --- | --- |
| All non-Story, such as Bug/Task/Test | One manifest containing all selected non-Story issue paths; one export call. |
| All Story | One individual issue-file path per call, submitted sequentially in selected order. |
| Mixed Story and non-Story | Scan in order. Each maximal contiguous non-Story run becomes one manifest and one call; each Story remains its own individual-file call. Do not move items across a Story boundary. |

A Story never appears in a manifest, including a project exempt from scoring. Do not change type to obtain a batch route. A mixed plan's non-Story run uses a manifest even when that run has one item. For example, Bug A / Story B / Task C / Bug D / Story E becomes manifest[A], B, manifest[C,D], E.

Each non-Story manifest has exactly one property, `story_json_workspace_paths`: a nonempty native array of actual unique issue-file paths in selected order. It contains no inline issues, defaults, nested manifests, credentials or workflow controls. Each listed child contains its own actual routing and fields. Read back each manifest and all its children; non-Story manifest support is confirmed and needs no extra applicability gate.

Resolve required file/target/operation blockers before final confirmation, or obtain an explicit revised selection. Do not silently omit an invalid selected item. Missing upstream scoring evidence alone is not such a helper blocker.

## Confirm the whole plan and submit in order

Follow [the common preview/submission rules](common-preview-confirm-export.md) once for the whole current package. Show every selected item and its actual operation/target, all manifest membership/order, individual Story paths, intended attachments/relationships, available assessment evidence and the exact ordered call plan. Confirmation binds all current files, participating uploads and that plan.

After unchanged read-back, execute each planned call sequentially through `java-base-mcp.exportjirabydynamicfield`, with exactly one `story_json_workspace_path`: either that non-Story manifest or that individual Story file. Do not pass arrays or additional fields beside the path. Preserve each actual response before proceeding to the next call.

Stop further calls at the first failed, partial or unknown required synchronous outcome, including a backend score-required rejection. Retain all confirmed effects and all unattempted selected files in order. An accepted asynchronous score-sync queue that is still pending is not itself a synchronous submission failure. No automatic scoring, type change, retry or rollback is used to continue the plan.

## Map results and recover the failed layer only

Map actual returned paths/indexes to the approved manifest's child order and then to source items. Report each individual Story call separately. Never guess index bases, consecutive Jira keys, or a key for an unreturned item. Unmatched results remain unknown.

Keep confirmed successes after partial failure. Do not replay a whole manifest, recreate a successful item, re-upload a confirmed file or rescore to repair another effect. Use only supported focused reconciliation/recovery under the common submission reference. Any later continuation or subset retry needs an established unapplied operation set, its current files and confirmation; do not treat a vague continue as permission to replay uncertain writes.
