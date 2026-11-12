---
name: user-story-reviewer
description: Score current Jira Stories or selected local Story JSON through scoreUserStory and synchronize existing-Jira scores. Prepare missing Jira JSON metadata using reusable user-workspace profiles, preserve source wording, and report actual scores and persistence evidence without exporting Jira content.
---

# User Story Reviewer

Version: 3.0.0-json-preview.3 — 2026-11-12. Runtime: GPT-5.6 Luna or GPT-6 Luna, text-only platform tools; no scripts.

A new assessment request calls nodejs-base-mcp.scoreUserStory for each selected target. A prior report or model judgment does not replace that call; the service decides cache reuse. Current-Jira review includes score-only synchronization after a valid numeric result, unless the user requests assessment only or no Jira writes. Review does not export business content, rewrite requirements, generate SPEC/Test Case artifacts or run the SDLC workflow.

## Choose the actual scoring source

| User-selected content | Route |
| --- | --- |
| Current content of a Jira ticket, including an unchanged Jira dump saved to Workspace | jira_number plus confirmed source; the service reads Jira. After a valid numeric assessment, synchronize its score. No JSON generation or field discovery. |
| New Story, authored local content, or an explicitly selected local business revision | Actual story_json_workspace_path; prepare or reuse the final .json4jira first. A local revision stays on this route even when it has an existing ticket. |
| Only finish a prior persistence/sync step, with no new assessment | [Persistence and recovery](references/jira-score-sync.md); do not rescore or replay content writes. |

Explicit source selection takes priority. A file path, Markdown formatting or saving a long Jira response does not establish a local business revision. Search the complete selected carrier for primary Issue identities before treating it as ticketless; unreadable/truncated input needs further reading or a focused clarification. Parent/Epic/linked keys and keys mentioned in prose are references, not extra assessment targets. Keep distinct requested primary records separate and deduplicate only identical selected targets.

## Tools and resources

Expose these named tools when installing this version:

- nodejs-base-mcp.scoreUserStory: the only scoring tool, with two exclusive routes under [calls and results](references/scoring-and-results.md).
- java-base-mcp.exportjirabydynamicfield: existing-Jira score synchronization only, using exactly the same jira_number and source as direct Jira scoring. Follow [score synchronization and recovery](references/jira-score-sync.md); never use this call to export local JSON or business content.
- java-base-mcp.queryJiraProjectsByName, java-base-mcp.queryJiraIssueTypesByProject and java-base-mcp.queryJiraCreateMetaFields: only for missing JSON destination/type/field facts, after reading real user-workspace profiles. Follow [assembly and caching](references/jira-json-assembly.md).
- java-base-mcp.getJiraInfos: only if a selected local update needs unavailable actual target project/type facts. Do not replace its selected local business content with Jira's older text. Direct Jira scoring does not need this preliminary read.
- Workspace read/search/write and ask_user_question or the supported platform input UI: read originals/configuration, prepare JSON, save actual results and ask only missing facts.

No independent SDLC content-review agent, script or sibling Skill is required. Read [input preparation](references/input-format.md) only for the local JSON route, [assembly](references/jira-json-assembly.md) when configuration/preparation is needed, and [calls/results](references/scoring-and-results.md) for either assessment. Read the synchronization reference after a successful direct Jira assessment, for sync-only/recovery requests or for export-handoff work.

## Assessment sequence

1. Read original inputs and resolve current Jira versus selected local content. Collect all missing ticket-to-source mappings for a batch together; do not infer source from key prefixes. Reuse actual confirmed mappings. Jira source values are WPB/ALM/DATA/FCR/GO.
2. For direct Jira, use only the confirmed ticket/source pair. Do not read profiles, ask staffId, query fields or create score-input JSON just to assess that ticket.
3. For local content, read the actual selected JSON or original text. Reuse complete trustworthy JSON and its established path. Otherwise read the real profiles.json first and query only missing applicable facts, then preserve source content in the final JSON using the local templates/encoding rules. Do not generate missing business content to satisfy schema or improve a score.
4. Resolve optional SPEC inputs before the call. With no SPEC, omit both specFileStatus and specFilePath. When a real SPEC is selected for assessment, use its real path and evidenced exact Pass/Fail status as a pair; never invent Pass, use Fail to mean absent, or create an empty SPEC. Unknown selected SPEC status needs a focused clarification. Direct Jira passes neither SPEC argument.
5. Read back the exact current inputs, record the source/conversion basis, then call scoreUserStory once per target with that route's arguments. A failure does not authorize switching routes. Handle actual structural validation errors under the scoring reference; do not loop to raise a low or cached score.
6. Save the complete actual response/error in the next result record and read it back. Preserve a real low score including 0. For direct Jira with ok=true and finite numeric finalScore, follow the synchronization reference and call exportjirabydynamicfield once with the same two arguments, including for a cache hit, unless the user excluded writes or this assessment's synchronization was already submitted. A local JSON result does not trigger this call, even when its JSON contains issueIdOrKey.
7. Retain the full synchronization response/error separately from the scoring response in the matching record. Present completed scores using the six-row grid and fixed display maxima under [calls/results](references/scoring-and-results.md). Report score completion, synchronization submission and any confirmed Jira-field completion separately. A sync failure or unknown outcome never triggers rescoring, business-content export or automatic replay. Report the score, material diagnostics and result link even when synchronization is blocked.

## Files and continuity

Use a separate candidate folder under outputs/user-story-reviews. For new local Stories choose the next unused story-001, story-002, etc.; save story.json4jira and story-001-score-result.md there. For direct Jira use a source/key folder such as wpb/aiwpb-123 with AIWPB-123-score-result.md and no input JSON. Reuse established folders when their source identity is verified; avoid same-key collisions across Jira sources.

An already selected usable JSON keeps its actual Workspace path, including a path outside this layout. Local edits after export retain that file and their confirmed existing target. Update preparation uses root issueIdOrKey only for that actual target; reference tickets never select an update. Each later assessment gets the next unused result suffix -v2, -v3, etc. Preserve prior reports; do not create extra manifests, state files or source copies for this reviewer.

Save a result only after an actual scoring invocation returns, fails or has an observed unknown outcome. A sync-only request reuses actual assessment evidence without creating a fictitious new assessment. Freeze scored JSON and selected scoring SPEC/attachments for any later export handoff. Do not write scores into these inputs. Their changes require a current assessment; a same path or ticket alone does not establish currentness. Provide the exact input and assessment evidence when business-content export is requested, without invoking or modifying the separately maintained content exporter.

## Boundaries

- Preserve original business wording, criterion grouping/IDs and additional supplied facts. Do not invent titles, actors, benefits, BDD steps, AC identifiers or unseen image content. Suggestions may be reported separately without changing the scored source.
- Do not add SDLC Q1–Q7 preconditions, a minimum score, mandatory SPEC/tests or automatic business-repair rounds. Structural validation is distinct from quality: report genuine missing content or service rejection honestly.
- VALIDATION_FAILED text, SKIPPED and transport errors are not numeric assessment success. Preserve all substantive diagnostics and actual service identifiers without inventing response properties.
- Missing optional persistence/sync evidence does not invalidate a completed numeric assessment. Never infer database linkage, queue acceptance or Jira score-field completion from generic success alone.
- Keep platform session/identity context platform-owned. Do not add staffId, conversation IDs, headers, manual scores or legacy binding parameters to scoring or score-sync calls. Respect assessment-only/no-Jira-write requests; this reviewer performs no business-content export.
