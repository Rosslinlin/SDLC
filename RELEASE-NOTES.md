# CEAIA SDLC Skill Bundle v5.3

更新日期：2026-10-27。目标运行模型：GPT-6 Luna。

新增独立 `ceaia-sdlc-jira-export`，仅处理已通过 SDLC 门禁的 Story 创建、更新与批量导出，保留 SPEC 附件及首次 Jira 成功后的逐票五字段数据库评分关联。原 `jira-createissue-helper/` 与独立 `user-story-reviewer/` 原样保留。Task 和 generation handoff 已接入新 exporter；平台导入前须填写新 skill 的实际 ID。

字段配置和默认值成功后保存到真实用户工作区 `ceaia-sdlc-jira/profiles.json`；完整命中跨 session 跳过项目/type/metadata query，不按 TTL 刷新。每次确认实际目标、Ticket Number、Epic/Parent 与字段值。Update 复用生成阶段的 baseline 和完整附件清单，不重复远端 intake。首次 Epic/no-Epic、76 分、独立 PASS、SPEC sync、全局覆盖、内容保真、附件替换顺序和失败恢复门禁保留。

本次只改导出专用化、配置复用及必要接入，不改生成候选串行/阶段依赖或评分阈值。静态和离线验证不能代替 GPT-6 Luna 平台上的真实 Jira/附件/数据库关联回归；实测按 PLATFORM-TEST-CHECKLIST.md 执行。

---

# CEAIA SDLC Skill Bundle v5.2

更新日期：2026-10-23


2026-10-28 小改动：SPEC 的 Review Result 区新增第三字段 `Story Score`，显示当前 Story 的真实评分（`<number> / 100`）；首次评分前显示 `Not scored`。生成阶段负责后置回写、全文读回和必要的正常缓存 metadata 绑定；业务正文、76 分门槛、导出工具参数及逐票数据库关联不变。
本次将 SDLC Task 从约 24 KB 精简至约 12 KB，保留四阶段调度、关键门禁及原有 Skill/工具配置。执行细节仍由各 Skill 负责；不改变 Story、Test Case、SPEC、审查和 Jira 功能。评分工具的 `specFileStatus` 只接受大小写严格的 `Pass`/`Fail`。SPEC 审查结果仍记录 `PASS`，在评分调用边界映射为 `Pass`；非 PASS 审查不会进入评分。普通 Jira 和 Test Case 导出规则未改。

## 历史发布说明：v5.1

本次调整 SDLC Task 与三个活动 Skill 的顺序：先生成 Story、Test Case、SPEC，再进行独立内容审查；同步并读回 SPEC `Review Result` / `Result` 后，使用 Workspace `STORY.md` 路径及主 SPEC 状态/路径调用更新后的评分工具。新建与更新均走 Workspace 评分，只有最终真实分数通过才进入 Jira。工具正常复用缓存分数时仍保存本次 SPEC 状态/路径，后续五字段 Jira 绑定关联本次最终通过的评分记录。数据库现阶段只要求最终 SPEC 状态。独立 `user-story-reviewer` 的 Jira 直评分路由保持独立。

独立审查报告中的 SCORE 改为 DEFERRED；内容 PASS 不直接代表 Jira 可写。评分或审查失败均按受影响文件局部修复，修复后重新执行必要内容审查与最终评分，共用原三轮预算。评分记录的人类摘要缩短，完整原始 JSON 仍保留。SDLC 导出只读取当前操作相关的通用说明，Task 更新逐票执行；首次导出调用已成功处理的附件不再次上传。此次规则按 GPT-6 Luna 后续运行保持简明的阶段边界，未改变评分阈值及原 Jira 字段映射。

本次纯文本审计将此前误以 `.md` 命名的 WPS/OLE 二进制 Test Case 引用改为真正的 Markdown，按原有共享规则写明 individual/single 两种导出形态及字段保真要求。修正通用 Helper 文档中未暴露的 `issues` 参数、Test Details JSON 字符串类型、SDLC 与普通流程的确认边界，以及附件示例文件名。Task 中 Jira Helper 的平台 skill ID 仍是安装时填写的配置值，不是运行脚本。

## 历史发布说明：v5.0.0

发布日期：2026-10-06

## 补充：独立 Story Reviewer 使用 Jira 直评分

`user-story-reviewer` 现在按 Ticket Number 路由：任何带 Ticket Number 的 Story 直接使用 `jira_number` 与用户明确提供的 `source` 调用评分工具；多条 Story 缺少 source 时一次性收集，不读取 `jira-user-info.md`，也不从 Ticket 前缀推断。Jira 模式不再生成评分输入 Markdown，不再调用 Jira 读取或独立数据库回写工具；评分工具自行读取 Jira、保存结果并关联 Ticket。只有没有 Ticket Number 的输入继续使用 Workspace Markdown 模式。暂不传递 `specFileStatus` 与 `specFilePath`。

## 补充：统一 SPEC 文件名

所有新生成的 SPEC，包括不上传的更新用本地 SPEC，都必须使用与 Story/业务功能对应的 `<name>-spec.md`。生成阶段不再允许 `SPEC.md` 或普通 `<name>.md` 的文件名例外；多个不同替换 SPEC 使用不同的业务功能名称。独立审查核对实际文件、SPEC 内 File Name、Story/Test Case 引用、状态和更新映射；最终对话从实际 Workspace 文件读回并显示相同名称。此次未修改任务 JSON、`VERSION.json` 或 `jira-createissue-helper`。

## v5：Jira Ticket 与 Story 评分记录关联

在 v4.2 的 Jira create/update 调用及要求的附件操作确认成功、取得该项返回的 Ticket Key 后，`jira-createissue-helper` 的 SDLC gateway 才对该项执行第二次 `exportJiraByDynamicFields` 调用。第二次调用恰好使用五个真实非空字段：`staffId`、`almType`、`issueIdOrKey`（返回的 Ticket Key）、`conversationId`、`markdownReviewRelativePath`（本次 PASS 评分所用的 `STORY.md` 工作区相对路径）。第一次调用不带 `markdownReviewRelativePath`；第二次调用不带 Jira 内容、附件或批量字段。

批量 Jira 返回按每项结果和路径分别关联。Jira 写入、附件、`markdownReview` 绑定结果分别留痕和报告；绑定失败或未知时保留已成功的 Ticket Key，不重建 Ticket、不重放已成功的 Jira 写入。所有绑定完成前不宣布整条流程成功。普通 Jira 和 Test Case 路由保持 v4.2 行为。

v5 的 task 文件是 `SDLC-workflow-v5.task.json`。其 helper skill ID 仍须替换为平台上传后返回的实际 ID。

## 继承的 v4.2 行为

v4 以 v3 为基线，将最后的 Jira 写入阶段从 `ceaia-sdlc-only-jira-push-content` 替换为 `jira-createissue-helper` 的独立 SDLC gateway；v5 保留这条链路并增加上述第二次关联调用。活动链路为：

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
- `.ceaia-work/...` 中的 plan、score、review、post-repair verification 和 Jira export-attempt records 单调编号、不可覆盖。
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

### 7. v4.1 直接导出与路由修正

- 删除 SDLC 对额外审批工具的依赖；最终 preview、payload read-back 和附件 preflight 通过后直接调用 `exportJiraByDynamicFields`。
- `handoffType=ceaia-sdlc-validated` 仅作为内部路由状态，禁止进入任何 Jira MCP 参数或序列化字段。
- SDLC 批量导出使用工具实际暴露的 `issuesJson`；所有参数直接传递，不使用 `requestJson` wrapper。
- 项目、issue type 和 metadata 查询参数与当前工具契约对齐。
- `jira-user-info.md` 增加 CEAIA SDLC 默认配置，可复用已验证的 source、project、Story type、Epic Link 和 Parent Link。
- 当时的 v4.1 顺序是 generation → score → review；当前 v5.1 顺序以本文顶部和 Task 为准。

### 8. v4.2 默认全流程、通用文本转换与 SDLC 用户配置

- SDLC 默认继续到 Jira create/update；初始输入没有 push/export 字样也不会在 review PASS 后停止。只有用户明确要求 preparation-only/no-Jira、明确停止或存在阻塞时才不进入 Jira 阶段。
- 新增 `common-jira-text-rendering.md`，供普通非 Test create/update/batch 和 SDLC transport rendering 使用。Workspace 源文件不修改，preview 展示即将发送的 Jira wiki 文本。
- Test Case 专用路由、Test issue item、表格、步骤、数据和预期结果明确排除在通用转换之外；`flow-testcase-source-export.md` 保持字节不变。
- `jira-user-info.md` 改为 SDLC 专属。普通 create/update/association/batch/Test Case 不读取、不创建、也不更新该文件。
- 首次 SDLC create 在 Jira 阶段主动询问并验证 Epic Link，或记录用户明确选择无 Epic；不会在 generation intake 提前询问。
- 后续 SDLC 运行会先重新验证保存的 source/project/Story type/Epic/Parent，再要求一次“使用这些设置/修改设置”的配置确认。该确认不是最终写入审批，最终 preview/preflight 后仍直接导出。

## 未修改或保留的范围

- v4 阶段 `flow-create-issue.md`、`flow-update-issue.md` 和 `flow-batch-export.md` 主要接入非 Test rich-text transport rendering；它们的业务路由、metadata、preview/confirmation 和写入规则在当前版本继续保留。v5.1 另修正旧工具参数示例与文本引用格式。
- v4 时 `flow-testcase-source-export.md` 实际是 WPS/OLE 二进制文档，曾为避免改变旧流程而保留原字节；v5.1 已将它替换为纯文本 Markdown，并依据现有通用 Test Case 规则明确两种 payload 形态。平台仍需按清单做真实导出回归测试。
- 评分阈值仍为 `ok === true` 且 `finalScore >= 76`，并要求 AC/parser 完整性。
- 三轮自动修复预算、无进展提前停止和用户授权额外有限轮次逻辑保持不变。

## Task 使用前需要填写的值

`SDLC-workflow-v5.task.json` 中 helper 的平台 skill ID 使用占位值：

`REPLACE_WITH_PLATFORM_JIRA_CREATEISSUE_HELPER_SKILL_ID`

在平台上传/更新 `jira-createissue-helper` 后，用平台实际返回的 skill ID 替换该值。Skill name 已设置为 `jira-createissue-helper`，评分工具仍位于 tools 列表最后。
