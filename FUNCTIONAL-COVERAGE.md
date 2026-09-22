# v4 Functional Coverage

| 能力 | v4 归属 | 关键约束 |
| --- | --- | --- |
| 新 Story / 现有 Jira 更新 intake | generation | 每票独立 source，完整 baseline/attachment intake |
| Story 规划与拆分 | generation | evidence-backed、INVEST、全局 coverage |
| Story 评分 | generation + external tool | 每次保存编号 Markdown，完整 raw JSON，76 分门禁 |
| Test Case / SPEC 生成 | generation | score PASS 后生成；outputs 仅最新版 |
| 中间历史 | generation/review/helper | `.ceaia-work` 编号保存 plan/score/review/export-attempt |
| 独立 review | review | reviewer 只读；检查 SCORE/STRUCTURE/COVERAGE/STORY/TESTS/SPEC/UPDATE |
| SPEC review header | generation parent | PASS 后回写、完整复读、一次 targeted retry、未验证不得 jiraReady |
| Jira SDLC routing | helper SDLC gateway | 禁止 generic fallback |
| Jira wiki rendering | helper SDLC gateway | `##`→`h2.` 等，仅 transport copy |
| Jira metadata / project / issue type | helper common modules | 动态 popup、不得 chat 替代所需选择 |
| Jira final preview / preflight | helper SDLC gateway | `jira-preview.md` 当前版；完整 payload/附件复读后直接导出 |
| Jira routing state | generation + helper | `handoffType` 仅内部路由，禁止发送到 Jira MCP |
| Jira user defaults | helper SDLC gateway | 成功后更新 SDLC-only `jira-user-info.md`，下次复验并要求一次配置确认 |
| SDLC 默认完整链路 | generation + task | 没有 push/export 字样也在 review PASS 后自动进入 Jira create/update |
| Generic Jira rich text | helper common rendering | 非 Test Markdown 生成 Jira wiki transport copy；源文档不修改 |
| SDLC user defaults boundary | helper SDLC gateway | `jira-user-info.md` 仅 SDLC 读写；首次问 Epic，后续复验并确认 |
| Jira write | helper | `exportJiraByDynamicFields`，失败/partial/unknown 可恢复但不自动重试 |
| Generic helper flows | helper original modules | 仅增加非 Test rich-text transport rendering；原确认与业务路由不变 |
| Test Case flows | helper Test Case modules | 不读取 SDLC user info，不应用通用转换，专用 payload/换行规则不变 |

## Active package contents

- `ceaia-sdlc-story-spec-generation/`
- `ceaia-spec-review/`
- `jira-createissue-helper/`
- `SDLC-workflow-v4.task.json`

`ceaia-sdlc-only-jira-push-content` 不再是 v4 活动组件；回滚时使用 v3 包。
