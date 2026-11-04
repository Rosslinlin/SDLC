# Optional upstream assessment and backend project rules

Read only when the selected input carries assessment evidence or an actual backend response concerns scoring. This helper has no universal Story scoring prerequisite, no local score threshold and no automatic scoring fallback. Some projects export Stories without scoring. Other issue types do not acquire Story scoring or SPEC requirements.

## Preserve evidence when it exists

Use actual `scoreUserStory` calls or a known supplied result, not a model-written score flag or profile. Preserve complete response/error, exact JSON input path and observed revision, actual optional SPEC pair/participating files, and real returned identifiers/checksums/cache flags. A record file is not mandatory when equivalent actual call evidence is available; absence is not a global export blocker.

If reporting this file as currently assessed, verify that the actual JSON-path invocation and saved input revision match. The same path/ticket alone is insufficient. A direct Jira ticket/source assessment reads online content and does not establish assessment of a local revision. Do not score the manifest, use one item's score for another or treat a previous assessment as current after an edit.

Actual `ok=true` with a finite numeric `finalScore`, including 0/cache hits, is a completed assessment. Preserve genuine low scores, skipped/validation outcomes and unknown states honestly; never calculate a replacement number or claim a failure as a score. No score is invented when the project does not need one.

## Changes and exports

Reuse complete upstream JSON unchanged. If the user changes content/routing/attachment selection or a participating file, keep earlier assessment historical and do not claim its old input binding covers the revision. Prepare and confirm the new content under [submission](common-preview-confirm-export.md). Missing or stale assessment alone does not introduce a universal pre-export block; actual project/backend or explicit user requirements remain authoritative.

Do not invoke reviewer/scorer merely because export was requested. Scoring and existing-Jira score-only synchronization remain separate reviewer work. The content exporter passes only the selected file path; it does not add a score, SPEC arguments, ticket/source sync or old binding calls.

Story files always submit individually under [batch planning](flow-batch-export.md), even when scoring is not required. A non-Story manifest contains no Story children. Do not relabel a Story to obtain batch handling.

## Actual backend rejection and downstream evidence

The backend decides project-specific stored-score/currentness/threshold rules. If it rejects a missing/stale/low score, preserve its exact error and any actual write effects; report the required assessment as a precise next action, with the existing JSON ready for reviewer. Do not score, change type, alter fields or repeat export automatically to bypass that response. A later user-requested review/correction can supply current evidence and return to current-content confirmation as needed.

An actual JSON export may associate a stored review and enqueue score synchronization where applicable. Report only returned or contract-supported effects. An unscored/exempt project need not produce assessment/queue evidence; missing optional properties are not failure. A conflicting same-kind review identifier leaves that association unconfirmed without erasing the issue write. No extra sync/binding call follows content export.
