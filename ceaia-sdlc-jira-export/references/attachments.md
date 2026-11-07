# SPEC synchronization and confirmed JSON attachment operations

Generation assembles the final file and authorized plan before scoring. Export verifies the unchanged file and mappings. The user has tested the following root attachmentsJson object in the new path-based JSON flow; do not reopen its format as an unknown capability.

## File format and scoring files

attachmentsJson is a native object inside story.json4jira, not a serialized JSON string or another exporter argument. Use these confirmed shapes with actual values:

```json
{
  "attachmentsJson": {
    "add": [{"relativePath": "<actual new SPEC path>", "fileName": "<actual new SPEC basename>"}]
  }
}
```

```json
{
  "attachmentsJson": {
    "replace": [{"attachmentId": "<actual old ID returned by readJiraAttachments>", "relativePath": "<actual new SPEC path>", "fileName": "<actual new SPEC basename>"}]
  }
}
```

These are root-property examples, not complete Stories or real inputs. relativePath is the exact readable Workspace-relative new file path; fileName matches its actual basename. Each replace entry binds one selected original attachmentId string to its reviewed new file. Multiple originals keep their actual separate entries and approved mappings. Do not use filename matching in place of attachmentId.

For add/replace, omit the old attachments path list; never keep a nonempty attachments list alongside attachmentsJson, which could upload the same file twice. A new Story adds its reviewed primary SPEC using attachmentsJson.add. For explicit no-remote-attachment-change intent, keep attachments=[] and omit attachmentsJson entirely. scoreUserStory still receives the current local SPEC separately through specFilePath in every mode. A score-only SPEC is not an implicit upload.

Only current reviewed SPECs are uploaded in this route; Test Case, state, plans, baselines, profiles and score/review records are not attachments. Story business changes synchronize every affected local SPEC projection, requirements/flows and tests before scoring, even when the user does not mention SPEC. None never skips local synchronization; a routing/schema-only change or already consistent content needs a consistency check, not a fabricated edit.

## Update target and plan

The scored update JSON has root issueIdOrKey after issueType, containing the exact nonempty approved ticket key matching the baseline and confirmed source. Creates omit it. The exporter still passes only story_json_workspace_path; neither issueIdOrKey nor attachmentsJson is a second tool argument.

Generation uses the existing readJiraAttachments result for that target's inventory, readable content and original attachment IDs before scoring:

| Plan | Selection and file content |
| --- | --- |
| replace | Positively identified related Jira SPECs exist: use attachmentsJson.replace with their actual returned IDs and reviewed current SPEC outputs. Story-only wording does not turn this into add or none. |
| add | Complete inventory and resolved classification establish no related SPEC: use attachmentsJson.add for the first SPEC. An unresolved/unreadable possible SPEC is not evidence of absence. |
| none | The user explicitly preserves remote attachments: attachments=[], no attachmentsJson; local SPECs remain synchronized, reviewed and scored. |

### Select only evidenced SPEC originals

Each replacement mapping must bind the confirmed source/ticket, that ticket's actual original ID/name returned by readJiraAttachments, its read body identifying it as the maintained SPEC for this Story/business function, and its exact reviewed new SPEC path/revision. Do not guess IDs, take them from a filename/user-entered number or borrow another source/ticket/candidate's ID. A name containing spec, extension, MIME type, upload order or directory is not sufficient SPEC identification; legacy SPEC names need not follow new output naming.

Attachments used only as requirements/design/FSD, screenshot, spreadsheet or other evidence remain untouched even when read or cited; a legacy design qualifies only when its actual purpose/content identifies it as this Story's maintained SPEC. Preserve non-SPECs and unselected SPECs. Possible SPECs with unresolved identity, unreadable body or ambiguous relation stay preserved and return upstream for classification/selection clarification. Default synchronization authorizes no deletion of these files. Distinct contents retain distinct mappings; reviewed many-to-one replacement must preserve each original's obligations. Only the exact positively identified and selected IDs belong in replace.

## Preview, submission and results

Before current-content confirmation and immediately before submission, read each new SPEC and the primary scoring SPEC. Reuse complete unchanged reads; check exact path/basename, reviewed revision and each approved old-ID mapping against the scored attachmentsJson. Changed files/IDs/operations return to generation for affected review/scoring and renewed confirmation; exporter never inserts or reserializes these operations after scoring. Final preview identifies selected old SPECs/IDs, new files, the upload-confirm-then-remove order and retained attachments.

Submit the confirmed replace object through the same single-path exporter. Replacement must upload the new file successfully before removing only its mapped old original; never pre-delete, emulate replace with add, or call an independent delete/legacy API. The tested format does not prove completed effects or backend atomicity; inspect actual results rather than requiring a new capability/atomicity proof before ordinary replace. An actual service rejection or unknown outcome stays precisely pending, without schema invention or fallback to create/add/none.

Record issue success, each new upload and each mapped old-ID removal separately. Replacement is complete only with actual evidence of both effects or a documented response guarantee covering them; a new file alone is not replacement. Failed/unknown upload preserves old originals. Confirmed upload with failed/unknown removal is partial: retain successful uploads and remaining originals, reconcile unknown IDs through supported read-only evidence, and report the unresolved removals. Do not re-export/re-upload confirmed success or introduce standalone delete; a recovery needs an actually supported focused contract, otherwise precise WAITING_TOOL. See [submission and recovery](jira-write-and-recovery.md).
