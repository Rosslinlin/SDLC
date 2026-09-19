# <Story Title>

## Summary

[Concise Jira-ready summary of the independently testable user value delivered by this single Story.]

## User Story

As a <role>, I want <goal>, so that <benefit>.

## Business Context

[Why this story matters to the user, business, journey, or operational outcome.]

## Scope

[In-scope behavior for this single Story. Keep this granular and independently testable. A Story should not combine unrelated journeys, screen states, actions, decisions, or error behaviors when those can be tested and delivered independently.]

## Acceptance Criteria and BDD Scenarios

<!--
Combined Acceptance Criteria and BDD rules:
- Assign every acceptance criterion a stable ID: AC-001, AC-002, AC-003, ...
- Use Given/When/Then wording.
- Each AC must be independently testable.
- ACs must cover happy path, negative/error, boundary, dependency, and permission behavior when relevant.
- Include enough scenario detail under each AC for execution.
- Do not add criteria for behavior unsupported by source requirements.
- Do not omit explicit in-scope source requirements; split into another Story when needed.
-->

- **AC-001**: Given [initial state], when [action/event], then [verifiable expected outcome].
- **AC-002**: Given [initial state], when [action/event], then [verifiable expected outcome].

## Attachments

- SPEC: `<matching-story-named-spec-file-name>.md`

<!--
Jira export rule:
- STORY.md becomes the Jira Story description.
- The Jira-facing SPEC attachment reference contains the SPEC filename only. Do not include `outputs/`, a workspace directory, an absolute path, or a URL.
- The matching story-named SPEC is the Jira attachment.
- Standalone TEST_CASE.md is a user-visible workspace deliverable and must not be referenced here as a Jira-facing attachment.
-->
