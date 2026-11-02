# CEAIA SDLC v6.1.0-preview.1 — JSON4Jira flow

日期：2026-11-02。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。运行目标仍为平台 GPT-5.6 Luna / GPT-6 Luna，纯文本执行。本预览按新接口重构活动三 Skill，不表示后端集成或两种模型已完成实测。

- 每候选的最终 `story.json4jira` 成为唯一 Story 业务源及提交文件。取消独立 `STORY.md`，SPEC 中 Story 投影来自 JSON；`TEST_CASE.md` 保留。
- 项目、Story 类型、字段 metadata 查询和跨会话配置复用前移至 generation，在最终 JSON 写成前完成。成功后保存已确认 profile，export 不再另组装一套提交字段。
- 评分改用 `nodejs-base-mcp.scoreUserStory`，仅传 `story_json_workspace_path`、`specFileStatus`（精确 `Pass` / `Fail`）和 `specFilePath`。完整响应与真实输入/标识保留；主链内容 PASS 不代替真实评分及持久化。
- 分数不写 SPEC，取消旧显示字段及仅为显示引起的 S2。导出工具仍为 `java-base-mcp.exportJiraByDynamicFields`，但**仅传 `story_json_workspace_path`**；评分与导出使用同一候选最终 JSON 路径。
- 真实评分通过后，先展示当前业务内容、目标与附件操作的前端 HTML 预览，用户明确确认当前内容后才导出。内容变化使旧评分/确认失效，必须重新完成相应步骤。
- 后端导出负责校验 score checksum/threshold、写入 Jira 与所需附件、回填数据库关联并入异步队列。前端不再执行旧五字段 B；分别记录 issue、attachments、linkage、queue、async sync 真实证据，不能把普通 success 或入队当作数据库/Jira UI 分数全部完成。
- 批量仍为每候选一个 JSON，默认逐票调用，不使用旧 `issuesJson`。外部未知/部分结果先核对，不能为修复关联或队列状态重建已成功票据。
- Q1–Q7 标准保持，审查对象更新为 JSON 业务内容与 SPEC 投影；review/score 共享两批内容修复、机械 cause 一次的规则保留。
- 更新 `none` / `replace` 业务语义保留，但 live schema 未暴露 target/replace 时明确 `WAITING_TOOL`。不猜新字段、不改成 create、不上传 score-only SPEC；预览版不冒称更新后端已集成。
- `jira-createissue-helper`、`user-story-reviewer`、`review-requirements` 三个独立包完全不改，不为旧工具所有路由新增兼容代码或脚本。

Task 兼容文件名仍为 `SDLC-workflow-v5.task.json`，显示名改为 `SDLC-workflow-v6.1 JSON preview`；schema 与原 10 工具顺序保留，最后评分工具 ID 替换为 `nodejs-base-mcp.scoreUserStory`。现有 generation/review ID 与 exporter 占位仍须核对实际安装结果，不能伪造平台 ID。

当前行为以 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)、[评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 与 [导出协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) 为准。新接口平台结果在 [测试清单](PLATFORM-TEST-CHECKLIST.md) 留空等待实测；旧 v6 本地、旧 S1–S2–A–B 验证仅为历史，不适用于新调用形态。

---

# 历史发布记录：v6.0 及更早版本（不作为 v6.1 运行指令）

以下原文包含旧工具、Markdown Story、SPEC 分数显示、S2 和五字段 B 等当时协议，均不适用于当前 JSON 主链；只保留版本历史。其“当前”“默认”“本次”等词仅指当时版本。

# CEAIA SDLC v6.0.0

发布日期：2026-10-31。运行目标：平台 GPT-5.6 Luna / GPT-6 Luna；纯文本执行。

本次重构保留三个活动 Skill，将重复的内容标准、编排和工具协议分开：

- generation 在写作前必读固定 Q1–Q7，review 包内携带同文副本。review 独立核查原来源与当前内容，保留遗漏/编造/可验收性/测试/SPEC/更新保留检查；移除SCORE=DEFERRED、标题禁or及预算/readiness等兼容门禁。
- 模板布局、`*-spec.md`、11列测试表和镜像引用集中在生产检查；真实内容或引用不一致仍须修复。删除已归并的独立planning-and-scoring、business-planning-checklist、validation资源。
- Task与普通对话遵循同一编排：每候选串行准备至review/score，保留阻塞项；全部选定候选及整批原始覆盖通过后才导出。默认全流程、显式preparation-only和阶段调用边界保留。
- review/score共用的内容修复由一批调整为两批，无进展提前停止；每个纯机械原因另限一次，变更失效规则不豁免。续跑保留计数，实际新证据或明确有限授权按契约处理；不改变外部操作各自的恢复规则。
- 真实S1评分、完整响应、76分/parser门槛、SPEC分数显示与必要S2最终元数据保存全部保留；不以共享标准保证后端高分，不以缓存命中省略持久化。
- Jira A及所需附件成功后逐票五字段B关联全部保留；分开记录外部结果，未知先核对，B失败不重放A。跨会话profile、设置确认、AC分区、wiki转换及附件替换顺序保留。
- 当前更新主链仍为none/replace；review逐SPEC记录保留义务。独立helper、user-story-reviewer及review-requirements原文件不改；legacy仅在明确选择时使用。
- 升级旧状态保留全部版本、计数、评分元数据和已知/未知远端操作结果，不通过重放调用迁移。

兼容文件路径仍为 `SDLC-workflow-v5.task.json`，显示名为v6；Task schema、10工具列表及score最后的位置保留，现有generation/review ID与exporter占位值仍须按实际平台核对。不在Task添加未经平台确认的模型参数，也不表示已完成平台安装。

当前行为以 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)、[质量标准](ceaia-sdlc-story-spec-generation/references/quality-contract.md)、[评分协议](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 和 [Jira A/B协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) 为准。文本结构/链接检查不代表真实平台回归；两种模型及Jira/附件/数据库结果须填写 [平台测试清单](PLATFORM-TEST-CHECKLIST.md)。

---

# 历史发布记录（不作为v6运行指令）

以下保留原发布文本，其中“当前”“默认”“本次”等词仅指当时版本。旧修复预算、旧review字段、旧helper路由与评分来源说明可能已被后续修正；执行一律以本文顶部v6和活动Skill为准。

# CEAIA SDLC Skill Bundle v5.3

2026-10-30：统一到 `main`。

- 默认 SDLC 链路为 `ceaia-sdlc-story-spec-generation` → `ceaia-spec-review` → `ceaia-sdlc-jira-export`；Task 同步采用新导出 Skill，字段配置复用、SPEC 附件及逐票五字段数据库关联保留。
- 合入原实验分支最新的单轮自动修复、SPEC 分数显示及问题澄清/绑定答案续跑规则。
- 保留合并前 `main` 的 `user-story-reviewer` 与 `jira-createissue-helper` 全部修复，包括长/批量 Jira 输入来源路由、同会话本地续改、实际评分模式决定四/五字段及漏关联恢复。通用 Jira/Test Case 功能不迁移到新 exporter。
- helper 的主入口及旧 gateway 增加当前/旧版分流：Task 和普通对话的当前 SDLC handoff 统一交给新 exporter；旧 gateway 仅供明确选择的旧版兼容流程，通用及评分绑定业务资源保持原 main 内容。
- 新增独立 `review-requirements`，仅含 `SKILL.md` 和 `templates/review-report-template.md`，不加入 SDLC Task；按纯文本平台适配，不执行脚本、命令或遥测，PR 评论仅按用户明确请求发布。
- 本次完成静态合并验证；真实 GPT-6 Luna、Jira、附件和数据库集成仍按测试清单验证，不把离线检查当作平台实测。

2026-10-30 内容澄清与续跑（已合入 main）：review/最终评分阻塞时，生成方在聊天区展示具体文件、条目、当前内容、失败原因和所需事实；ask_user_question 弹窗仅保留唯一简短定位、简短原因及具体问题。补充信息与当前问题绑定，充分回答后更新受影响 Story/Test Case/SPEC 并重新独立审查、评分；明确的 AI 编写错误只询问有限追加修复决定，不要求用户重复提供已知需求。保留单轮预算、所有质量门禁与导出/数据库回写。

2026-10-29 单轮修复调整（已合入 main）：每个 candidate / evidenceRevision 的默认自动修复由三轮降为一轮，review 与评分问题共用；初审后最多一个集中修复批次及受影响内容复审。仍需修改时立即暂停并返回现有问题，不再额外启动诊断 reviewer。续跑保留已消耗额度；额外修复仅来自真实新增实质证据/决策或用户明确的有限加轮授权。独立审查、76 分、SPEC 分数显示、工具恢复及导出/逐票数据库关联均保留，两个 exporter 目录未改。

v5.3 导出专用化最初实现于 2026-10-27，本次统一到 main 的日期为 2026-10-30。目标运行模型：GPT-6 Luna。

新增独立 `ceaia-sdlc-jira-export`，仅处理已通过 SDLC 门禁的 Story 创建、更新与批量导出，保留 SPEC 附件及首次 Jira 成功后的逐票五字段数据库评分关联。保留合并前 main 的 `jira-createissue-helper/` 与独立 `user-story-reviewer/` 最新修复。Task 和 generation handoff 已接入新 exporter；平台导入前须填写新 skill 的实际 ID。

字段配置和默认值成功后保存到真实用户工作区 `ceaia-sdlc-jira/profiles.json`；完整命中跨 session 跳过项目/type/metadata query，不按 TTL 刷新。每次确认实际目标、Ticket Number、Epic/Parent 与字段值。Update 复用生成阶段的 baseline 和完整附件清单，不重复远端 intake。首次 Epic/no-Epic、76 分、独立 PASS、SPEC sync、全局覆盖、内容保真、附件替换顺序和失败恢复门禁保留。

v5.3 最初的导出专用化改动保留生成候选串行/阶段依赖和评分阈值；当前 main 还包含上述单轮修复与澄清续跑规则。静态和离线验证不能代替 GPT-6 Luna 平台上的真实 Jira/附件/数据库关联回归；实测按 PLATFORM-TEST-CHECKLIST.md 执行。

---

# 历史发布说明：CEAIA SDLC Skill Bundle v5.2

以下记录当时的实现；其中三轮预算、旧 helper 接入及旧 reviewer 路由均以当前 v5.3 和对应 Skill 为准，不能作为当前运行指令。

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
- 历史 v5.2 使用三轮自动修复；当前 v5.3 改为每个 candidate / evidenceRevision 共用一轮，保留无进展停止及用户明确授权有限追加轮次。

## 当前 Task 使用前需要填写的值

当前 `SDLC-workflow-v5.task.json` 中 SDLC exporter 的平台 skill ID 使用占位值：

`REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID`

在平台上传/更新 `ceaia-sdlc-jira-export` 后，用平台实际返回的 skill ID 替换该值。Skill name 已设置为 `ceaia-sdlc-jira-export`，评分工具仍位于 tools 列表最后。`jira-createissue-helper`、`user-story-reviewer` 与 `review-requirements` 独立安装，不加入当前 SDLC Task。
