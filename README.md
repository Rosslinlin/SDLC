# CEAIA SDLC v6.1 JSON4Jira flow — preview

当前主分支为 `main`，版本为 `6.1.0-preview.9`。2026-11-07 将 `codex/sdlc-json4jira-flow` 的重构版本合入主线。本项目将每个候选的最终 Jira 内容统一为一个 `story.json4jira`，从该文件生成 SPEC 中的 Story 投影，使用同一路径评分、预览和导出。运行目标为平台 GPT-5.6 Luna / GPT-6 Luna；运行时仅使用现有平台工具和 Markdown/JSON 资源，不执行本地脚本。平台集成验证状态仍以测试清单的实际记录为准。

用于平台“自然语言生成 workflow”的完整中文输入见 [SDLC 工作流自然语言说明](SDLC-WORKFLOW-NATURAL-LANGUAGE.md)，涵盖触发条件、阶段职责、工具调用、内容门禁、更新和附件规则、确认、恢复与验收条件，以本分支当前规则为准。

三个活动 Skill：

| Skill | 职责 |
| --- | --- |
| `ceaia-sdlc-story-spec-generation` | 来源与拆分、前置 Jira 配置读取/查询、最终 JSON/Test Case/SPEC 生成、局部修复、状态与真实评分 |
| `ceaia-spec-review` | 独立读取原始来源、JSON 中的业务内容、SPEC 投影与测试，按共享 Q1–Q7 返回结论 |
| `ceaia-sdlc-jira-export` | 展示已评分当前内容的前端 HTML 预览，取得明确确认，仅以 JSON 路径导出并分别记录后端结果 |

唯一编排规则见 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)。主链为：来源读取 → 在生成阶段解析项目/Story 类型/字段配置并复用持久配置 → 每候选生成最终 `story.json4jira`、`TEST_CASE.md`、`<name>-spec.md` → 生产自检 → 独立内容审查 → 同步并读回 SPEC review 状态 → 真实评分 → 整批覆盖核对 → 前端 HTML 预览 → 用户明确确认当前内容 → 逐候选导出。默认所有用户继续 Jira-backed 完整流程，不在入口提供 Preparation-only / Jira-backed 路线选择。缺信息时保留完整流程意图并补齐对应缺项；初始全流程意图不代替评分后的最终内容确认。

入口先读真实用户 Workspace 的跨 session 配置和可信身份。首次按缺失 source → project → 可选附加字段引导，Epic Link、Story Type 等只展示 metadata 实际支持项，允许暂不设置。已有配置在生成业务文件前展示项目/Story 字段并确认沿用、修改或切项目；当前 run 已明确确认的未变批次/续跑无需重问。技术类型/schema/AC ID 由工具取得。SDLC 不向用户索取 staff ID，也不把它加入新配置、Story JSON 或评分/导出参数；身份由可信平台上下文提供，实际缺失属于工具集成问题。SDLC 固定导出 Story；最终评分后仍要用户检查文件并明确确认导出。缺项保留默认 Jira 流程意图，只有用户主动要求只准备/不导出时才遵从。

`story.json4jira` 是唯一 Story 业务源，也是最终 Jira 提交文件；**不再生成独立 `STORY.md`**。SPEC 中的 Story 展示须从该 JSON 的实际业务字段投影，不能另写一份不同内容。`TEST_CASE.md` 仍保留；仅按已确认 schema 授权的 SPEC 附件计划进入 JSON，测试与内部记录不上传。

开发与用户已确认以下 JSON 结构为生成及评分前的强制格式，具体 ID 和身份值从实际上下文/metadata 取得：

| 位置 | 内容与一致性 |
| --- | --- |
| 根级 `jiraInstance`、`projectKey`、`issueType` | 已确认来源/项目和 Story 类型名；不再生成根级 `staffId`，不把截图示例值当默认值。 |
| 更新专用根级 `issueIdOrKey` | 仅更新现有 Story 时，在 `issueType` 之后加入已确认的实际 Ticket Number；大小写固定为 `issueIdOrKey`（大写 I）。与本候选可信 source/target/baseline 完全一致；创建直接省略此键，不传空值或示例票号。 |
| 根级 `acceptanceCriteria` | 真实 AC 字段 ID 字符串，不是验收标准正文；不再包在 `customFields` 中，不硬编码截图中的字段 ID。 |
| `fields.project.key`、`fields.issuetype.id` | 项目与根级一致，类型 ID 来自实际查询；保留精确大小写和对象结构。创建和更新均不提交 `fields.reporter`，更新省略它不改变 Jira 上的原 reporter。 |
| `fields.summary`、`fields.description`、`fields[acceptanceCriteria]` | 标题保持原文；描述与 AC 正文为 Jira wiki 字符串。其他获准自定义业务值也放在 `fields[实际字段ID]`，只对 metadata 确认的可写富文本应用对应格式。 |
| 根级 `attachmentsJson` | 2026-11-06 用户实测确认旧 `add`/`replace` 格式适用于新路径流程：对象内 `add` 条目使用 `relativePath`、`fileName`，`replace` 条目另含 `attachmentId`。该对象保存在评分及导出的同一文件内，不是额外工具参数；不同时使用非空 `attachments` 数组重复上传。 |

生成阶段、评分之前就完成 Jira wiki 编码：描述标题使用 `h1. 标题`、`h2. 标题`；AC 例如 `* *AC-001*: Given ... When ... Then ...`，以 Jira 列表和单星号粗体保留每条 AC 的声明。JSON 字符串解码后有真实换行，普通正文与代码内容保真；不对 summary、路由/身份/选项、附件路径或更新中未改动字段做格式转换。SPEC 仍为 Markdown，从这些字段的相同语义生成可读投影，不能直接贴入 wiki 标记。导出只提交已评分的原文件，不再临时转换格式。

移除旧顶层 `summary` / `description` / `bddScenarios`、旧 `customFields` 映射包装、根级 `staffId` 及 `fields.reporter`。根级 `acceptanceCriteria` 仅保留实际字段 ID，旧顶层 AC 正文/数组不适用。查询只按实时 schema 使用平台实际提供的身份；不保留手工收集 staffId 的步骤或默认必填参数，缺少身份注入不转成用户问题。生成、自检、SPEC 投影、测试与 reviewer 都读取同一实际 `fields` 内容；映射缺失、类型错误或目标冲突是结构问题，不给 Q1–Q7 新增业务标准。旧 JSON 迁移字段后须重新完成当前包的评分及内容确认，不能沿用旧 checksum。

当前接口边界：

| 操作 | 工具与入参 |
| --- | --- |
| 评分 | `nodejs-base-mcp.scoreUserStory`：`story_json_workspace_path`、`specFileStatus`（精确 `Pass` / `Fail`）、`specFilePath`。主链只在当前独立内容 PASS、SPEC 状态读回通过后提交 `Pass`。 |
| 导出 | `java-base-mcp.exportJiraByDynamicFields`：**仅传 `story_json_workspace_path`**，使用与本候选最终通过评分相同的工作区相对路径。 |

评分完整响应、实际输入及后端返回的真实标识/校验信息仍编号保存。数值 76–100、调用成功及可用的解析/完整性证据按 [评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 核验；不自算分数或猜测后端 checksum。分数不写入 SPEC，本分支没有为显示分数而来的 S2，也不再执行旧的五字段 B 调用。JSON、已评分 SPEC、附件选择或目标字段改变后须重新审查受影响内容、评分并取得对应新内容的确认，不能用旧预览或旧评分继续提交。

导出后端负责校验已评分内容的 checksum/阈值、写入 Jira 与所需附件、回填数据库关联并将异步同步加入队列。前端按真实响应分别报告 **issue、attachments、linkage、queue、async sync**；普通 `success`、票据已创建或已入队，都不能证明数据库关联、异步任务及 Jira UI 分数已全部完成。未返回或无法确认的结果保留为未确认/待处理，不补造成功，不调用旧 B“补关联”。详见 [写入与恢复](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md)。

批量请求仍是每候选一个 JSON，默认按顺序逐票调用路径接口，不发送旧 `issuesJson`。失败、部分成功或未知结果保留各项事实，先按实际支持的只读方式核对；不得自动重放未知或已成功的写入，也不得为修复后端关联/队列状态重新创建票据。

[Q1–Q7 共享内容标准](ceaia-sdlc-story-spec-generation/references/quality-contract.md) 及 reviewer 的同文副本继续使用：来源完整性、价值与范围、可验证 AC、相关场景、可执行测试、SPEC 一致性、已有票保留。对象改为 JSON 的业务内容、SPEC 投影和测试；机械命名/布局、`or` 或负向标签所在行不单独构成业务失败。每 candidate/evidenceRevision 仍有 **2 个共享内容修复批次**；纯机械原因另限一次，续跑保留计数和外部结果。

跨会话配置统一存到真实用户 Workspace 的 `ceaia-sdlc-jira/profiles.json`，SDLC 与通用导出创建/更新都必读。保留现有 profiles 数组，按用户/source/project/实际 issueType/capability 区分多个项目、Story/Epic/Bug 等类型与操作；旧 storyType 兼容读，新 Story 同时保留一致别名。稳定项目/类型/AC 映射跨 create/update 复用，权限/default 保留操作范围，baseline 不被配置代替。issue 真实成功后即执行 fresh-read → merge → 用户作用域 write → read-back，附件/关联/队列失败也不跳过独立配置保存。只存有意义的已验证配置和明确可复用默认，一次性值和业务正文不进入 profile；不完整 metadata 可保留已知路由事实并标 complete=false。profilePersistence=saved 必须有真实用户作用域读回证据，否则明确报告配置未保存并只恢复保存，不重放 Jira。详见[持久化协议](ceaia-sdlc-jira-export/references/workspace-profile.md)。

主链支持创建和更新现有 Story：创建使用 [创建模板](ceaia-sdlc-story-spec-generation/templates/story-json-template.json)，省略 `issueIdOrKey`；更新使用 [更新模板](ceaia-sdlc-story-spec-generation/templates/story-update-json-template.json)，在 `issueType` 后加入根级 `issueIdOrKey`，值为已确认的实际票号。更新先保留原始 Description/AC、附件清单、baseline 和获准增量，再按同一 JSON 路径完成评分、确认和导出；返回票号须与目标一致。目标缺失或冲突先澄清，不得通过省略目标改为新建。评分和导出工具不额外传票号参数。

Story 的标题、描述、AC 或其他业务内容一旦改变，generation 自动更新本地 SPEC、受影响测试与 Story 投影，保留原票及相关 SPEC 的原有义务；无需用户另外说“update spec”。每个相关 SPEC 都要读回并核对当前 Story，完成审查及 Header 同步后再评分。纯路由/schema 变更只核对业务一致性，不为制造文件变化而重写正确内容。

SDLC 默认将远端 SPEC 一起纳入更新计划：已有相关 SPEC 时使用 `replace` 并保留实际原件 ID/输出映射；没有相关 SPEC 时使用 `add` 首次交付；不因用户没提附件而默认 `none`。2026-11-06 用户已实测确认：根级 `attachmentsJson` 是对象，`add` 条目含 `relativePath`/`fileName`，`replace` 条目再加 `attachmentId`；`readJiraAttachments` 返回真实原件 ID。每个替换 ID 必须绑定同一 source/目标票据的实际原文、已确认相关 SPEC 身份和对应新文件，不能按文件名或读取过就替换；非 SPEC、无关和未选原件保留。多个相关原件无法确定映射时，只询问原件选择，不询问是否要同步 SPEC。

使用 `attachmentsJson` 时，不再以非空 `attachments` 数组重复上传同一计划。只有用户明确要求保留远端附件时才用 `none`：维持 `attachments: []`，省略 `attachmentsJson`；本地 SPEC 仍同步，并通过既有 `specFilePath` 评分。最终预览展示原件 ID→新文件及保留项。普通 `replace` 不再因格式未知或重复能力证明进入等待；删除是 `replace` 的后端效果，不新增独立 delete 操作。仍按真实服务响应分别核对新上传和旧件移除；失败、部分成功或未知结果准确保留，不重放已成功写入/上传、不猜恢复操作或回退创建。本次附件格式确认不代表全部后端效果均已验证。

安装与验证：

1. 分别上传三个活动 Skill，核对平台实际返回的 skill ID；保持两个包的 Q1–Q7 副本一致。
2. 核对 Task 中 generation/review 的实际 ID；将 `REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID` 替换为 exporter 的实际 ID，不伪造或填旧 helper ID。
3. 导入兼容路径 [SDLC-workflow-v5.task.json](SDLC-workflow-v5.task.json)。文件名与 schema 保留，显示名为 `SDLC-workflow-v6.1 JSON preview`；原 10 个工具保持顺序，最后的评分 ID 替换为 `nodejs-base-mcp.scoreUserStory`。模型在平台运行配置中选择。
4. 验证真实用户 Workspace read/write 能力及用户作用域，更新后新建 session 读回同一配置；旧本机安装包/已发布平台包不会因仓库更改自动更新。
5. 按 [平台测试清单](PLATFORM-TEST-CHECKLIST.md) 留存两种模型及真实后端证据；此前 v6 的本地/旧接口验证不代表本分支新接口已经通过。

`jira-createissue-helper` 已同步本次持久用户配置规则，仍为独立通用导出包，不加入本 Task，也不是 SDLC fallback；发布通用导出修复时须单独更新其包。`user-story-reviewer`、`review-requirements` 未改。新接口不可用时停止在准确阻塞处。参见 [功能归属](FUNCTIONAL-COVERAGE.md)、[工具参考](TOOLS-REFERENCE-2026-10-26.md) 和 [发布记录](RELEASE-NOTES.md)。
