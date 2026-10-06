# Evidence planning and functional preservation checklist

Read during initial planning, material evidence changes and re-decomposition. This restores concrete business checks without copying the score service's rubric.

## Input coverage

Inventory every supplied document, pasted requirement, ticket field, readable attachment, UX screen, API/FSD/data contract and approved clarification. Classify direct requirement, UX evidence, implementation/dependency constraint, background or explicitly non-deliverable material. Preserve conflicting sources separately until resolved.

For each SR row record exact evidence location, actor/trigger, journey/screen/state, action/decision, rule/data/dependency and observable outcome. Each explicit in-scope requirement must map to a candidate, a blocking clarification or a source/user-supported exclusion.

Check explicitly for requirements easy to overlook: downstream integration, reporting, notifications, audit, fraud, retention, operational MI and testing obligations. Missing detail is never evidence that these can be dropped or placed Out of Scope.

## UX and coverage layers

For supplied designs inspect initial, loading, success, validation, error, empty, unavailable and permission states; entry points, actors, controls, actions, transitions, displayed values and validations. Record only supported facts.

The Coverage Capability Matrix distinguishes UI, API/FSD, backend rules, integration/async, persistence, downstream/reporting and permission/security. Mark Confirmed, Not provided or To confirm. UI confirmation does not establish storage, notification delivery, audit persistence or end-to-end integration.

When relevant separate:
- permission: role, allowed/denied action, entry conditions, visible outcome, explicit audit consequence;
- data: displayed, entered, derived, retained and transmitted values;
- asynchronous work: accepted, queued, complete, failed, timeout/retry and visible status;
- delivery dependencies: upstream data, entitlement, configuration/feature flags, migration, release order and operational support.

Do not invent every category. For an explicit requirement retain it; for a material gap ask; for a genuinely irrelevant layer record a rationale.

## Candidate readiness and decomposition

| Check | Required decision |
|---|---|
| Actor/trigger | Who initiates which event? |
| Value | What independently observable outcome changes? |
| Scope | Which journey, state, action and ownership boundary are included? |
| Rules | Which validations/decisions are evidenced, which are unknown? |
| Dependencies | Which confirmed dependencies affect release or verification? |
| Acceptance | Can evidenced conditions/actions/results be expressed in executable BDD? |

Assess INVEST: independent value and dependencies, negotiable implementation rather than invented design, business value, enough clarity to estimate, manageable size and observable tests. Record an evidence-backed sizing/split rationale, not an unsupported numeric size.

Evaluate separate journeys, actor permissions, independently releasable outcomes, decisions, integrations and business ownership for splitting. Keep closely coupled validation/error handling with the outcome it qualifies. Several ACs alone do not force a split; shared screen/entity alone does not justify merging unrelated outcomes.

For existing tickets preserve baseline scope; propose a scope decision rather than implicitly creating tickets. A low score is not authority to discard requirements or strip source-supported dependencies.

## Ready-to-generate checklist

- Every explicit source requirement has a disposition.
- Each new candidate has a primary value outcome and a traceable supported scope.
- Source conflicts and material unknowns have precise questions, not guessed defaults.
- Candidate title is an outcome, not a source filename or programme label.
- Source→SR→candidate links are complete; later extend to AC→FR→TC.
- Supplied metadata and approved design-reference values are recorded for later templates.
- Batch coverage is reconciled against original evidence, not only the shortened register.
- A blocked candidate does not contaminate unrelated candidates; changing the selected Jira batch requires user direction.
