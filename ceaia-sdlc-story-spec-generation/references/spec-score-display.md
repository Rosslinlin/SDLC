# Story score in the SPEC header

Generation owns this post-score display step. Read it after saving the actual score response and before marking Jira readiness. The score is visible only in the SPEC header, not in embedded Story, tests, Jira Description/AC or other issue fields. Existing review, parser and 76-point export gates remain unchanged.

## Header and source

Keep the first two Review Result fields in their existing order, followed by the new display field, all before the first standalone `---`:

```markdown
# Review Result

**Result**: PASS
**Reviewer Notes**: Attempt 1 — Content review passed.
**Story Score**: 82 / 100

---
```

This is a structural example, not an actual score. A newly generated SPEC uses the literal `Not scored` until a real numeric assessment is available. `Not scored` is allowed review metadata before scoring, not a missing business requirement. Do not invent zero, copy a sibling's score, derive a total from sub-scores, or round the returned value.

Use only `finalScore` from a persisted complete response for this exact candidate/current `STORY.md` revision, with `ok === true` and a finite JSON number in 0–100. Display a valid low score honestly while keeping the export gate blocked. A missing/failed/non-numeric initial result leaves `Not scored`; it never becomes a fabricated number. Parser/AC errors still block export even when a numeric score is displayed.

Every current primary/additional SPEC for the same Story displays that Story's score. Different Stories retain their own score/record/path bindings. Keep raw JSON, record paths, audit/invocation IDs, checksums and diagnostic details internal. The displayed numeric total is the sole score-information exception allowed in the SPEC.

## Finalize without a score/write loop

1. Save/read back the complete initial score record under the normal scoring contract. Before any header write, keep readiness false and invalidate affected export bindings. Set `specScoreDisplay.status=pending`.
2. Write only a changed `Story Score` value in each current SPEC. Keep Result, Reviewer Notes, everything after `---`, embedded Story and tests unchanged. Read back the complete saved files and confirm the exact value against the persisted response. Advance only each edited SPEC's `fileRevision`; do not advance `bodyRevision` or rewrite Story. This derived header update needs no new substantive review or content-repair round. Retain the unchanged content review, and refresh the successful specHeaderSync read-back binding to the observed final fileRevision after checking Result/Notes/body. Any valid numeric display, including a low/parser-blocked assessment, is now `specScoreDisplay.status=verified` with its exact value, Story revision, source record and verified paths; this verifies the display only. Keep `metadataFinalized=false` until any required passing-score binding is complete.
3. For a passing, parser-valid score, if the primary SPEC's `fileRevision` changed, make **one normal metadata-bearing scoring invocation** with the unchanged Story `relativePath`, exact `specFileStatus: Pass` and unchanged primary `specFilePath`. Omit `force_review` and metadata. The documented normal cache can reuse the Story assessment while saving the final SPEC metadata. Record the actual new input revision and complete response in a new numbered attempt; never rewrite the earlier attempt's inputs or raw JSON. Keep Story and SPEC unchanged during this call. If the primary revision and score metadata were already current, skip this invocation.
4. Verify the latest result's numeric score equals the displayed value, all existing score/parser/content gates pass, and its actual recorded primary SPEC revision matches the final file. Keep `specScoreDisplay.status=verified`, update its source to the latest score record and final primary file revision, and set `metadataFinalized=true`. Only then, with all existing gates passing, may normal readiness be set. Do not rewrite an unchanged score header after this invocation, and do not score again merely to re-display the same value.
5. If the metadata invocation returns a different valid numeric score, preserve both results and allow at most **one** header correction plus normal metadata check. If the next result still disagrees, or currentness/outcome cannot be established, mark the mismatched display pending and metadataFinalized=false, then use bounded technical recovery with readiness false; do not loop, force a new assessment or alter business content to obtain a matching score. A low/invalid/failed result follows the existing blocked-score policy rather than another automatic score attempt.

For a valid low or parser-blocked initial score, update/read back and verify the numeric display but do not run a metadata-only call for a failing Story; metadataFinalized stays false and readiness stays false. This display exception does not make the failed score current/passing or waive any gate. A later supported Story repair follows the normal review/scoring sequence. On any Story-content revision, invalidate this display and reset it to `Not scored` in dependent SPECs before reusing them; never present the old Story's score as current. An interrupted header write or failed read-back stays pending and uses the existing bounded header recovery.

On resume, reuse any verified unchanged display that still matches its persisted numeric source and current Story revision. A low/parser-blocked display is a no-op even when metadataFinalized=false: retain the failed-score gate and do not call the scorer merely to refresh its display metadata. For a passing score, require the final score/SPEC binding and metadataFinalized=true before readiness; only its genuinely stale binding uses step 3. This feature adds no Jira tool parameters, no score field to issue payloads, and no changes to either export skill or its mandatory post-write database binding.
