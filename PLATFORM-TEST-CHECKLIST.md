# v6.1 JSON4Jira flow 平台测试清单 — preview

版本 `6.1.0-preview.3`，日期 `2026-11-03`，分支 `codex/sdlc-json4jira-flow`，未合入 `main`。分别在平台 GPT-5.6 Luna 与 GPT-6 Luna 验证新路径接口。下列结果栏留空表示尚未记录，不能把旧 v6 本地/旧接口检查、mock 推演或普通 tool success 当作新接口与数据库实测。

## 环境与执行记录

| 模型 | 执行日期 | 平台/Skill版本与实际ID | 会话/测试数据 | 总体结果 | 证据位置 |
| --- | --- | --- | --- | --- | --- |
| GPT-5.6 Luna | | | | | |
| GPT-6 Luna | | | | | |

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| J01 | 三个包独立安装、资源可读，generation/review 的 Q1–Q7 同文；实际 skill IDs 已核对，exporter 占位替换。兼容 Task 文件显示 `SDLC-workflow-v6.1 JSON preview`，schema 与 10 工具顺序保留，最后一项为 scoreUserStory；模型由平台配置。 | | |
| J02 | 所有用户未写 push/export 也默认继续 Jira-backed 完整流程，入口不提供路线选择；缺信息只补缺项，不推荐 preparation-only。仅用户主动明确只准备/不导出时遵从；阶段调用不越界。默认流程最终仍须评分后的当前内容 HTML 预览及用户明确确认。 | | |
| J03 | 项目/type/meta 查询在生成最终 JSON 前完成；只使用 live schema 真实字段、类型、选项和必要值，不把旧 direct 参数直接猜成 JSON 字段。缓存命中复用，缺项/显式变更才查相应部分。 | | |
| J04 | 跨会话配置由 generation 读取；票据及要求附件确认成功后仅将上游已确认快照保存到真实用户工作区 profiles.json；票据/要求附件失败、partial 或 unknown 不覆盖已保存配置，linkage/queue 待处理单独记录。完整命中不因 TTL 刷新，对话文件不冒充用户持久化，create metadata 不冒充 edit 权限。 | | |
| J05 | 每候选只有一个最终 story.json4jira 和保留的 TEST_CASE.md、SPEC；不生成独立 STORY.md。SPEC Story 投影与 JSON 业务字段一致，测试镜像与当前表一致；内部路由/诊断不泄露到 Jira。 | | |
| J06 | 生成前加载 Q1–Q7；review 独立读取原来源、JSON、测试和所有 SPEC。实际遗漏/编造/不可验证行为被定位，or、机械命名/布局、负向标签位置不单独构成业务失败。 | | |
| J07 | 新 Story 按内聚价值拆分而非 AC 数；每 AC/FR 有可执行测试，不为拒绝型 AC 编造 Positive 伴侣，不发明接口、日志、SLAs 或无来源场景。 | | |
| J08 | review/score 共用两批内容修复；机械 cause 一次独立，变更仍使受影响 review/score/确认失效。续跑保留计数，无进展提前停止，外部操作不由本地额度授权重发。 | | |
| J09 | 真实业务缺口以具体文件/条目和所需事实询问；部分回答只追问未解决事实，充分回答后恢复局部内容；未知可选 metadata 不虚构、不造成无关阻塞。 | | |
| J10 | scoreUserStory 实际入参恰为 JSON 工作区相对路径、精确 Pass/Fail、实际 SPEC 路径；主链先独立内容 PASS 和 SPEC 状态读回，不使用 relativePath、jira_number 或旧评分工具。 | | |
| J11 | 每次实际评分的完整原始响应、未知字段及真实输入/标识保存为新的编号记录；ok=true 不等于质量通过，数值 76–100、解析/完整性证据及当前内容绑定按 live 契约验证；不自造 checksum 或强制刷分。 | | |
| J12 | SPEC 不出现 Story Score/真实分数显示；没有为显示分数再调用 S2。合法低分/解析阻塞停止，不以内容 PASS 或缓存命中绕过评分保存。 | | |
| J13 | 当前通过评分的内容生成并展示前端 HTML 预览，清楚呈现目标、业务内容与获准附件操作；用户明确确认当前内容才可调用导出。初始请求、设置确认或旧预览不替代本次内容确认。 | | |
| J14 | 预览后修改 JSON、已评分 SPEC、附件选择、目标或提交字段时，使旧确认/评分失效并重新执行所需审查/评分/HTML 确认；仅 path 相同不能证明 bytes 未变，后端 stale checksum 拒绝不得绕过。 | | |
| J15 | exportJiraByDynamicFields 实际只传 story_json_workspace_path，且等于该候选最终通过评分的路径。不传 direct fields、旧 B 五字段、issuesJson、requestJson 或内部 handoffType。 | | |
| J16 | 后端真实执行已评分内容 checksum/threshold 校验；缺评分、低分、未保存或内容不匹配明确拒绝。记录实际服务错误和已知/未知写入结果，不宣称本地检查等同后端校验。 | | |
| J17 | 正常 create 分别记录真实 issue key/URL、每项附件结果、数据库 linkage、queue 以及 async sync 结果；普通 success 或只有 ticketKey 不足以宣布全部完成。 | | |
| J18 | queue 已接受但 async sync 未完成时明确待处理；只有后端真实完成证据才报告 Jira UI 分数已同步。不臆造查询工具、任务ID或完成字段。 | | |
| J19 | 后端 linkage/queue 失败或未知时保留已创建票据与已成功附件，不执行旧五字段 B、不重建票据。只使用实际支持的核对/恢复接口；不可恢复时准确等待。 | | |
| J20 | 三候选按各自 JSON 路径逐票执行，不用 issuesJson；绑定每票评分、预览确认和响应。前票失败/partial/unknown 时按契约停止并保留其余待办，不静默丢项或猜返回Key。 | | |
| J21 | 超时/断连无法确定写入结果时记 unknown，先以实际可用只读手段核对，不自动重发未知/成功的 issue、附件或数据库步骤；status=400 等业务失败不被 envelope success 掩盖。 | | |
| J22 | 更新读取 source、目标、Description/独立AC状态和完整附件 inventory；none/replace 保留义务。live schema 不能表达 target/replace 时明确 WAITING_TOOL，不猜字段、不擅新建。 | | |
| J23 | update none 的本地 SPEC 只用于审查/评分，不被自动当上传附件；replace 只有 schema 明确支持且原件映射获准时才执行，并按真实后端结果确认替换，不假设旧API可用。 | | |
| J24 | 旧 v6 会话迁移保留原始状态、编号证据、修复计数及所有已知/未知远端结果；不把旧 STORY.md 评分当新 JSON 评分，不通过调用重放完成迁移，也不补旧 S2/B。 | | |
| J25 | 按实际 metadata 取得 AC 字段 ID，`customFields.acceptanceCriteria` 保存该 ID，正文从 `fields[该ID]` 读取且为 Markdown 字符串；SPEC/测试/reviewer/评分 AC 识别指向相同正文，截图示例值不被硬编码。 | | |
| J26 | 输入旧扁平 summary/description/acceptanceCriteria/bddScenarios 时在组装/自检识别并修正为 fields 结构；不拿旧顶层内容或旧评分继续导出。变更后按当前包重新评分、确认，不重放已有远端写入。 | | |
| J27 | `fields.description` 被误写为数组/对象时作为结构问题处理，修为保留原业务含义的 Markdown 字符串；不因格式修正编造新业务要求或另加 Q1–Q7 门槛。 | | |
| J28 | `customFields.acceptanceCriteria` 的字段 ID 不被计作 AC 正文/AC 数量；读取对应 fields 值，缺少实际正文不能被映射字符串伪装成内容通过。 | | |
| J29 | AC 映射为空/缺失、指向不存在字段，或字段值为空/非字符串时明确结构阻塞并回到生成；不猜 ID、不从旧顶层数组静默兜底、不宣称通过评分。 | | |
| J30 | 根级 projectKey 与 `fields.project.key`、类型名/真实 `fields.issuetype.id`、新建时 staffId 与 `fields.reporter.name` 一致；更新按获准增量/省略语义保留原 reporter。冲突在评分前解决，不硬编码类型 ID 7 或照抄示例员工号/项目。 | | |
| J31 | 首次无适用配置：先读真实 Workspace/可信身份，再只问尚缺来源、staffId、项目名称/已知 key 或实际项目选择；类型/schema/AC ID 由工具查。保留默认 Jira 意图，没有路线选择或 preparation-only 推荐；元数据未全时不伪造完整可评分 JSON。 | | |
| J32 | 已有适用配置及可信身份：复用已确认项，不重复询问来源/员工号/项目；仅缺失或不适用部分定向查询/提问。Story 类型/schema/AC ID 从真实配置或工具取得，不让业务用户填技术 ID；继续默认流程及最终内容确认。 | | |
| J33 | 用户主动明确只准备/不导出：记录该方向，不执行导出；不把此方向变成默认入口选项。仍只用已知真实值，缺必要元数据则交付已有事实和待补项，不伪造完整可评分 JSON 或实际评分成功。 | | |

## 后端结果证据

后端字段名与状态语义以实际 live schema/响应为准；下表是观测类别，不是虚构的响应字段协议。

| 候选/实际JSON路径 | issue结果与票号 | 各附件结果 | 数据库linkage证据 | queue证据 | async sync / Jira UI分数证据 | 未确认项与下一步 |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

## 独立包边界回归

三个独立包本次完全不改。下表验证隔离边界，不把旧接口场景当作新 JSON 主链的完成证据。

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| R01 | 新 Task 只加载三个活动 Skill，不因新接口不可用自动转入 helper/legacy 或绕过 HTML 内容确认。普通 Jira/Test Case 等显式独立请求仍使用各自包。 | | |
| R02 | jira-createissue-helper 的原业务字段/确认/Test Case 专用格式与换行规则未被新路径接口改写。 | | |
| R03 | user-story-reviewer 原评分来源/回写规则保持其包原样；新 JSON SDLC 不读取其旧四/五字段规则来补数据库操作。 | | |
| R04 | review-requirements 仍为独立需求/任务审查，不自动加入本 Task，不评分/导出，未授权不发 PR 评论。 | | |

## 当前分支验证记录

尚未在此表记录 v6.1 新接口的真实平台结果。填写时保存实际输入文件、评分请求/完整回应、HTML预览、明确确认、导出调用/完整回应及后续状态证据；区分本地检查、mock 推演和真实服务调用。

| 日期/模型/场景ID | 验证类型 | 实际调用、耗时或用量 | 结果与缺失证据 | 证据位置 |
| --- | --- | --- | --- | --- |
| | | | | |

### 2026-11-03 preview.3 本地检查与入口隔离验证

本地检查通过：6 个 JSON、87 条文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter（Ruby Psych）、共享质量副本一致、state 7 和 git diff --check。Task schema/skill IDs/工具列表、评分三参数/导出单路径、JSON 模板及 Q1–Q7 内容保持 preview.2 原样；质量标准版本仍为 6.1.0-preview.2。

GPT-6 Luna 使用最终入口资源，对三个给定 mock 场景产出具体问题与状态。首次无配置：只合并询问来源/staffId/项目名称或已知 key，jiraExportRequested=true；完整已确认 profile：不提问、不重复 query，可继续 assemble；用户主动要求不导出：jiraExportRequested=false，仍补问最终 JSON 所需的缺失配置。根代理另行解析轨迹核对问题、参数范围与状态。三个场景均未提出流程模式选择，也未生成/评分/导出文档或执行真实服务调用；这是入口阶段的离线推演，不能作为 J31–J33 的平台实测。耗时/token 与 GPT-5.6 Luna 未测，平台结果栏留空。

### 历史记录：preview.2 — 2026-11-03 本地检查与 assemble 隔离验证

以下为 preview.2 当时的检查，不验证 preview.3 的默认入口与缺项补齐行为；新增 J31–J33 的真实平台结果尚未记录。

当时本地检查通过：6 个 JSON、85 条文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter、共享标准副本一致、state 7 和 git diff --check；Task schema/skill IDs/工具列表及 Q1–Q7 业务判定表与 preview.1 一致。模板经 JSON 解码检查字段对象、动态 AC 映射、字符串类型、身份/项目关系及 Markdown 换行；旧顶层字段已移除。官方 quick_validate 仍因缺 PyYAML 未完成，frontmatter 使用 Ruby Psych 解析检查。

GPT-6 Luna 当时读取 preview.2 generation 资源，以与截图不同的模拟项目、Story 类型 ID 和 AC 字段 ID，完成一次仅 assemble 阶段的 JSON 生成。根代理独立解码实际产物，确认两个 AC 从真实模拟映射字段读取、描述与 AC 均为有实际换行的 Markdown 字符串、未照抄截图常量；附件仅保留获准的后续 SPEC 路径，未声称文件存在。没有执行完整生产 precheck、review、评分、预览确认或 Jira/数据库调用，不代表 J25–J30 的真实平台测试通过。平台结果栏继续留空；模型耗时/token 未测。

### 历史记录：preview.1 — 2026-11-02 本地检查与 GPT-6 Luna 隔离推演

以下是 preview.1 当时的检查，不验证 preview.2 的 fields/动态 AC 映射结构。新结构的真实平台结果尚未记录。

当时开发检查通过：6 个 JSON 文件解析、83 条本地文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter/命名、两个 Q1–Q7 副本逐字一致、state contract 7、Task schema/skill IDs/10 工具顺序（仅评分 ID 更新）、评分三参数与三个导出单参数示例、独立三目录无修改、git diff --check。官方 quick_validate 因环境缺少 PyYAML 未完成；frontmatter 改由 Ruby Psych 解析并检查同类约束，未安装依赖。

GPT-6 Luna 当时读取 preview.1 包文本，使用给定 mock 文件状态/工具结果生成事件和具体调用提议；未调用真实评分、Jira、数据库或预览服务。根代理另行解析完整轨迹，核对六个场景的调用数量及参数键值。耗时、token、GPT-5.6 Luna 与真实后端行为未测；以上平台结果栏保持空白。

| 隔离场景 | 实际推演结果 |
| --- | --- |
| 当前评分、内容确认及后端 mock 分项成功 | 仅提议一次单路径导出；报告异步分数同步待处理，无 B。 |
| JSON 从已评分/确认的 rev3 改为 rev4 | 拦截旧评分/确认；没有导出提议。 |
| 已发出导出但超时、无法核对结果 | 保留 unknown/WAITING_TOOL；没有重放提议。 |
| 评分返回 SKIPPED、无数值 | 不认定为通过；没有导出提议。 |
| 已有票 none 更新但缺目标 schema/score-only SPEC 支持 | WAITING_TOOL；没有猜字段、新建或导出提议。 |
| 当前审查/头部同步完成，尚未评分且未确认导出内容 | 提议 scoreUserStory 恰三参数，保存完整 mock 响应，不改 JSON/SPEC 分数；等待内容确认，没有导出提议。 |

这些结果只验证给定 mock 下的流程选择和参数形态，不能证明评分落库、附件上传、checksum、数据库关联或队列执行已通过集成测试。

## 历史记录：v6.0 本地验证 — 2026-10-31

以下只针对旧 Markdown 评分 / S1–S2–A–B 接口，不能作为 v6.1 新路径接口的验证结果：

- 当时通过 frontmatter/命名检查、5 个 JSON、73 个本地链接、11 个 JSON 示例与 7 处嵌套 JSON 字符串解码、共享标准逐字一致及旧 Task/schema/工具顺序检查。
- 当时检查旧三字段 Workspace Markdown 评分、旧 A direct payload、五字段 B、none 无附件与 SPEC 分数显示；这些旧调用形态不再用于新 JSON 主链。
- 独立三个包当时未改。曾准备 12 个隔离 mock 场景；GPT-6 Luna 轨迹执行被额度中断，未产生完成记录，不计通过。
- quick_validate 曾因缺 PyYAML 未运行完成，改用 Ruby Psych 验证 frontmatter；未安装环境依赖。
- 当时文件体积从 33 文件/201,394 字节整理为 29 文件/132,666 字节，Task 从 14,390 字节整理为 8,593 字节。仅为历史体积记录，不是新分支运行耗时/token 测量。
