# Jira Create Issue Helper

A modular skill for create, update, batch, association, Test Case and validated CEAIA SDLC Jira writes.

## Guarantees

- Preview every Jira write. Generic routes retain explicit confirmation; the validated CEAIA SDLC gateway performs direct export after its final read-back/preflight and has no approval-tool step.
- Assemble metadata from current Jira field definitions.
- Add the default `CEAIA_GEN` label whenever labels are included or changed.
- Read current Jira information before an existing-ticket update.
- Preserve Test Case execution fields during source-to-payload mapping, allowing only Jira-safe line-break normalization with newline characters, not literal `<br>` tags.
- Keep Jira-visible Test Case fields self-contained.
- Route validated CEAIA SDLC handoffs through a dedicated gateway without changing the generic business flows.

## Usage

Read `SKILL.md` first, then only the matching modules:

- Create: `flow-create-issue.md`, `common-tools-and-inputs.md`, `common-state-and-user-info.md`, `common-project-issue-type-validation.md`, `common-field-assembly.md`, `common-preview-confirm-export.md`.
- Update: `flow-update-issue.md`, `common-tools-and-inputs.md`, `common-state-and-user-info.md`, `common-field-assembly.md`, `common-preview-confirm-export.md`.
- Associate: `flow-associate-issue.md`, `common-tools-and-inputs.md`, `common-state-and-user-info.md`, `common-field-assembly.md`, `common-preview-confirm-export.md`.
- Batch: `flow-batch-export.md`, `common-tools-and-inputs.md`, `common-state-and-user-info.md`, `common-project-issue-type-validation.md`, `common-field-assembly.md`, `common-preview-confirm-export.md`.
- Test Case source export: `flow-testcase-source-export.md`, `flow-batch-export.md`, `common-field-assembly.md`, `common-preview-confirm-export.md`.
- CEAIA SDLC: `sdlc-gateway.md`, `sdlc-handoff-validation.md`, `flow-sdlc-validated-handoff.md`, then the common modules named by that flow.
- Attachments: matching business flow plus `common-attachments.md`.
- Guardrails: `common-guardrails.md`.
