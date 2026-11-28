# CEAIA Preparation — [candidate]

Save only as <historyRoot>/preparation.md for a new candidate. paths.planningPath and exportContext.assembly.recordPath point to this same file, with separate Plan/evidenceRevision and Assembly/storyJsonRevision sections. Keep compact current facts; preserve sections already used by review/score/export and append an affected section only for actual changes. Fill an initial working section in place; resume, a phase change or a no-op never creates another file/revision. No separate preparation-handoff.md is needed.

## Plan — evidenceRevision [actual value]

Internal evidence only. Use actual source locations and short facts; preserve original documents at their actual paths. Unknown material facts become questions, never exclusions or invented defaults.

## Requirement and source register
| SR ID | Exact source location | Supported obligation / actor / trigger / result | Candidate | Disposition / material question |
| --- | --- | --- | --- | --- |
| SR-001 | [source/section] | [fact] | [candidate] | [in scope / approved exclusion / waiting] |

## Candidate decision
| Candidate / mode / source / ticket | Cohesive value and scope | SR IDs | Split / inherited-scope rationale | Dependencies / blockers | Canonical Story package / SPEC names / attachment action |
| --- | --- | --- | --- | --- | --- |
| [identity] | [supported outcome] | SR-001 | [short evidence-based decision] | [actual facts] | [actual JSON and distinct Description/AC wiki paths / synchronized SPEC names / add reviewed SPECs for create or update, or explicit none] |

## Artifact coverage
Fill when files exist; do not create placeholder future artifacts.
| SR ID | Candidate | AC IDs | FR IDs | TC IDs | Covered / exact gap |
| --- | --- | --- | --- | --- | --- |
| SR-001 | [candidate] | AC-001 | FR-001 | TC-001 | [actual coverage] |

## Source-specific details — include only when applicable
[Supported UX states/transitions, API/data rules, async/integration dependencies or original-attachment preservation. Use exact source locations. Distinguish Confirmed / Not provided / To confirm; omit irrelevant categories.]

## Current check and decisions
- Q1–Q7 self-check and production precheck: [actual result / defects]
- Original obligations reconciled: [covered SRs, approved exclusions and pending SRs]
- Clarifications / user scope decisions: [exact facts or None]

- Jira assembly snapshot: [Assembly section in this same internal file and exact JSON plus two wiki revisions; complete before scoring]
- Update synchronization: [Story business delta → affected local SPEC/tests/projection changes or verified unchanged sections. Record baseline obligations, reviewed new SPEC paths or explicit none, and material evidence gaps.]
- Update baseline evidence: [related original SPEC content and actual source/ticket binding. Preserve all existing attachments and resolve material evidence gaps.]

## Assembly — storyJsonRevision [actual value]
- Actual JSON and two distinct wiki paths, mode and target: [verified current facts]
- Persistent profile selection and settings decision: [actual available Workspace operation/user-destination binding and read outcome; selected user/source/project/Story issue-type key; existing-settings Use/Change/project-switch confirmation when applicable; reusable defaults versus run-only overrides; populated snapshot for post-create/update save]
- Optional fields decision: [provided/skipped/reused plus actual user answer/explicit skip or approval of displayed saved fields; source/project-only choices are insufficient; exportContext.assembly.optionalFieldsDecision.recordPath points to this section and remains pending until that evidence exists]
- Source/project/type/field mapping and confirmed values: [compact schema and field-provenance rows; real profile/query/baseline references]
- Relationship targets: [confirmed key/source, requirement-source versus relationship-only role, actual lookup status/type; empty body is not invalid]
- Attachment representation: [Root attachmentsJson.add with actual relativePath/fileName. Omit legacy attachments for add. Explicit none uses attachments=[] and omits attachmentsJson.]
- Attachment confirmation: [Show the exact reviewed new files and retained existing attachments. Old IDs are baseline evidence only.]
- Currentness/read-back: [actual paths/revisions and observed verification, never invented success]

<!-- Initial working sections may be filled until first consumption. Later real changes append the affected version-bound section and reference unchanged prior facts. Do not duplicate complete sources, JSON bodies or raw tool responses here. -->
