# CEAIA SDLC Skill Bundle v4.0.0

发布日期：2026-09-21

## 版本定位

v4 以 v3 为基线，将最后的 Jira 写入阶段从 `ceaia-sdlc-only-jira-push-content` 替换为 `jira-createissue-helper` 的独立 SDLC gateway。活动链路为：

1. `ceaia-sdlc-story-spec-generation`
2. `ceaia-spec-review`
3. `jira-createissue-helper` → `references/sdlc-gateway.md`

旧 push Skill 未复制到 v4 包，仍完整保留在 v3 版本目录中，便于回滚。

## 主要改动

### 1. Helper 中新增独立 SDLC gateway

- 新增 `jira-createissue-helper/references/sdlc-gateway.md`。
- SDLC handoff 优先于 generic create/update/batch；失败时禁止降级绕过。
- gateway 校验评分、独立 review、全局覆盖、Test Case 标题检查、每个 SPEC 的 review header 回写和附件绑定。
- generic create、update、association、普通 batch 和 Test Case 业务流程未被改写为 SDLC 流程。

### 2. Jira wiki 数据组装

- Workspace 中的 `STORY.md` 保持不变。
- Jira-bound Description 是单独的 transport rendering。
- Markdown 标题按层级转换为 `h1.`—`h6.`，例如 `## Test` → `h2. Test`。
- 列表、代码块和 inline code 采用 Jira wiki 表示；转换仅改变表现语法，不得改写业务语义。
- preview 前检查代码块之外是否仍有 Markdown 标题；无法无损转换时阻塞导出。

### 3. 评分结果改为可读 Markdown

- 每次实际评分生成新的 `story-quality-score-attempt-<nnn>.md`。
- Markdown 顶部展示绑定、结果、AC/parser 状态和诊断摘要。
- 完整原始 JSON 响应保存在 fenced `json` block 中，未知字段也必须保留。
- 不再把新评分覆盖到同一个 `.json` 文件。

### 4. 输出最新版，中间记录保留

- `outputs/ceaia/...` 的 `STORY.md`、`TEST_CASE.md` 和 SPEC 始终是最新版，可被修复后的版本替换。
- `.ceaia-work/...` 中的 plan、score、review、post-repair verification 和 approval records 单调编号、不可覆盖。
- compact state / batch state 仍可更新，只保存指针、计数和状态，不复制完整文档正文。
- 不创建历史 Story/Test Case/SPEC 副本，避免十个 Story 时重复占用大量上下文。

### 5. 强化 SPEC review result 同步

- 独立 reviewer 仍然只读，不直接修改 SPEC。
- parent 在 substantive PASS 后逐个更新所有 intended SPEC 的 `Result` 和 `Reviewer Notes`。
- `Reviewer Notes` 记录当前 review attempt。
- 更新后必须立即读回完整 SPEC，确认 header 与最新 PASS 一致且 body 未变化。
- 首次 header write/read mismatch 只允许一次 targeted retry；再次失败进入 `WAITING_TOOL`，不得设置 `jiraReady=true`。
- helper gateway 再次检查这一状态，避免出现 review 已 PASS 但 SPEC 仍显示旧结果。

### 6. Helper 截图增量

- popup/selection UI 成为 projectName、projectKey、issueType 等允许输入的控制契约，不能用普通 chat 代替。
- batch flow 补载 project/issue-type validation。
- Test Case Jira-visible 字段统一使用 newline characters，禁止 literal `<br>` 及其 escaped variants。
- `common-field-assembly.md`、`common-guardrails.md` 和 preview validation 增加对应检查。

## 未修改或保留的范围

- `flow-create-issue.md`、`flow-update-issue.md`、`flow-associate-issue.md`、`common-tools-and-inputs.md`、`common-attachments.md` 保持 v3 字节不变。
- 原始 `flow-testcase-source-export.md` 实际是 WPS/OLE 二进制文档而不是纯文本 Markdown。根据“不修改别人的 Test Case 流程”的要求，v4 中保持其字节和 SHA-256 不变；跨流程的换行保护已放到共享文本规则中。该遗留文件是否能被纯文本平台读取，需要在平台测试中单独确认。
- 评分阈值仍为 `ok === true` 且 `finalScore >= 76`，并要求 AC/parser 完整性。
- 三轮自动修复预算、无进展提前停止和用户授权额外有限轮次逻辑保持不变。

## Task 使用前需要填写的值

`SDLC-workflow-v4.task.json` 中 helper 的平台 skill ID 使用占位值：

`REPLACE_WITH_PLATFORM_JIRA_CREATEISSUE_HELPER_SKILL_ID`

在平台上传/更新 `jira-createissue-helper` 后，用平台实际返回的 skill ID 替换该值。Skill name 已设置为 `jira-createissue-helper`，评分工具仍位于 tools 列表最后。
