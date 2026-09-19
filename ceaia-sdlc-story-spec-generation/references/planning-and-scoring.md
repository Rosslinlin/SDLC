# Mandatory planning gate

## Contents

- Source and atomic requirement registers
- Coverage and UX inventories
- Story candidate matrix
- Audited Story scoring, validation, and outcomes

Before deciding Story count, create or update `.ceaia-work/planning.md` from `templates/planning-template.md`. It is internal only and may contain source references.

## 1. Source Evidence Register

Record each input as direct requirement evidence, UX/UI journey evidence, implementation or dependency constraint, background/domain context, or non-deliverable information. Preserve a precise source reference and do not elevate background material to a requirement without support.

## 2. Atomic Requirement Register

Break every explicit in-scope requirement into a stable internal `SR-###` row:

| Field | Required content |
|---|---|
| Source requirement | Concise evidence-backed statement |
| Actor / trigger | Who or what starts the behavior |
| Journey / screen / state | Known UX/UI location or `Not provided` |
| Action / decision | User action and system decision |
| Rule / data / dependency | Known validation, data, integration, permission, or policy fact |
| Expected outcome | Observable business result |
| Evidence class | Direct requirement / UX / constraint / background |
| Coverage disposition | Story candidate, clarification, or explicitly excluded |

Every explicit in-scope item must be assigned to a Story candidate or raised as a true clarification. Do not silently omit downstream integration, reporting, notification, audit, fraud, retention, operational MI, or testing requirements.

## 3. Coverage Capability Matrix

For each journey, mark `Confirmed`, `Not provided`, or `To confirm` for UI behavior and states; API/FSD contract; backend rules; integrations/asynchronous behavior; data persistence; downstream/reporting behavior; and permission/security.

When only frontend evidence exists, state that generated tests are frontend behavior coverage, not end-to-end coverage. Create focused clarification items for missing layers rather than inventing backend behavior.

## 4. UX/UI state inventory

When UX/UI material exists, record supported facts for each screen: entry point and actor; controls and actions; initial, loading, success, validation, error, empty, unavailable, and permission states; transitions; and visible outcome; and values/validations shown by the design. Do not infer backend behavior solely from a visual design.

## 5. Story Candidate Matrix

Create a row for each independently testable value slice. Include proposed title and user/business value; source requirement IDs; journey, state, action, or decision covered; acceptance outcome; dependencies/release constraints; and why it is independently deliverable and testable.

Split a Story when candidate contains multiple independent testable journeys, screens/actions, decisions, user outcomes, permission states, or integration outcomes. Keep related validation and error behavior in the same Story when it is needed to accept the same primary outcome; split it when it has separate business value or materially different delivery ownership.

Do not optimize for fewest Stories. One Story is valid only if it covers its assigned requirements without becoming broad, vague, or untestable.

# Mandatory audited Story quality scoring gate

Process candidates serially. For each candidate, write the current `STORY.md` and score it immediately, before creating any downstream artifact for that candidate. Do not batch-create Story, Test Case, and SPEC files and score afterward. Do not precreate placeholder, empty, copied, reserved, or named `TEST_CASE.md` or SPEC files.

## Authoritative scoring tool and invocation contract

The sole Story evaluator is `nodejs-base-mcp.score_requirement_markdown`. The tool evaluates the generated Jira-ready `STORY.md` directly. Do not create, read, retain, or use an evaluator-input adapter, local rubric, output schema, calibration fixture, local scoring implementation, or hand-calculated score.

For each generated or regenerated `STORY.md`, make exactly one invocation:

```json
{
  "relativePath": "<workspace-relative path to this STORY.md>",
  "force_review": false
}
```

`relativePath` is workspace-relative only. Never supply an absolute path, conversation ID, URL, a path containing `..`, or copied Story body. `force_review` must be boolean `false`.

One call is required even if a prior equivalent Story assessment was returned from cache. A cache-hit result remains the result of that one required call. Do not make a second call to confirm, refresh, retry for a better score, or score another representation of the same generated Story. Tool failure is handled as evaluation recovery, not by an unapproved repeat invocation.

Persist complete returned JSON next verbatim, without formatting, trimming, extracting fields, additions, redactions, or normalization, as `story-quality-score.json` in selected internal root:

- new Story: `.ceaia-work/stories/<story-slug>/story-quality-score.json`
- existing Jira update: `.ceaia-work/updates/<TICKET-KEY>/story-quality-score.json`

Returned record is internal evidence only. It is never embedded in, attached to, linked from, or copied into Jira, `STORY.md`, `TEST_CASE.md`, a SPEC, an update payload, or Jira manifest. Retain full fields as returned including audit and diagnostic fields: `ok`, `cache_hit`, `review_id`, `audit_id`, `content_checksum`, `title`, `finalStatus`, `finalScore`, `scoreBreakdown`, `dimensions`, `criticalIssues`, `warnings`, `strengths`, and `details`. Tool fields are authoritative; do not synthesize replacements for missing fields or reinterpret them with legacy score model.

## Validation and pass rule

After persisting verbatim result, inspect returned JSON only to establish `ok` is boolean `true` and `finalScore` is numeric and at least `76`. A Story passes only when both conditions hold:

```text
ok === true AND finalScore >= 76
```

This is strict greater-than-75 policy: 75 does not pass. Do not use `finalStatus`, a title, a quality label, a dimension result, or aggregate/average score as substitute. Never average scores across Stories; each Story has its own gate.

`review_id`, `audit_id`, and `content_checksum` identify assessment evidence. A changed `STORY.md` is a new assessment version: retain or update planning/audit trace to show new Story content and its newly returned score record, and never carry forward a prior pass, checksum, review ID, audit ID, or score to changed content. Regenerated Story receives exactly one new invocation under invocation contract above.

## Gate outcomes and recovery

- **Pass:** `ok: true` and `finalScore >= 76`. Only then create that Story's `TEST_CASE.md` and matching SPEC.
- **Awaiting source revision:** a successfully returned result that does not meet pass rule. Verify no downstream file exists. Invoke `ask_user_question` with Story title, complete workspace-relative Story path, relevant line/range when known, `finalScore`, `finalStatus`, relevant `criticalIssues`, `warnings`, `dimensions`, and `details`. Offer additional/corrected requirements or explicit stop. Do not automatically rewrite, expand, split, or re-score unchanged evidence.
- **Awaiting evaluation recovery:** tool cannot be invoked, does not return usable JSON, or lacks usable `ok`/`finalScore` determination. Verify no downstream file exists. Invoke `ask_user_question` with technical failure, complete workspace-relative paths of affected input/output, relevant line/range where applicable, and choices to provide usable source material, provide corrected Story input, retry after tool correction, or explicitly stop. Do not invent a result or substitute local evaluator.

After user supplies material, revisit planning evidence and affected candidates, regenerate affected `STORY.md` files from updated evidence, and invoke audited tool once for each regenerated Story. Do not patch only a scoring representation, silently preserve an old score, or continue from failed result. A capability-bundle finding can require re-decomposition; score every resulting Story independently.

Score failure and evaluation failure are active waiting states, not workflow completion. Keep task open until user supplies requested input or explicitly stops. Never identify artifact error using only `STORY.md`, `TEST_CASE.md`, a SPEC filename, or line N. Use `<complete-workspace-relative-path>:<line>` or `<complete-workspace-relative-path>:<start>-<end>` when line information is available.

This scoring gate does not replace `ceaia-spec-review` and does not alter later review-repair policy. Independent review remains mandatory full-artifact gate after passed Story has its Test Case and SPEC.

# Planning execution protocol

## Evidence intake and traceability

Treat planning as controlled translation from supplied evidence into testable delivery slices, not a request to complete missing product design. Start an intake entry for each supplied document, pasted requirement, ticket field, readable attachment, design, API material, clarification, or prior approved decision. Assign internal evidence reference, record source type, capture workspace-relative location when one exists, and state whether it is direct, contextual, constrained, or non-deliverable evidence. Preserve conflicting statements as separate entries until their conflict is resolved; do not silently select more convenient statement.

Planning record must distinguish an observed fact; an interpretation needed to organize facts; a question whose answer changes behavior; and an explicit exclusion. Interpretation may help form candidate but cannot become acceptance criterion unless supported by evidence. Exclusion is valid only when source explicitly excludes work or user confirms exclusion; lack of detail is not an exclusion. For each clarification, name affected `SR###` rows, candidate, decision needed, consequence of no answer, and smallest answer that permits safe continuation.

Maintain forward and reverse traceability. Each source requirement must point to one candidate, a clarification, or supported exclusion. Each Story scope item and AC must point back to one or more `SR###` rows. Each planned test intent must point to an AC or supported rule. Reverse traceability is required so reviewer can begin with source row and find Story, BDD behavior, test intent, and SPEC requirement without guessing from similar title.

## Candidate readiness test

Before generating Story, record readiness decision for candidate. Candidate is ready only when actor, trigger, user or business outcome, supported scope, primary decision path, observable result, and material dependencies are known well enough to write evidence-backed acceptance criteria. Known unknowns may remain when documented as `To confirm` and do not force invented result. If unknown changes primary behavior, validation outcome, permission model, data handling, integration result, or release decision, it is blocker and must be raised through focused question.

Use following decision pattern for every candidate:

| Check | Ready outcome | Waiting outcome |
|---|---|---|
| Actor and trigger | Actor and initiation event are evidenced | Ask for the initiating role or event |
| Value outcome | Business or user result is observable | Ask what successful completion changes |
| Scope boundary | Included behavior and supported exclusions are clear | Ask which journey, state, or system owns the behavior |
| Rules | Validation, policy, and decision facts are evidenced | Mark exact rule gap; do not fabricate defaults |
| Dependencies | Material systems, data, permissions, or sequencing are known | State `To confirm` or ask when gap blocks behavior |
| Acceptance | Outcomes can be expressed in observable BDD form | Return to evidence decomposition |

Do not make score eligibility a planning readiness substitute. A Story can be well structured yet lack source evidence; it must be held before generation rather than sent to scoring gate as probe. Conversely, a score-qualified Story still requires complete planning traceability and independent review.

## Scope and dependency discipline

Record dependencies at level that changes delivery or verification: upstream data availability, external service behavior, identity or entitlement, asynchronous notification, audit recording, reporting consumption, migration, configuration, feature flag, release ordering, or operational support. State whether each dependency is confirmed, unavailable, assumed by explicit source, or requires clarification. Do not convert a named system into implementation contract unless source provides contract.

Where workflow crosses layers, split evidence statement by layer. A visible confirmation proves UI outcome; it does not prove persistence, integration delivery, notification dispatch, audit storage, or reporting availability. Candidate may include cross-layer behavior only where source establishes joined outcome. Otherwise, retain confirmed UI behavior and create focused dependency or clarification for unconfirmed layer. This protects against claiming end-to-end coverage from mockup or single ticket sentence.

For permissions, identify evidenced role, entry condition, allowed or denied action, visible result, and any specified audit or notification consequence. Do not infer role can view, edit, approve, delegate, or bypass state simply because role name is familiar. For data, separate displayed values, user-entered values, derived values, retained values, and transmitted values. For asynchronous behavior, separate request acceptance, queued work, completion, failure, retry, timeout, and user-visible status when evidence supports distinctions.

## Planning quality checks

Before a candidate enters generation, perform these deterministic checks in planning record:

1. Every explicit requirement has a disposition and none is hidden in narrative notes.
2. Each candidate has a single primary value outcome and does not combine unrelated outcomes merely because they share a screen or entity.
3. Candidate title describes outcome rather than implementation task, source filename, or broad programme label.
4. Requirements assigned to different actors, permissions, decision results, or independently releasable journeys have been evaluated for splitting.
5. Source gap is stated as gap, not converted to default error message, API rule, retention period, field format, or permission behavior.
6. Coverage matrix uses only defined evidence statuses and identifies unsupported layers explicitly.
7. Candidate Story can contain specific ACs without copying unverified source prose into promise.
8. Candidate-to-source links and candidate-to-dependency links use stable internal identifiers.

Record date or sequence of planning decision only as internal workflow evidence. Do not place internal source paths, source extracts, audit information, or planning labels in Jira-ready Story. Internal plan may contain details because it is not export artifact.

# Scoring transaction and persistence protocol

## Pre-invocation controls

Parent generation workflow owns score transaction. Immediately before sole invocation, verify current Story file is fully written and readable at exact workspace-relative path supplied to tool. Confirm extension is `.md` or `.markdown`, it is regular workspace file, and path has no absolute root, URI scheme, traversal segment, null byte, or ambiguous alias. Confirm candidate has not already advanced to Test Case or SPEC for same generation version. If downstream artifact is present, stop and reconcile generation state; do not score around invalid sequence.

Optional metadata is descriptive only. It may identify workflow mode, artifact kind, candidate slug, or ticket key when values contain no source content or sensitive data. It must not contain secrets, tokens, credentials, access headers, raw ticket text, raw attachment content, URLs, conversation identity, personal data not needed for workflow, or instruction designed to influence assessment. Tool input is file path, not copied Markdown body and not external location.

Make one invocation per generated or regenerated Story version. A regenerated version means Story text intentionally replaced after new evidence, correction, or re-decomposition; it is not reason to call tool twice for same version. Do not call for preview, score comparison, diagnostic experiment, confirmation, retry for higher result, or second representation. Cache hit is accepted outcome required invocation. `force_review` remains false; no workflow branch upgrades it merely because earlier response was cached, low, incomplete, or inconvenient.

## Verbatim record handling

On receipt, write complete returned JSON as designated `story-quality-score.json` before evaluating pass predicate or creating downstream files. Preserve bytes and structure as returned: do not pretty-print, minify, sort keys, append comment, redact field, add locally computed fields, extract summary as replacement, or overwrite response with later narrative. Planning trace may reference facts actually returned, but it is not score record.

If writing record fails, treat transaction as evaluation recovery condition even when tool returned qualifying response. Report writing failure with exact Story path and intended score-record path, retain no substitute local result, and prevent downstream creation. If partial file exists, do not treat it as persistence; preserve only according to safe internal recovery practices and make clear required complete record is unavailable. Never fabricate missing response from fields visible in log or UI.

When result contains audit identifiers, cache information, checksum, title, status, breakdown, dimensions, issues, warnings, strengths, or details, use those fields only as returned internal evidence. Their absence is not itself reason to invent values. Checksum mismatch with current Story means record is stale; hold candidate. Checksum absent cannot be reconstructed under different algorithm and cannot be represented as tool evidence.

## Deterministic gate handling

The only release predicate for this gate is `ok === true` and numeric `finalScore >= 76`. Numeric means actual numeric JSON value, not string that looks numeric, formatted label, rounded display value, or field embedded in prose. Do not derive score from dimensions or diagnostics. Do not use positive status to pass 75, and do not use favorable score to pass `ok: false`. No Story can borrow, aggregate, average, or inherit another Story's result.

For passing result, record internally that Story version satisfied gate, then create only that candidate's Test Case. Complete its title split validation before producing matching SPEC. A pass does not authorize another candidate, Jira preparation, approval request, or final completion by itself. All other generation and review gates remain mandatory.

For non-qualifying but valid response, preserve record and enter source-revision waiting state. Explain diagnostic evidence faithfully, without asking user to select number or alter audit record. For invalid, unavailable, or unpersistable responses, enter evaluation-recovery waiting state. State whether problem is path validation, tool availability, malformed response, missing required fields, invalid numeric value, or record persistence. In both cases, verify no Test Case or SPEC was created for candidate before requesting user decision.

## Resumption rules

When user supplies new or corrected evidence, update relevant evidence register, requirement rows, coverage matrix, and candidate decision before regenerating Story. Do not make cosmetic edit solely to obtain another assessment. If new evidence changes decomposition, retire affected candidate mapping internally and create resulting candidates with their own traces and single calls. Earlier records remain internal historical evidence but cannot be treated as current result for changed content.

When user requests technical retry after tool problem, verify Story version and path are still intended current version. If path or content changed while waiting, treat it as regenerated Story and follow one-call rule for that current version. If response was valid but below threshold, do not retry unchanged evidence merely because user wants different result; request or apply actual supported correction first. If user explicitly stops, record waiting state internally and do not claim candidate is complete, reviewed, Jira-ready, or exported.

## Internal trace record minimums

For each candidate, planning trace must make sequence auditable without exposing it to Jira: candidate identifier; Story path; source requirement IDs; readiness result; score-record path; gate decision; known currentness evidence; and returned identifiers or values only when present. It may include links to latest Test Case, SPEC, and manifest after they exist. It must not transform internal diagnostic material into user-facing acceptance criteria.

For every waiting state, retain precise affected artifact path, relevant line or range where known, source IDs or candidate ID, reason category, user question, and safe next step. A pathless message such as “the Story failed” is insufficient because multiple candidates can be active over lifecycle of request. A bare score is also insufficient because it does not identify content version or internal record that produced it.

This protocol preserves serial generation, source-first planning, audit-backed scoring, and downstream isolation. It deliberately does not authorize any replacement scoring logic, inferred source evidence, or Jira exposure of internal quality evidence.
