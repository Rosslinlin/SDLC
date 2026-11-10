# Review Result

**Result**: Not Reviewed
**Reviewer Notes**: Independent review pending. After review, replace with `Attempt <N> — <brief evidence-backed notes>`.

---

# Feature Specification: [Story Title]

- **SPEC File Name**: `final-story-name-spec.md`
- **Branch**: [supplied branch; omit if unknown]
- **Author**: [supplied author; omit if unknown]
- **Date**: [actual date]
- **Requirement ID**: [supplied requirement ID; omit if unknown]
- **Parent Requirement ID**: [supplied parent ID; omit if unknown]
- **Status**: Draft
- **General feature requirements**: [approved supplied GitHub commit-specific general-spec URL; remove if unavailable]
- **Overall architecture design**: [approved supplied GitHub commit-specific architecture.md URL; remove if unavailable]
- **Market**: [supplied market; omit if unknown]
- **Channel**: [supplied channel; omit if unknown]
- **Customer Type**: [supplied customer type; omit if unknown]
- **Customer Segment**: [supplied customer segment; omit if unknown]

## Summary *(mandatory)*
[Supported business goal, value and scope.]

## User Flow Diagram
[For an evidenced user action or state transition, insert one nonempty fenced mermaid flowchart; simple flows still use a minimal diagram, not a prose substitute. Include only supported steps and branches. If no user flow applies, write Not applicable with a short factual reason. Missing material business facts follow the existing clarification path rather than being hidden as Not applicable.]

## Jira Story *(mandatory)*

[Insert the faithful Markdown business projection derived from the current story.json4jira. Its Story title starts at ### and its business sections at #### beneath this ## Jira Story section. Preserve every business section, AC and supported BDD detail; omit routing, identity, field IDs and transport controls.]

## Test Cases *(mandatory)*

[Insert exactly the current test-case.md executable table, including header/separator/rows. Omit tracking metadata.]

## Edge Cases
[Source-supported relevant boundary/error/dependency/permission outcomes, or explicit Not applicable with rationale.]

## Requirements *(mandatory)*

### Functional Requirements
- **FR-001**: [Specific supported capability, traceable to AC-001.]

### Key Entities
[Supported entities and attributes, or Not applicable with rationale.]

## Success Criteria *(mandatory)*

### Measurable Outcomes
- **SC-001**: [Observable measurable acceptance outcome, without invented targets.]

<!-- Replace placeholders and remove this comment.
Only Result and Reviewer Notes appear above the first standalone --- separator; Notes identify the actual review attempt. Valid verdicts: Not Reviewed, PASS, NEEDS_REVISION, NEEDS_HUMAN_CLARIFICATION, FINAL_WITH_UNRESOLVED.
SPEC File Name equals the actual saved <name>-spec.md basename. The parent synchronizes review fields and reads back the full file before scoring, without changing its reviewed business body.
For scoreUserStory, map actual Result PASS to exact specFileStatus Pass and use the actual specFilePath plus canonical story_json_workspace_path.
The Jira Story section is the JSON business projection, never a separate requirement source or raw upload-payload dump. Embed exactly the saved executable Test Case table.
In the existing production precheck, check the flow's mermaid fence/closing delimiter and nonempty flowchart, embedded heading levels, removed authoring placeholders and valid 11-column test tables. Preserve actual code/literals; do not add a separate format-review stage or report.
Retain supplied metadata and approved general-spec/architecture links; omit optional unknown values. Do not leak source registers, internal paths, raw responses, audit IDs, credentials or unresolved business promises.
Do not add Story Score or any numeric score to this file. Scores remain in the independent report/UI. Freeze SPEC and canonical JSON after scoring; any later change requires current scoring and a fresh content/upload confirmation.
-->
