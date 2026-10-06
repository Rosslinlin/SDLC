# Story Quality Score — Attempt [NNN]

## Binding

- Candidate: [candidate ID]
- Story path: [complete workspace-relative STORY.md path]
- Story revision: [observed workflow revision]
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

- Summary: [returned summary or Not returned]
- Critical issues: [returned items or None]
- Warnings: [returned items or None]
- Evidence gaps: [returned items or None]
- Strengths: [returned items or None]
- Parse errors: [returned items or None]
- Section/dimension scores: [render all returned score structures without calculating a replacement score]

## Complete raw tool response

The fenced block below is the complete single JSON text response returned by `nodejs-base-mcp.score_requirement_markdown`. Do not edit, shorten, reorder, normalize or omit unknown fields.

```json
[complete raw JSON text]
```

<!-- Replace all placeholders. The readable sections are a view of the raw response, not a substitute for it. Never place this record in Jira content or attachments. -->
