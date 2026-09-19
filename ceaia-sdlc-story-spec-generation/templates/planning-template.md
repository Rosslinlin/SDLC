# SDLC Internal Planning Register

> Internal artifact only. Do not expose this file, source links, source paths, or source identifiers in Jira-facing content.

## Source Evidence Register

| Evidence ID | Evidence class | Source summary | Reliability / limitation | Used for |
|---|---|---|---|---|
| EV-001 | Direct requirement | [Concise source fact] | [Known limitation] | [SR IDs / Story candidates] |

## Atomic Requirement Register

| SR ID | Source requirement | Actor / trigger | Journey / screen / state | Action / decision | Rule / data / dependency | Expected outcome | Evidence class | Coverage disposition |
|---|---|---|---|---|---|---|---|---|
| SR-001 | [Evidence-backed requirement] | [Actor/event] | [Known location or Not provided] | [Action/decision] | [Known fact] | [Observable result] | Direct requirement | Story candidate ST-001 |

## Coverage Capability Matrix

| Journey / Story candidate | UI states | API / FSD | Backend rules | Integration / async | Data persistence | Downstream / reporting | Permission / security | Coverage statement |
|---|---|---|---|---|---|---|---|---|
| ST-001 | Confirmed | Not provided | To confirm | Not provided | Not provided | Not provided | Confirmed | Frontend behavior coverage only |

## UX/UI State Inventory

| Screen / state | Entry point | Actor | Controls / actions | Decision / validation | Visible outcome | Evidence |
|---|---|---|---|---|---|---|
| [Screen] | [Entry point] | [Actor] | [Actions] | [Rule] | [State / message / transition] | EV-001 |

## Story Candidate Matrix

| Candidate ID | Proposed Story | Business value | SR IDs | Journey / state / action | Acceptance outcome | Dependencies | Independent delivery rationale |
|---|---|---|---|---|---|---|---|
| ST-001 | [Title] | [Value] | SR-001 | [Scope] | [Observable outcome] | [Known dependency] | [Why this is a vertical slice] |

## Test Design Register

| AC / FR | Preconditions | Trigger / input | Expected outcome | Verification location | Scenario type | Priority | Negative coverage disposition |
|---|---|---|---|---|---|---|---|
| AC-001 | [State] | [Action/data] | [Observable result] | [UI/API/log/data/report] | Positive | P0 | Covered |
