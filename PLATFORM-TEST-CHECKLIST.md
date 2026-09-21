# v4 Platform Test Checklist

## 安装前

- [ ] 上传三个活动 Skill，并记录平台返回的 skill IDs。
- [ ] 替换 task 中的 helper skill ID 占位值。
- [ ] 确认平台提供 `ask_user_question`、Jira MCP tools 和 `nodejs-base-mcp.score_requirement_markdown`；SDLC 路由不依赖审批工具。
- [ ] 确认平台能读取所有文本资源；单独检查遗留 WPS/OLE `flow-testcase-source-export.md`。

## 场景 A：新 Story 正常链路

- [ ] Story 只有一个 `## Acceptance Criteria`，AC 使用稳定编号和 Given/When/Then。
- [ ] 评分后产生 `story-quality-score-attempt-001.md`，可读摘要与 raw JSON 一致。
- [ ] score PASS 后才生成 Test Case 和 SPEC。
- [ ] review 产生 `review-attempt-001.md`，不是覆盖固定 `review.md`。
- [ ] PASS 后 SPEC header 显示 PASS 和正确 attempt，完整复读成功后才 `jiraReady=true`。
- [ ] Jira preview 的 Description 使用 `h2.` 等 Jira wiki 标题，不包含代码块外的 `## `。
- [ ] 最终 preview/read-back/preflight 通过后直接调用 `exportJiraByDynamicFields`，不出现额外批准等待。
- [ ] 外层参数、`dynamicFieldsJson`、`attachmentsJson` 和 `issuesJson` 中均不存在 `handoffType`。
- [ ] 批量 SDLC 使用 `issuesJson`，不发送未暴露的 `issues` 参数或 `requestJson` wrapper。

## 场景 B：评分或 review 修复

- [ ] Story 修复后 outputs 中 STORY 被替换，不产生历史 Story 副本。
- [ ] 新评分写入 attempt-002，attempt-001 保留。
- [ ] 新 review 写入新的编号文件，旧 review 保留。
- [ ] SPEC-only 修复不触发 Story 重写或不必要的重新评分。
- [ ] 三轮预算用尽或连续两轮无进展后停止自动编辑并请求明确决策。

## 场景 C：SPEC header 回写失败

- [ ] 首次 mismatch 只执行一次 header-only retry。
- [ ] 第二次 mismatch 进入 WAITING_TOOL。
- [ ] helper gateway 拒绝未验证的 SPEC，即使 review report 为 PASS。

## 场景 D：Jira wiki 转换

- [ ] `#` 至 `######` 分别转换为 `h1.` 至 `h6.`。
- [ ] code fence 内的 `#`/`##` 不转换。
- [ ] 有序/无序列表层级保持。
- [ ] AC IDs、Given/When/Then 和业务文案没有改写或重排。
- [ ] Workspace `STORY.md` 未因 Jira rendering 被修改。

## 场景 E：现有 Jira 更新

- [ ] `attachmentAction=none` 可完成 description-only update。
- [ ] 多个不同 replacement SPEC 保持独立 mapping，均有独立 review/header binding。
- [ ] preview 显示 original→replacement mapping。
- [ ] 第一个失败/partial/unknown 后暂停剩余更新，不重放已成功操作。

## 场景 F：Helper 通用流程回归

- [ ] 普通 create/update/association/batch 不进入 SDLC gateway。
- [ ] project/issue type 需要选择时使用 popup，而不是普通 chat。
- [ ] Test Case Jira-visible 换行使用 newline，不出现 `<br>` 或 escaped equivalents。

## 场景 G：首次请求与用户工作区复用

- [ ] 初次输入末尾包含 “push to Jira” 时，先运行 generation/score/review，不在 intake 阶段询问 project 或 Epic Link。
- [ ] 首次成功 SDLC 导出后，user workspace 的 `jira-user-info.md` 只有一个 CEAIA SDLC 默认配置区。
- [ ] 下一次 SDLC 导出先读取并验证保存的 source、project、Story type、Epic/Parent Link；有效时不重复询问。
- [ ] 保存值失效或用户明确变更时，只询问受影响字段并重新验证。
