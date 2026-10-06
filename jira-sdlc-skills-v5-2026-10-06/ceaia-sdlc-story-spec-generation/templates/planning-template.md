# CEAIA Internal Planning

Internal only. Replace example cells; use mode-specific planningPath from workflow-contract.md.

## Source Evidence Register
| Evidence ID | Class | Exact source / section / location | Supported fact | Limitation | Used by SR |
|---|---|---|---|---|---|
| EV-001 | Direct requirement | [source location] | [fact] | [limitation or None] | SR-001 |

## Atomic Requirement Register
| SR ID | Evidence IDs | Requirement | Actor / trigger | Journey / state | Rule / data / dependency | Observable outcome | Disposition / candidate |
|---|---|---|---|---|---|---|---|
| SR-001 | EV-001 | [requirement] | [actor/event] | [known state] | [supported rule] | [outcome] | [candidate or clarification or supported exclusion] |

## Coverage Capability Matrix
| Candidate / journey | UI | API / FSD | Backend | Integration / async | Persistence | Downstream / reporting | Permission / security | Coverage statement |
|---|---|---|---|---|---|---|---|---|---|
| [candidate] | Confirmed | Not provided | Not provided | Not provided | Not provided | Not provided | To confirm | [honest supported scope] |

## UX State Inventory
| Screen / state | Evidence IDs | Actor / entry | Actions | Decision / validation | Transition / visible result |
|---|---|---|---|---|---|
| [state or N/A] | [EV IDs] | [actor] | [actions] | [rules] | [result] |

## Candidate Register
| Candidate ID | Mode / source / ticket | Value and scope | SR IDs | Independent delivery or inherited baseline rationale | Dependencies | Readiness / blockers | Final SPEC name | Attachment action |
|---|---|---|---|---|---|---|---|---|
| [unique ID] | [mode/identity] | [outcome] | SR-001 | [rationale] | [confirmed/gap] | [ready or exact gap] | [final-name.md] | [add/replace/none] |

## Artifact Traceability
Fill after corresponding artifacts exist. Do not create placeholder Test Case/SPEC files.
| SR ID | Candidate | Story scope / AC IDs | FR IDs | TC IDs | Status / supported exclusion |
|---|---|---|---|---|---|
| SR-001 | [candidate] | AC-001 | FR-001 | TC-001 | [covered or gap] |

## Test Design Register
| AC / FR | Preconditions | Trigger / data | Expected outcome | Verification location | Type | Priority | Negative disposition / rationale |
|---|---|---|---|---|---|---|---|
| AC-001 / FR-001 | [state] | [input] | [observable] | [supported observation] | [type] | [priority] | [Covered/N/A/To Confirm and rationale] |

## Global Coverage Decision
- Original evidence reconciled: [yes/no and precise omissions]
- Selected candidate IDs: [complete list]
- Every in-scope SR has a disposition: [yes/no]
- Blocking clarifications: [exact items or None]
- Unsupported claims / cross-Story inconsistencies: [items or None]
