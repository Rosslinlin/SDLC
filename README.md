# CEAIA SDLC v6.1 JSON4Jira flow — preview

当前分支版本为 `6.1.0-preview.5`，日期 `2026-11-03`，工作分支为 `codex/sdlc-json4jira-flow`，尚未合入 `main`。本分支将每个候选的最终 Jira 内容统一为一个 `story.json4jira`，从该文件生成 SPEC 中的 Story 投影，使用同一路径评分、预览和导出。运行目标为平台 GPT-5.6 Luna / GPT-6 Luna；运行时仅使用现有平台工具和 Markdown/JSON 资源，不执行本地脚本。

三个活动 Skill：

| Skill | 职责 |
| --- | --- |
| `ceaia-sdlc-story-spec-generation` | 来源与拆分、前置 Jira 配置查询/缓存、最终 JSON/Test Case/SPEC 生成、局部修复、状态与真实评分 |
| `ceaia-spec-review` | 独立读取原始来源、JSON 中的业务内容、SPEC 投影与测试，按共享 Q1–Q7 返回结论 |
| `ceaia-sdlc-jira-export` | 展示已评分当前内容的前端 HTML 预览，取得明确确认，仅以 JSON 路径导出并分别记录后端结果 |

唯一编排规则见 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)。主链为：来源读取 → 在生成阶段解析项目/Story 类型/字段配置并复用缓存 → 每候选生成最终 `story.json4jira`、`TEST_CASE.md`、`<name>-spec.md` → 生产自检 → 独立内容审查 → 同步并读回 SPEC review 状态 → 真实评分 → 整批覆盖核对 → 前端 HTML 预览 → 用户明确确认当前内容 → 逐候选导出。默认所有用户继续 Jira-backed 完整流程，不在入口提供 Preparation-only / Jira-backed 路线选择。缺信息时保留完整流程意图并补齐对应缺项；初始全流程意图不代替评分后的最终内容确认。

入口先读取真实用户 Workspace 中的适用配置和可信身份。只有尚未确认的来源、实际 staffId、项目名称/已知项目 key，或工具返回的多个实际项目选择需要询问用户；已确认配置直接复用。Story 类型、字段 schema 与 AC 字段 ID 通过现有查询工具取得，不要求业务用户填写技术 ID。缺项是待补信息，不取消默认 Jira 流程，也不推荐 preparation-only。只有用户主动明确要求“只准备/不导出”时才遵从；必要元数据不全时保存已有事实与待补项，不能伪造完整可评分 JSON。

`story.json4jira` 是唯一 Story 业务源，也是最终 Jira 提交文件；**不再生成独立 `STORY.md`**。SPEC 中的 Story 展示须从该 JSON 的实际业务字段投影，不能另写一份不同内容。`TEST_CASE.md` 仍保留；仅按已确认 schema 授权的 SPEC 附件计划进入 JSON，测试与内部记录不上传。

开发与用户已确认以下 JSON 结构为生成及评分前的强制格式，具体 ID 和身份值从实际上下文/metadata 取得：

| 位置 | 内容与一致性 |
| --- | --- |
| 根级 `jiraInstance`、`projectKey`、`issueType` | 已确认来源/项目和 Story 类型名；不再生成根级 `staffId`，不把截图示例值当默认值。 |
| 更新专用根级 `issueIdOrKey` | 仅更新现有 Story 时，在 `issueType` 之后加入已确认的实际 Ticket Number；大小写固定为 `issueIdOrKey`（大写 I）。与本候选可信 source/target/baseline 完全一致；创建直接省略此键，不传空值或示例票号。 |
| 根级 `acceptanceCriteria` | 真实 AC 字段 ID 字符串，不是验收标准正文；不再包在 `customFields` 中，不硬编码截图中的字段 ID。 |
| `fields.project.key`、`fields.issuetype.id` | 项目与根级一致，类型 ID 来自实际查询；保留精确大小写和对象结构。创建和更新均不提交 `fields.reporter`，更新省略它不改变 Jira 上的原 reporter。 |
| `fields.summary`、`fields.description`、`fields[acceptanceCriteria]` | 标题保持原文；描述与 AC 正文为 Jira wiki 字符串。其他获准自定义业务值也放在 `fields[实际字段ID]`，只对 metadata 确认的可写富文本应用对应格式。 |

生成阶段、评分之前就完成 Jira wiki 编码：描述标题使用 `h1. 标题`、`h2. 标题`；AC 例如 `* *AC-001*: Given ... When ... Then ...`，以 Jira 列表和单星号粗体保留每条 AC 的声明。JSON 字符串解码后有真实换行，普通正文与代码内容保真；不对 summary、路由/身份/选项、附件路径或更新中未改动字段做格式转换。SPEC 仍为 Markdown，从这些字段的相同语义生成可读投影，不能直接贴入 wiki 标记。导出只提交已评分的原文件，不再临时转换格式。

移除旧顶层 `summary` / `description` / `bddScenarios`、旧 `customFields` 映射包装、根级 `staffId` 及 `fields.reporter`。根级 `acceptanceCriteria` 仅保留实际字段 ID，旧顶层 AC 正文/数组不适用。身份配置与查询工具按实际 schema 所需的 `staffId` 仍保留；这不是评分/导出的额外入参。生成、自检、SPEC 投影、测试与 reviewer 都读取同一实际 `fields` 内容；映射缺失、类型错误或目标冲突是结构问题，不给 Q1–Q7 新增业务标准。旧 JSON 迁移字段后须重新完成当前包的评分及内容确认，不能沿用旧 checksum。

当前接口边界：

| 操作 | 工具与入参 |
| --- | --- |
| 评分 | `nodejs-base-mcp.scoreUserStory`：`story_json_workspace_path`、`specFileStatus`（精确 `Pass` / `Fail`）、`specFilePath`。主链只在当前独立内容 PASS、SPEC 状态读回通过后提交 `Pass`。 |
| 导出 | `java-base-mcp.exportJiraByDynamicFields`：**仅传 `story_json_workspace_path`**，使用与本候选最终通过评分相同的工作区相对路径。 |

评分完整响应、实际输入及后端返回的真实标识/校验信息仍编号保存。数值 76–100、调用成功及可用的解析/完整性证据按 [评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 核验；不自算分数或猜测后端 checksum。分数不写入 SPEC，本分支没有为显示分数而来的 S2，也不再执行旧的五字段 B 调用。JSON、已评分 SPEC、附件选择或目标字段改变后须重新审查受影响内容、评分并取得对应新内容的确认，不能用旧预览或旧评分继续提交。

导出后端负责校验已评分内容的 checksum/阈值、写入 Jira 与所需附件、回填数据库关联并将异步同步加入队列。前端按真实响应分别报告 **issue、attachments、linkage、queue、async sync**；普通 `success`、票据已创建或已入队，都不能证明数据库关联、异步任务及 Jira UI 分数已全部完成。未返回或无法确认的结果保留为未确认/待处理，不补造成功，不调用旧 B“补关联”。详见 [写入与恢复](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md)。

批量请求仍是每候选一个 JSON，默认按顺序逐票调用路径接口，不发送旧 `issuesJson`。失败、部分成功或未知结果保留各项事实，先按实际支持的只读方式核对；不得自动重放未知或已成功的写入，也不得为修复后端关联/队列状态重新创建票据。

[Q1–Q7 共享内容标准](ceaia-sdlc-story-spec-generation/references/quality-contract.md) 及 reviewer 的同文副本继续使用：来源完整性、价值与范围、可验证 AC、相关场景、可执行测试、SPEC 一致性、已有票保留。对象改为 JSON 的业务内容、SPEC 投影和测试；机械命名/布局、`or` 或负向标签所在行不单独构成业务失败。每 candidate/evidenceRevision 仍有 **2 个共享内容修复批次**；纯机械原因另限一次，续跑保留计数和外部结果。

跨会话字段规则仍保存到真实用户工作区 `ceaia-sdlc-jira/profiles.json`，由 generation 在生成最终 JSON 前读取/解析并拥有配置内容；票据及要求附件确认成功后，只保存上游已确认的配置快照，后端关联/异步结果仍单独报告。完整命中不按时间强制刷新。不能把对话文件当成用户级持久化，也不能把 create metadata 当作 update 权限。

主链支持创建和更新现有 Story：创建使用 [创建模板](ceaia-sdlc-story-spec-generation/templates/story-json-template.json)，省略 `issueIdOrKey`；更新使用 [更新模板](ceaia-sdlc-story-spec-generation/templates/story-update-json-template.json)，在 `issueType` 后加入根级 `issueIdOrKey`，值为已确认的实际票号。更新先保留原始 Description/AC、附件清单、baseline 和获准增量，再按同一 JSON 路径完成评分、确认和导出；返回票号须与目标一致。目标缺失或冲突先澄清，不得通过省略目标改为新建。评分和导出工具不额外传票号参数。

`none` / `replace` 附件语义保留。普通 `none` 更新使用 `attachments: []` 保留远端附件，本地 SPEC 通过已确认的 `specFilePath` 参数评分，再按标准更新流程确认和导出；不额外索取 score-only 支持证明，不把本地 SPEC 自动加入上传。具体 replace 编码及上传后删除保证仍须真实后端证据；仅实际后端明确拒绝普通更新，或请求的替换能力缺失时，记录对应的 `WAITING_TOOL`。保存已有目标、baseline、增量和附件映射，不猜附件控制、不擅自追加上传或回退创建。字段确认和本地检查不代表真实后端更新已集成验证。

安装与验证：

1. 分别上传三个活动 Skill，核对平台实际返回的 skill ID；保持两个包的 Q1–Q7 副本一致。
2. 核对 Task 中 generation/review 的实际 ID；将 `REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID` 替换为 exporter 的实际 ID，不伪造或填旧 helper ID。
3. 导入兼容路径 [SDLC-workflow-v5.task.json](SDLC-workflow-v5.task.json)。文件名与 schema 保留，显示名为 `SDLC-workflow-v6.1 JSON preview`；原 10 个工具保持顺序，最后的评分 ID 替换为 `nodejs-base-mcp.scoreUserStory`。模型在平台运行配置中选择。
4. 按 [平台测试清单](PLATFORM-TEST-CHECKLIST.md) 留存两种模型及真实后端证据；此前 v6 的本地/旧接口验证不代表本分支新接口已经通过。

`jira-createissue-helper`、`user-story-reviewer`、`review-requirements` 三个独立包完全不改，不加入本 Task。本分支不会为兼容其旧接口另写通用路由；新接口不可用时停止在准确阻塞处。参见 [功能归属](FUNCTIONAL-COVERAGE.md)、[工具参考](TOOLS-REFERENCE-2026-10-26.md) 和 [发布记录](RELEASE-NOTES.md)。
