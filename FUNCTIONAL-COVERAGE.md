# v4 Functional Coverage

| 能力 | v4 归属 | 关键约束 |
| --- | --- | --- |
| 新 Story / 现有 Jira 更新 intake | generation | 每票独立 source，完整 baseline/attachment intake |
| Story 规划与拆分 | generation | evidence-backed、INVEST、全局 coverage |
| Story 评分 | generation + external tool | 每次保存编号 Markdown，完整 raw JSON，76 分门禁 |
| Test Case / SPEC 生成 | generation | score PASS 后生成；outputs 仅最新版 |
| 中间历史 | generation/review/helper | `.ceaia-work` 编号保存 plan/score/review/approval |
| 独立 review | review | reviewer 只读；检查 SCORE/STRUCTURE/COVERAGE/STORY/TESTS/SPEC/UPDATE |
| SPEC review header | generation parent | PASS 后回写、完整复读、一次 targeted retry、未验证不得 jiraReady |
| Jira SDLC routing | helper SDLC gateway | 禁止 generic fallback |
| Jira wiki rendering | helper SDLC gateway | `##`→`h2.` 等，仅 transport copy |
| Jira metadata / project / issue type | helper common modules | 动态 popup、不得 chat 替代所需选择 |
| Jira preview / approval | helper SDLC gateway | `jira-preview.md` 当前版；`request_user_approval` 绑定精确 payload/bytes |
| Jira write | helper | `exportJiraByDynamicFields`，失败/partial/unknown 可恢复但不自动重试 |
| Generic helper flows | helper original modules | 不被 SDLC gateway 改写 |

## Active package contents

- `ceaia-sdlc-story-spec-generation/`
- `ceaia-spec-review/`
- `jira-createissue-helper/`
- `SDLC-workflow-v4.task.json`

`ceaia-sdlc-only-jira-push-content` 不再是 v4 活动组件；回滚时使用 v3 包。
