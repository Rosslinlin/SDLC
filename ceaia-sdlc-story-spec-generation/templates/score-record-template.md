# Story Quality Score — Attempt [NNN]

## Binding

- Candidate: [candidate ID]
- Story path: [complete workspace-relative STORY.md path]
- Story revision: [observed workflow revision]
- Scoring mode: Workspace Story (`relativePath`); for updates use this modified Story path, not the Jira ticket
- Verified primary SPEC Review Result / Result: [source header verdict, normally PASS]
- Submitted specFileStatus: [exact case-sensitive tool value, normally Pass]
- Submitted SPEC path: [complete workspace-relative primary <name>-spec.md path]
- SPEC file revision / content-review attempt: [observed values]
- Score attempt: [monotonic integer]
- Invocation reference: [actual returned review/audit/invocation ID, or Not returned]
- Cache hit: [true/false/not returned]
- Persistence mode: [verbatim-json-in-markdown/complete-object-json-in-markdown]
- Response read back: [true/false]

## Result

- Invocation OK: [true/false]
- Final status: [returned value or Not returned]
- Final score: [returned numeric value or Not returned]
- SDLC score gate: [PASS/FAIL/BLOCKED]
- Parser / AC recognition: [PASS or exact blocker]
- Declared Story AC count: [count]
- Returned AC count(s): [all returned count paths/values or Not returned]

## Human-readable diagnostics

- Summary: [brief returned summary or Not returned]
- Blocking critical issues / warnings / evidence gaps: [brief returned items relevant to the next action, or None; retain every original item in raw JSON]
- Parse errors: [exact blockers or None]
- Other returned section/dimension scores and diagnostics remain in the complete raw response below; do not calculate a replacement score or duplicate full structures here.

## Complete raw tool response

The fenced block below is the complete single JSON text response returned by `nodejs-base-mcp.score_requirement_markdown`. Do not edit, shorten, reorder, normalize or omit unknown fields.

```json
[complete raw JSON text]
```

<!-- Replace all placeholders. The binding records actual submitted inputs even if the service does not echo them. The readable sections are a view of the raw response, not a substitute for it. Never place this record in Jira content or attachments. -->
