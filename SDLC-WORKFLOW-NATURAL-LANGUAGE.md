# CEAIA SDLC：用于生成 Workflow 的完整自然语言说明

整理日期：2026-11-04。依据：本仓库 `codex/sdlc-json4jira-flow` 分支的 `6.1.0-preview.6`，状态契约版本为 7。

本文可以整体作为平台“自然语言生成 workflow”的输入。它描述期望生成的工作流，不表示已经执行其中的 Jira、评分或上传操作。工具参数和后端能力仍以平台实际开放的 schema 为准；未确认的能力必须保留为明确的等待项。

## 1. 要创建什么工作流，什么时候使用

请创建一个完整的 CEAIA SDLC 工作流：从用户提供的业务需求和原始证据出发，拆分并生成可交付的 User Story、可执行测试和 SPEC，经过独立内容审查与真实服务评分，再展示最终内容供用户确认，最后创建或更新 Jira，并分别核对附件、评分记录关联和异步分数同步结果。

当用户要求根据需求、设计、Jira、Confluence 或附件生成 CEAIA Story/SPEC，或要求修改现有 Jira Story 及其相关 SPEC 时，使用本工作流。先区分两种模式：`new_story` 创建新 Story；`existing_ticket_update` 更新用户指定的现有 Story。一个被引用的 Jira 票据可能只是需求证据，不能据此把它当作更新目标。明确的更新任务不能自动改为创建任务。

默认执行包含 Jira 的完整流程，记录 `jiraExportRequested=true`。不要在入口让用户选择“只准备文件还是导出 Jira”，也不要因为缺配置而建议取消 Jira。只有用户主动明确要求“只准备、不导出、不要 Jira”，才设为 false；用户明确停止或改变范围时遵从其决定。只准备模式仍须使用真实配置才能生成最终可评分 JSON；缺配置时只能保存已知事实和待补项，不能伪造完整文件或评分成功。

如果调用方只分配某个阶段，则执行到该阶段的边界，不擅自执行后续阶段，也不取消完整工作流原有的后续意图。

本工作流只处理 CEAIA Story/SPEC。UAT-only、独立 Test Case 导出、普通 Bug/Task、普通 Jira 关联及独立需求审查使用各自专用流程。不要在本流程受阻时转到通用 Jira helper 或旧 SDLC 接口绕过门禁。

## 2. 三个角色及固定顺序

编排与生成角色对应 `ceaia-sdlc-story-spec-generation`：负责来源读取、拆分、Jira 配置复用和查询、最终 JSON 组装、测试与 SPEC、生产自检、修复、用户澄清、状态持久化、真实评分和交接。

独立审查角色对应 `ceaia-spec-review`：使用新的只读子代理，独立读取原始来源及当前文件，依据同一套 Q1–Q7 标准返回报告。它不修改文件或状态、不查询 Jira 字段、不评分、不准备上传 payload、不调用 Jira 写工具、不向用户提问，也不判定最终 Jira readiness。父角色保存报告并处理后续动作。

导出角色对应 `ceaia-sdlc-jira-export`：只消费已经完成组装、审查和评分的当前文件，检查交接，展示前端预览，取得当前内容的真实上传确认，以原 JSON 路径调用导出工具并记录分项结果。它不重新发现字段、不改写业务内容、不评分。

固定主链为：来源与目标识别 → 规划与前置 Jira 配置组装 → 保存最终 Story JSON → 生成测试和全部相关 SPEC → 一次生产自检 → 独立内容审查 → 同步并读回全部 SPEC 审查 Header → 真实评分并保存响应 → 整批来源覆盖核对 → 前端预览与当前内容确认 → 逐候选按原路径导出 → 分项核对后端结果与保存可复用配置。

每次把一个就绪候选依次推进到组装、生成、审查、评分完成，再处理下一个。某候选受阻时保留在已选清单中，可以继续准备其他无依赖候选。除非用户明确改变范围，否则所有选中候选和整批覆盖均通过后，才能进入集成导出阶段。

目标运行环境为平台 GPT-5.6 Luna 或 GPT-6 Luna；模型在平台运行配置中选择。工作流运行时只使用已有平台工具及 Markdown/JSON 资源，不运行本地脚本、不新增工具、不编造模型参数。按当前阶段加载所需规则和模板；不同安装包之间使用真实支持的 Skill/resource 读取能力，或由调用方提供完整适用规则，不能假设能读取相邻目录或编造 reader 工具。

## 3. 什么时候调用什么工具

下面十个命名工具是当前 Task 的工具清单。工作流的执行顺序由业务阶段决定，不由工具在清单中的排列顺序决定。

| 工具 | 何时使用 | 参数与边界 |
| --- | --- | --- |
| `java-base-mcp.queryJiraProjectsByName` | 生成阶段，配置及已知证据不能确定项目时 | 已知参数为 `staffId`、`almType`、`projectName`。用户可提供项目名；已有确定项目 key 时不为重复查项目而调用，也不编造该工具的 projectKey 参数。 |
| `java-base-mcp.queryJiraIssueTypesByProject` | 生成阶段，缺少实际 Story 类型名/ID 时 | `staffId`、`almType`、`projectKey`。复用已验证类型；多个实际选择需要选择，无 Story 类型则阻塞本路线。 |
| `java-base-mcp.queryJiraCreateMetaFields` | 生成阶段，缺少适用字段描述、实际 AC 字段 ID、required/type/options 等组件时 | `staffId`、`almType`、`projectKey`，以及实时 schema 支持的 `issueType` 或 `issueTypeId`。它查询 create metadata，不能证明 edit 权限。 |
| `java-base-mcp.exportJiraByDynamicFields` | 当前审查、真实评分、整批覆盖及当前内容确认都通过后 | 恰好一个参数 `story_json_workspace_path`，值与通过评分时完全相同。创建和更新都使用这个接口。 |
| `java-base-mcp.getJiraInfos` | 拆分前读取需求票；更新时读取原票；首次验证新提供的 Epic；必要的受支持只读核对 | 必须先确认该票的 Jira source。Description 和 AcceptanceCriteria 分别调用；其他参数和可读字段遵循实时 schema，不假设任意字段可读。 |
| `java-base-mcp.fetchConfluenceContent` | 用户给出受支持的 Confluence URL 时 | 读取原文作为证据，参数遵循实时 schema。 |
| `java-base-mcp.fuzzyMatchingConfluence` | 有针对性的关键词补充检索确有帮助时 | `queryType=true` 用于发现，`queryType=false` 获取少量详情，`number<=5`；使用提供的 spaceKey 或相关 URL。搜索背景不能自动成为交付范围。 |
| `java-base-mcp.readJiraAttachments` | 现有票更新的来源摄取阶段 | 获取完整清单、原件真实 ID 和可读内容，包括确认零附件。遵循实时 schema；成功读取不因换阶段而重复。 |
| `ask_user_question` | 缺失事实、实际选项、来源错误、设置决定、业务澄清、有限恢复授权或平台支持的确认交互时 | 问精确缺项；不能伪造用户答复或用旧确认代替当前上传确认。业务澄清时尽量合并不超过三个相关问题。 |
| `nodejs-base-mcp.scoreUserStory` | 当前独立内容 PASS，全部 SPEC Header 已同步并读回后 | 恰好三个参数：`story_json_workspace_path`、`specFileStatus`、`specFilePath`。正常主链传大小写严格的 `specFileStatus=Pass`。 |

另外，平台必须提供真实的 Workspace 文件读写、独立只读代理、原生 `.json4jira` 前端预览和当前用户内容/上传确认能力。这里不为这些能力编造工具名。若能力缺失，保存工作和精确阻塞项，进入 `WAITING_TOOL`。业务澄清弹窗不可用时可在文本中准确提问一次并等待；这不能代替缺失的原生预览或上传确认能力。

所有实际调用遵循实时 schema。若实时能力与当前路线必需契约不兼容，准确报告能力缺口，不以新增参数、旧接口、假响应或其他模式绕过。会话身份和请求 Header 由可信平台上下文提供，不能自造 session、Conversation-ID 或 Header。

## 4. 入口、来源和原始证据

先盘点用户提供的业务需求、UX/UI、FSD、API、数据资料、Jira、Confluence、附件、Workspace 文件和明确澄清。保留原始来源，区分直接需求、背景资料和待确认内容。内部记录精确来源位置；Jira 业务文档不能泄漏内部证据路径。

先读取真实用户 Workspace 的适用配置、可信身份和本次已确认设置，再询问缺项。入口只问尚未确认的 Jira source、实际查询确实需要但无法从可信上下文取得的 staffId、项目名/已知项目 key，或工具返回的多个真实项目选择。不要让业务用户填写 Story type ID、字段 schema 或 AC 字段 ID。

第一次读取任何 Jira 票前，必须有该票被明确提供或确认的 source，选项严格为 `WPB`、`ALM`、`DATA`、`FCR`、`GO`。多个未确认票在弹窗中各有独立选择，等待答复。不能按前缀、项目、组织、另一个票推断 source，不能自动轮询不同 source，也不能索取 Jira URL 代替 source 确认。已有适用的明确确认可以复用，不反复询问。

Jira 查询的 `almType` 使用该 source 的小写形式。出现真正的票据不存在或来源错误时，在纠正/重试/停止弹窗中展示相关精确工具错误、票号和已确认 source，让用户纠正票号、纠正 source、重试同一映射或停止。不要暴露凭据或无关敏感传输信息。

需求票在拆分前调用 `getJiraInfos`。Description 和 AcceptanceCriteria 分别读取；评论只在缺少或需要近期澄清时读取，其他字段仅在确实相关且工具支持时读取。独立 AC 字段为空、null、不可用、not-found 或字段级错误，只要 Description 成功，就记录 `empty-or-unavailable`，检查完整 Description 中内嵌的 AC/BDD/验收条件并继续。Description 不可用才阻塞该票的字段摄取；后续真正的业务缺口由正常质量门禁处理，不把空 AC 字段误报为整票读取失败。

Confluence URL 读取支持当前来源规则列出的站点：`wpb-confluence.systems.uk.hsbc`、`digital-confluence.systems.uk.hsbc`、`alm-confluence.systems.uk.hsbc/confluence`、`fcr-wiki.systems.uk.hsbc`、`gbmt-confluence.prd.fx.gbm.cloud.uk.hsbc`、`data-confluence.systems.uk.hsbc:5050`，均按支持的 HTTPS URL 访问。访问失败时索取可读正文或有效的受支持 URL。仅对 Confluence 搜索，可按已支持的上下文映射 DIGITAL→WPB、GBMT→MSS，服务契约允许时才使用 ALM fallback；这个例外不允许推断 Jira source。

截图只能证明截图显示的行为，不能证明后台落库、通知送达、审计持久化或端到端集成。模板、示例、评分诊断和模型常识不能替代业务证据。冲突来源必须问精确决策，不能仅凭篇幅或表面日期选择一方。明确获准的新变更只覆盖对应旧要求，不解除其他原有义务。

## 5. 规划、拆分和整批范围

为全部明确范围内的来源义务分配整批唯一、稳定的 `SR` ID。建立“原始来源位置 → 需求义务 → 候选 → AC → FR → TC”的覆盖关系，保留原始全文。生成的简短 register 不能取代原文。

规划记录候选模式、source、目标票、价值与范围、拆分或继承范围理由、依赖、精确阻塞、JSON/SPEC 路径和附件计划。文件实际生成后才填入实际覆盖，不创建假文件或声称未来文件已经存在。

新 Story 按连贯价值、独立验收、真实流程和依赖判断 INVEST。AC 数量可以提醒评估，不能强制按数量拆票；相关验证和错误行为应保留在其限定的价值结果中。不能编造团队容量、估算或交付承诺。更新现有宽范围票时，票太大本身不授权新建票或删减原范围。

原文明示的报表、集成、通知、审计、欺诈、保留期、MI、测试等义务必须保留；缺少实现细节不等于可排除。缺 actor、核心结果或影响验收的重大规则时，仅阻塞受影响候选并提问。无关分类可省略或简要 N/A；不要为了填模板索取不存在的端点、SLA、安全、通知或审计规则。

整批状态保留用户选中的全部候选及顺序。每个候选可以是就绪或精确等待，不能因受阻而默默移出。所有候选准备完成后，再从原文核对整批 `inScopeSRIds`、`coveredSRIds`、`pendingSRIds`、有依据的用户批准排除及 candidate/evidenceRevision 绑定。只有待覆盖义务为零且证据当前时，整批覆盖才为 pass。

## 6. 前置 Jira 配置与复用规则

创建和更新都先读取真实用户级持久化文件 `ceaia-sdlc-jira/profiles.json`。按可信用户身份、已确认 source、项目和实际 Story type ID 匹配，按组件复用配置。优先使用可信平台身份；真实 staffId 仅为同用户范围内的后备身份或查询所需字段。不读取、解析或存储 JWT/token；不只凭 staffId 跨用户共享配置。会话文件不是用户级跨会话存储。

同用户/source/project/type 下，可以跨 create/update 复用稳定的项目、类型名/ID、AC 字段 ID、字段身份/类型和适用选项编码。已有 create profile 不因本次是 update 就全部失效；原票 baseline 也可提供实际项目/类型事实。足够时三个 discovery query 均不调用。只对缺失或不适用的组件、用户明确刷新/目标变更、实际 schema 错误定向查询，不按 TTL 强制刷新，不为了制造 update capability 重跑三项查询。

requiredness、可写操作、权限和默认值必须保留原操作证据范围。create metadata 不证明 edit 权限，create 默认值不能覆盖现有票。缓存不能代替原票 Description、AC、附件读取，也不能选择更新目标。读取 metadata 时使用真实返回的字段描述；若是 `result.fields.values[]` 则读取其中描述，完整性由真实契约或已结束分页证明，不能编造分页参数。

在生成与评分前解决全部必需值。更新先以批准的 delta 限制变更，再依次采用本次明确批准值、实际 baseline/未改字段省略、适用已确认默认或真实 Jira 默认、直接证据支持的合法选项。只对仍缺的必需业务事实提问，使用工具返回的真实选项；未知可选值省略，不编造 ID、选项或占位值。

创建时明确 Epic/no-Epic。新提供的 Epic 使用受支持的 `getJiraInfos` 证据验证一次，已适用确认过的 Epic 不重复读取。Epic/Parent 和其他关系只放在实际 JSON/后端 schema 支持的位置及表示法，不从旧工具参数猜 wrapper。

组装阶段向用户展示实际 source、项目、Story 类型、创建/更新目标、关系和字段设置，获得所需 Use/Change 设置决定；已有明确当前选择无需重复输入。该决定只确认设置，评分后仍须确认最终上传内容。

保存编号 `jira-assembly-revision-<nnn>.md`，记录真实 profile/query 来源、紧凑 schema、requiredness/选项编码、值的证据、设置决定、JSON 路径/修订和附件计划。完成后才把 assembly 标为 verified。导出阶段只消费该快照，不重新查询、重建或转换。

## 7. 最终 Story JSON 及文本格式

每个候选唯一权威 Story 为真实 Workspace 文件 `story.json4jira`。它是一个有效 JSON 对象，使用平台识别的专用扩展名，不是 Markdown、被再次 JSON 编码的字符串或旧请求 wrapper。不再生成独立 `STORY.md`。测试、SPEC 投影、审查、评分、预览和提交都基于此文件。

| JSON 位置 | 强制规则 |
| --- | --- |
| 根级 `jiraInstance` | 已确认、受支持的小写 Jira source，不增加猜测的 almType/jiraType 别名。 |
| 根级 `projectKey` 与 `fields.project.key` | 同一个真实项目 key，`fields.project` 是对象。 |
| 根级 `issueType` 与 `fields.issuetype.id` | 根级为实际 Story 类型名，后者为实际类型 ID 字符串；保持 `issuetype` 的精确大小写。 |
| 根级 `issueIdOrKey` | 仅更新时放在 `issueType` 后，值为非空、准确的已确认现有票号，和 source/target/baseline/state 一致。`Id` 使用大写 I。创建完全省略此键，不能传 null、空串或来源证据票号。 |
| `fields.summary` | 有来源支持、已审查的标题字符串；没有旧根级 summary。 |
| `fields.description` | 非 AC 业务内容的单个 Jira wiki 字符串，保留所有非 AC 段落，包括原本在 AC 后的内容；不能是数组、对象或内层 JSON 文档。 |
| 根级 `acceptanceCriteria` | metadata/profile 确认的真实 AC 字段 ID 字符串。它是映射，不是 AC 正文、数组或 descriptor。 |
| `fields[acceptanceCriteria]` | 对应实际字段中的非空 AC Jira wiki 正文，包含稳定唯一 AC ID、条件、动作及可观察结果。 |
| `fields[其他实际字段 ID]` | 仅加入批准且对本操作可写的业务值，使用真实类型、选项和编码，不复制未批准 baseline/profile 字段。 |
| 根级 `attachmentsJson` | 用户已实测适用于路径流程的 JSON 对象，非字符串：`add` 数组条目为 `relativePath/fileName`；`replace` 数组条目还包含实际旧 `attachmentId`。新建用 add，更新按已确认原件选择 replace 或首次 add，不再用裸路径数组表达替换。 |
| 明确不改远端附件 | 省略 `attachmentsJson`，维持 `attachments=[]`；本地 SPEC 仍同步并通过 `specFilePath` 评分。add/replace 时省略旧 `attachments` 路径数组，避免重复上传。 |

创建和更新均不生成根级 `staffId`、根级 `summary/description/bddScenarios`、`customFields` 包装或 `fields.reporter`。staffId 可继续用于真实查询及 profile 身份，但不进入 Story 根级、评分参数或导出参数。更新省略 reporter 不表示清空远端值。BDD 细节放在真实 AC 正文中，不维护独立 BDD 对象。

先确认实际 AC 字段 ID，再将同一精确字符串放在根级映射和 fields 的键中。不得硬编码截图 ID，不能把映射 ID 当 AC 文本/数量，不能从旧根级 AC 数组静默兜底。映射缺失、指向不存在字段、正文为空或类型错误，均回到生产/组装阶段处理。

Description、AC 及 metadata 明确允许的其他新编写富文本在生成阶段完成 Jira wiki 编码。标题用 `h1.` 至 `h6.`；AC 声明例如 `* *AC-001*: Given … When … Then …`，列表和单星号粗体均保留。每条 AC 和相应行为仅声明一次，字段自身已有标签时不再加重复 AC 标题。不要新增未经证实的精确标题解析门槛。

格式转换只改变表现：Markdown 粗体转 Jira 单星号，列表保留层级，代码块转 `{code}`、行内代码转 `{{…}}`，链接转 Jira 链接语法，引用转 `bq.`，表格保留各单元格并移除 Markdown 分隔行。代码正文、字面量、ID、顺序、链接目标、日期、数字和标点保真。已有 Jira wiki 先识别格式，不重复转换。普通文本不强加标记；summary、身份/路由/选项/标签/日期/数字/路径及未改动更新字段不做富文本转换。

保存后解码一次检查字符串中的真实换行、引号和反斜杠，不能双重编码、产生字面量反斜杠 n 或用 HTML 换行替代正文换行。导出阶段不再转换格式或重新序列化。

## 8. 测试、SPEC 和一次生产自检

读取已保存 JSON，为实际 AC 分配有依据的 FR，并生成 `TEST_CASE.md`。每个 AC/声明的 FR 都有可执行测试，包含证据支持的入口状态、数据、按序动作、实际观察位置和可观察预期，不能只写“验证成功”或换句话重复 AC。

测试表保留以下 11 列：Test Case Id；AC / Requirement Ref；Scenario Type；Negative Coverage Status；Coverage Rationale / To Confirm；Priority；Test Case Description；Preconditions；Test Data；Test Steps；Expected Results。每行引用一个 AC，可附相关 FR；按真实场景类型分类，按风险使用 P0/P1/P2，不机械复制优先级。测试编号稳定，Markdown 表格中的管道与换行有效。

覆盖来源要求的成功、拒绝、边界、权限和依赖行为。不存在“每 AC 必须一个正向加一个负向”的通用要求；拒绝型 AC 可直接用 Permission/Negative 测试。`Covered` 表示真实拒绝/失败行为已有测试；`N/A` 有具体理由；`To Confirm` 仅用于草稿，重大缺失结果未解决前不能最终 PASS。负向 disposition 放在代表行是紧凑写法，不是内容门禁。

按实质不同条件/结果拆分用例；等价且来源支持的数据可明确参数化。单词 `or` 本身不构成缺陷，也不能按词数或组合数虚构案例。不能编造错误消息、阈值、权限、端点、日志、表或观察位置来让用例看起来完整。

从 JSON 的真实 Jira wiki 业务字段生成可读 Markdown Story 投影，放入每个相关 SPEC。保留所有业务段落、AC ID、文字、顺序、BDD、代码和链接；用一个 AC 标题展示实际正文，不把路由、身份、字段 ID、原始 JSON 或内部控制信息复制成需求。SPEC 是权威 JSON 的视图，不能另写一份不同 Story。

SPEC 必须包含 Summary、Jira Story 投影、与独立文件完全相同的可执行测试表、Functional Requirements 和 Measurable Success Criteria。按证据保留相关 Edge Cases、Key Entities、上下文及流程；有意义时用受支持 Mermaid，简单流程可以文字说明。不能给图补未提供的系统、重试、持久化或错误分支，不能编造量化目标。

SPEC 文件名用安全小写 kebab-case 并以 `-spec.md` 结尾。文件顶部第一条独立 `---` 前只放 `Result` 和 `Reviewer Notes`，初始为 Not Reviewed，审查后 Notes 标记实际 Attempt 和证据支持的简述。保留真实日期和用户提供的作者、Branch、Requirement ID、Parent Requirement ID、Market/Channel/客户分类等；未知可选项省略。批准提供的 commit-specific general-spec 与 architecture.md 业务链接可以保留，内部 source register、Workspace 证据路径、私有下载/token URL、原始响应、审计标识和凭据不得泄漏。不得向 JSON 或 SPEC 写 Story Score。

独立 `TEST_CASE.md` 可保留实际 Related Story JSON、Related SPEC 等 Workspace 追踪信息，但 SPEC 只嵌入其可执行表，不嵌入追踪 metadata。测试文件不作为 Jira 附件。

完成保存后做一次生产自检：核对 JSON schema/实际映射/可写字段/类型/真实换行/无占位；创建或更新目标准确；实际路径和附件文件存在且与授权计划一致；每个 SPEC 的业务投影、FR/SC/流程和测试当前；嵌入测试表与独立文件一致；引用可解析；对原文完成 Q1–Q7 自检。机械格式问题在所属生产层有界修正；自检标记不能替代独立审查。

## 9. 共享 Q1–Q7 内容标准

生成与审查使用完全一致的标准，不自造本地数值评分器。以下是内容通过标准，不代替外部真实评分。

| 标准 | 通过要求 | 典型实质阻塞 |
| --- | --- | --- |
| Q1 来源忠实和完整 | 每条明确范围内义务进入分配的 JSON/AC/测试，或有证据及用户批准的处置；所有业务承诺有来源 | 漏要求、矛盾、把未解决需求藏成排除、添加未经提供的规则。 |
| Q2 价值、清晰和范围 | 能识别 actor、trigger、action、outcome、scope；新 Story 有连贯可验收价值，重要依赖清楚 | 重大缺失/冲突导致无法理解或验收；无关目标混在一起。 |
| Q3 可验证验收 | AC 身份稳定，条件和动作可确定，结果可观察；Given/When/Then 不添加事实 | 无法区分成功失败、AC 冲突、必需行为缺失。 |
| Q4 相关场景覆盖 | 在适用范围覆盖来源要求的成功、拒绝、边界、权限及依赖行为 | 遗漏已证实的重要分支，或其要求的结果未确定。 |
| Q5 可执行测试 | 每个 AC/FR 有对应可执行数据、状态、动作、观察和预期，断言真实要求 | 无法执行或验证、改写期望、编造接口/日志/表/规则。 |
| Q6 SPEC 一致 | JSON 业务字段、每个 SPEC 投影、测试、FR/SC/图表达相同范围，引用有效，所有相关 SPEC 当前 | 内容陈旧或矛盾、漏覆盖、断引用、虚构流程。 |
| Q7 旧票保留 | 更新保留 baseline 义务，仅施加批准的 delta 和附件映射；新 Story 为 N/A | 错目标、未授权改删、丢失旧票或原附件义务。 |

只报告高置信且有实际影响的问题。更多细节“可能更好”只是建议，不阻塞 PASS。文件名风格、列顺序、负向标签所在行、`or`、旧 Header、未知可选 metadata 或常见技术术语不能单独构成业务失败。若格式导致真实歧义、引用丢失或内容不一致，应说明具体影响，分别在正确层处理。

## 10. 独立审查、修复和澄清

每次由新的只读审查角色先读原始来源，再读当前 JSON、测试及全部 SPEC；更新还要读 baseline、完整附件证据、批准 delta 和 add/replace/explicit none 计划。父角色提供实际字段语义和已观察修订。审查同时检查“来源到输出遗漏”和“输出到来源无依据承诺”，不能用生成摘要、以前 PASS、自检或 Header 代替阅读。

所有 SPEC 都要审，包括不上传、只用于评分的本地 SPEC。审过一个文件不能代表未读的其他 SPEC。已读未变的适用结论可以复用；修复后读取改动及依赖并验证受影响标准，不无故重写正确 JSON。

审查报告包含候选身份/模式、实际 artifact paths、读过的 sources、evidence/JSON/test/SPEC body 绑定、每份 SPEC 结果、Q1–Q7 证据、发现、是否需要改 JSON 及一个下一步。不存在或未观察的 revision 用 null，不编造。父角色分配并保存编号审查报告、维护计数和状态。

报告返回一个 JSON，完整保留以下字段：`identity`、`reviewResult`、`artifactPaths`、`reviewedBindings`、`reviewedSpecArtifacts`、`criteriaResults`、`findings`、`coverage`、`sizingSplitRecommendation`、`preservationConclusion`、`clarifications`、`nextAction`，无需重复一份散文报告。`artifactPaths.sourcesRead` 每项包含真实 path/location；JSON/测试绑定及每份 SPEC 的 `contentReadBack=true` 只能由实际读取支持。每 SPEC 记录 path/bodyRevision/result 和 preservation，更新时列出适用原件 ID、保留义务及 gaps；first add/explicit none 即使没有选定原件，也要保留适用 baseline 义务。

`findings` 每项含 id、criterion、classification、path、location、sourceEvidence、problem、impact、action、affectedPaths 和 storyJsonChangeRequired；`clarifications` 用 findingId 关联具体 question。`coverage` 记录 coveredRequirementRefs、gaps、approvedExclusions、crossCandidateIssues，不能用编造计数代替真实来源引用；`sizingSplitRecommendation.decision` 限 NO_SPLIT/SPLIT_RECOMMENDED/NOT_ASSESSABLE 并给理由；`preservationConclusion` 覆盖完整目标而不只主 SPEC，含 result/attachmentAction/authorizedChanges/gaps，新建的 attachmentAction 为 null。SPEC result 用 PASS/FAIL/NOT_ASSESSED，preservation 仅新建可 N/A。

`nextAction` 只给一项，使用 continue-parent-workflow、repair-content、request-business-facts、technical-recovery、complete-review 或 stop-with-unresolved；PASS 使用 continue-parent-workflow。审查报告不加入评分占位、jiraReady、Header-sync、修复计数或旧 title-validation 兼容字段。父角色核对候选身份、路径、完整 SPEC 清单和实际修订后才接纳 PASS，并将报告绑定复制到匹配 state 字段。

发现分四类：`CONTENT_BLOCKER` 是可由现有证据修复的实质内容问题；`CLARIFICATION` 是改变范围/验收的缺失或冲突业务事实；`TECHNICAL_BLOCKER` 是无法读取或安全关联内容/证据；`ADVISORY` 是非阻塞建议。每项写明标准、实际文件/条目、来源证据、问题、影响和最小修复；已解决问题不重复报，一个问题不在多个标准重复计数。

报告总体结论只能是 `PASS`、`NEEDS_REVISION`、`NEEDS_HUMAN_CLARIFICATION`、`FINAL_WITH_UNRESOLVED`。PASS 要求适用标准通过、全部 SPEC 已读且没有阻塞；建议可以保留。业务澄清优先用 NEEDS_HUMAN_CLARIFICATION；可修复问题/技术恢复/审查不全用 NEEDS_REVISION；只有用户明确停止才用 FINAL_WITH_UNRESOLVED。单项标准使用 PASS/FAIL/NOT_ASSESSED/N/A，缺技术访问不能装作已审。

每个 candidate/evidenceRevision 默认最多两个共享内容修复批次，review 与 score 的内容问题共用该预算。每批合并全部已知且有证据的修复，只计一次，并独立验证受影响内容。实质问题没有进展而重复时应提前停止自动修复。不能改范围、删需求或做无意义措辞变化追分。

纯机械问题按真实 causeId/artifactPath 另记，每个真实原因最多一次修正和核验，不消耗内容修复批次。机械改动若触及评分输入，仍使旧评分/确认失效。规范输入仍被后端拒绝属于技术问题，不能无限改写业务内容。

重大业务缺失或冲突立即提问，不先耗尽修复预算。聊天中展示真实完整相对路径、section/AC/TC/FR、短引文或精确缺失、原因和影响；只在已知时引用行号。弹窗保持简短定位、原因和一个具体问题，共享决定可以合并。不要让用户重复已知需求；如果只是模型写错且证据充足但额度耗尽，应请求明确命名、有限的额外修复批次，不捏造业务问题。

记录 pending question ID、finding ID、受影响条目和 evidenceRevision；真实工具返回 questionRef 才保存它。设 WAITING_USER 并等待；部分答复先保留，只问余项，不每答一句就重生成。实质新业务证据只开启受影响候选的新 evidenceRevision/活动预算；额外批次授权只扩展明确范围，累计计数保留。关闭弹窗、换会话或含糊的“继续”不构成新事实或有限授权。

答复充分后，从最早受影响门禁继续：只修改受影响层，联动测试/SPEC，独立复核、同步 Header、评分、最终确认和导出。无需再让用户说 continue。

## 11. 更新现有 Jira 和 SPEC 附件

每个 source+ticket 是独立目标，分别保留 baseline、规划、manifest、审查、评分和外部结果。当前 baseline 的 intake 完成一次即可复用，不因调用另一个 Skill 或新会话而例行重读；已知外部变更时必须重新核对，不能覆盖新内容。

更新读取完整 Description、单独 AC 结果，并通过 `java-base-mcp.readJiraAttachments` 读取完整附件 inventory（包括零附件）、实际原件 ID 和可读内容。这个工具已提供旧 ID，不再要求用户填写或猜测。保存实际 ID、名称、相关性和跳过理由；读取所有相关可读附件。重要附件不可读/跳过必须得到有依据的处理决定。Description 不可用或相关清单不全阻塞该目标。

替换前必须把每个候选原件的完整正文、reader 返回的实际附件 ID/路径与当前 source+ticket 对上，依据文档用途、原业务义务及本票关联确认它确实是受影响 SPEC。在现有 manifest 中区分“确认相关 SPEC / 其他证据或无关附件 / 待确认”，只把确认的 SPEC 原 ID 纳入替换映射。名称含 spec、扩展名、MIME、清单位置、新旧顺序或曾被读取都不能单独证明身份；旧 SPEC 无需符合当前命名或模板。FSD、测试报告、截图、其他票的 SPEC 等资料保留。不可读、同名或关联不明的候选先保留并补原文/精确选择，不能把待确认当作无 SPEC 而直接 add。

建立 `update-manifest.md`：真实 source/target、baseline 路径、每项原义务保留/明确变更/待澄清、获准 delta、Summary 决定、实际附件原件到输出的映射和问题。保留原标题及 priority、assignee、labels、自定义字段、status、links、Epic/Parent，除非对应变更被明确授权；设置默认值不能扩大授权。评分/审计资料另存内部记录。

Story 的标题、描述、AC、范围或其他业务内容改变，自动同步全部受影响本地 SPEC、投影、FR/SC/流程、测试和嵌入表；用户无需另说 update SPEC。已有本地 SPEC 则复用，无则由 baseline、相关原 SPEC 和批准 delta 生成。无远端 SPEC 也要生成当前本地 SPEC。纯路由/schema 变化或正文已一致时记录核对，不为制造变化重写正文。

| 真实情况 | 默认远端附件计划 | 必须保留的边界 |
| --- | --- | --- |
| 已有相关 Jira SPEC | `attachmentsJson.replace` | 每条写入 readJiraAttachments 返回的实际旧 `attachmentId`、当前新文件 `relativePath/fileName`；保留原件义务和无关附件，原件歧义只问具体选择。 |
| 完整清单确认没有相关或待确认 SPEC | `attachmentsJson.add` | 条目为新 SPEC 的实际 `relativePath/fileName`；保留所有现有非 SPEC 附件，不创建另一 Story。 |
| 用户明确要求不改远端附件 | `none`：省略 `attachmentsJson`，`attachments=[]` | 本地 SPEC/测试仍同步、审查及通过 `specFilePath` 评分；明确报告远端有意保留。 |

用户只提 Story/AC 不构成 none，也不需要额外询问是否进行普通本地同步。最终当前内容确认展示实际 add/replace/none 计划和文件映射。

2026-11-06 用户提供了旧接口 add/replace 截图，并确认已实测同样用法适用于新路径流程。生成阶段在 `story.json4jira` 内写原生对象，例如下面的替换片段（ID、路径、名称均须换成真实值）：

```json
{
  "attachmentsJson": {
    "replace": [
      {
        "attachmentId": "<readJiraAttachments返回且确认为本票SPEC的原ID>",
        "relativePath": "outputs/ceaia/updates/<SOURCE>/<TICKET-KEY>/current-spec.md",
        "fileName": "current-spec.md"
      }
    ]
  }
}
```

新增用同一对象的 `add` 数组，每条只有新文件 `relativePath/fileName`；不要给 add 编造旧 ID。add/replace 不再附带旧 `attachments` 路径数组。附件操作在文件内，不成为导出工具的第二个参数。只有确认本票 SPEC 原件后才把其实际 ID 放入 replace；不能按名称删附件或把非 SPEC 证据也列入。独立 delete 参数不在本次确认范围，不能自动组合额外删除调用。

在 Assembly、审查和评分前完成操作对象与原件映射核对，然后冻结文件。已确认的 add/replace 不再重复索取能力证明或因为旧文档写“编码未知”而等待。替换仍要求先上传确认成功再删除所选旧 ID，最终预览展示旧件、新件和保留项；本次用户测试不替代每次执行的真实结果证据。只上传成功或旧件删除失败/未知仍为 partial，核对后仅用受支持恢复处理缺失删除，不能重传新文件或重放 Story 更新。

普通更新目标的 `issueIdOrKey` 和上述附件对象均已确认。明确 none 保留空上传列表及当前本地 SPEC 评分，不增加额外能力证明门槛。若后端真实拒绝，保存精确错误再定向处理，不能覆盖用户 none、删目标改创建或回退旧接口。

## 12. SPEC Header 同步和真实评分

独立内容 PASS 后，父角色将全部 SPEC 的 Result/Reviewer Notes 写为当前实际审查结果和 Attempt，再读取完整文件，确认业务正文未改变。第一次 Header 不一致允许一次仅 Header 重试；再次不一致等待技术恢复。全部 Header 写入必须在评分前结束。审查角色不执行评分，也不以未产生的评分记录作为内容审查前提。

评分前读取实际 JSON 和相关 SPEC，确认 schema、来源覆盖、更新目标、附件计划、review 绑定以及真实文件当前。调用 `nodejs-base-mcp.scoreUserStory`，只传以下三个参数：`story_json_workspace_path` 为该候选真实 JSON 的 Workspace 相对路径；`specFileStatus` 为严格大小写 `Pass`；`specFilePath` 为实际主 SPEC 的 Workspace 相对路径。接口枚举是 Pass/Fail，但正常主链非 PASS 不进入评分。

不传 `relativePath`、`force_review`、jira_number/source、conversationId、staffId、票号额外参数、粘贴正文或评分报告路径。路径不能是绝对路径、URL、别名、含父目录穿越的路径或猜测的会话前缀。保存此路径，后续预览/导出逐字复用。

为真实调用分配下一编号 `story-quality-score-attempt-<nnn>.md`，记录实际三参、JSON/SPEC 修订、review attempt 和完整实际响应，包括未知字段、错误及服务标识。文本里含 JSON 时保留原文并解析完整 assessment，不挑有利的嵌套结果；已解码对象则完整序列化一次并标记保存模式，不能声称字节原文。响应截断或冲突是技术阻塞。保存后读回，确认调用期间输入未改变，才可设通过状态。

通过政策为真实 boolean `ok=true`、有限数值 `finalScore` 在 76–100 之间，并且输入校验和 AC/parser 识别有效。分数 75、字符串数值、标签、维度推算、人工判断、SKIPPED 或 VALIDATION_FAILED 都不能代替通过。76 是当前 SDLC 政策，不能声称已验证后端实际配置阈值就是 76；后端拒绝仍阻塞导出。

检查真实 parseErrors/warnings 和返回的 AC 数量，和 `fields[acceptanceCriteria]` 中独立声明的 AC 对照；映射 ID、引用和重复提及不算 AC。可选 count/ID 不返回不自动判失败，但非空 AC 返回零/矛盾数量或未解决识别错误阻塞 readiness。规范 AC 仍不被识别时进入技术诊断，不把权威字段换回 Markdown 追分，不编造 evaluator 权重或本地算分。

缓存命中只在后端真实返回且当前包匹配时有效；不保证每次调用都命中、不声称每次调用必新增审计版本。后端计算 checksum，模型不得计算替代品或猜归一化规则。session/path/version 及 JSON、评分 SPEC 的实际内容共同关系到当前性。

评分后冻结 JSON 和评分 SPEC 的实际文件内容，分数只在独立记录/UI 展示，不写 JSON、SPEC 或附件，不调用旧显示分数 S2 或五字段关联 B。不能为了换分重复评分未改内容。

收到响应后本地保存失败，只重试保存；评分已知在评估前失败时最多一次有依据的修正重试；超时结果不明先使用真实支持的核对能力，不能盲重放。低分只允许在共享内容预算内按现有证据修复；真实业务缺项等待用户。无有效评分、响应未保存或当前性不明时 readiness 保持 false。

## 13. 哪些变化会使已有门禁失效

编辑受影响内容前，将 readiness 置 false，当前 preview confirmation 置 stale；完成真实写入并读回后才推进已观察 revision。revision 是本地观察记录，不是 checksum、远端锁或不可变证明。

| 变化 | 必须返回的步骤 |
| --- | --- |
| JSON 任意字节，包括路由、字段映射和附件列表 | 检查并同步受影响投影/测试/SPEC；业务或映射语义改变时独立复核；对当前包重新校验/评分，按影响复核整批覆盖，再取得新内容确认。 |
| 已评分 SPEC 的内容、名称/路径或评分附件选择 | 对齐引用并独立检查改变的业务内容，先同步 Header 再评分，重新确认；不能假设 metadata 改动不影响缓存。 |
| 只改测试 | 同步 SPEC 嵌入表，完成受影响独立复核；SPEC 已变化，须新评分及确认。 |
| Result/Reviewer Notes | 评分前完成；评分后 Header 改动也改变已评分文件，需当前评估及确认。 |
| 用户在前端预览中编辑 | 返回生成层，修改权威文件及依赖，完成受影响审查/评分/新确认；不能提交旧评分下的预览改文。 |
| 已知远端票发生变化 | 在更新前协调 baseline 与保留义务，重走受影响门禁。 |

纯格式/schema 修复无需重写正确业务内容或无故全量重审，但改了评分输入就不能沿用旧评分/确认。与改动无关且当前绑定已建立的候选和结论继续有效。

## 14. 整批交接、最终预览与明确确认

父角色在当前独立 PASS、Header 同步读回、真实通过评分和整批覆盖通过后，才设置 `jiraReady=true`；更新仅在完整受支持时设置 `updateReady=true`。交接包含 statePath、实际 batchStatePath、原始来源、assembly/review/score 记录、原样 JSON 路径、全部 SPEC、更新 baseline/manifest/真实附件映射及能力依据。内部 `handoffType=ceaia-sdlc-validated` 不进入工具参数。

导出角色读取当前实际文件及编号记录，核对身份、全部 SPEC 清单、审查实际读取绑定、Header、评分输入、assembly、覆盖、更新目标和附件计划。可复用未变的完整读取，不能仅信 PASS 文本或修订计数。缺失、过期或冲突返回上游；导出阶段不重新做内容生成或评分。

只准备模式在完成真实可完成的覆盖/文件/评分后交付，不能声称 Jira 成功。正常导出模式使用平台原生 `.json4jira` 前端预览展示实际已评分文件：创建/更新操作、source/project/Story type、准确更新目标、全部业务内容、已同步本地 SPEC 及精确 add/replace/explicit none 计划。前端可能呈现为 HTML，这不要求另生成 HTML 上传文件或发明预览工具。

必须得到用户对当前包的真实内容/上传确认，且绑定 JSON 路径、revision 和全部相关 SPEC file revisions。初始“做完整 SDLC”、较早设置确认、旧版本确认和模型写“confirmed”均不算。相同文件/版本已有真实确认可复用；可一次确认多个清楚标识的当前包。用户拒绝时保留结果并按明确修改或停止决定处理，不擅自提交。

保存当前唯一 `jira-preview.md`：实际单参数调用、JSON 路径/修订、SPEC 路径/文件修订、target/附件摘要、真实预览和确认依据。preview confirmation 设 confirmed 并引用证据记录；不用再编写一份不同的业务 Story。历史实际确认保存在编号 export attempt，不用覆盖历史记录来表示新授权。

## 15. 按原路径导出及后端结果

每个候选分配 `jira-export-attempt-<nnn>.md`，保存当前评分记录/输入、assembly/review/coverage、预览确认、提交前读回、准确单参数请求和完整响应。提交前再核对实际 JSON 和 SPEC 未改变、目标和确认一致。

真正调用 `java-base-mcp.exportJiraByDynamicFields`，恰好一个参数 `story_json_workspace_path`，逐字复制当前通过评分输入及 `paths.storyJsonPath` 的值。不能改名、规范化、重新序列化或重建文件，不能额外传 Summary、Description、issueIdOrKey、staffId、conversationId、SPEC 参数、requestJson、dynamicFieldsJson、attachmentsJson、issuesJson、数组批量 wrapper 或内部状态信息。更新票号和 `attachmentsJson.add/replace` 已在已评分 JSON 内；禁止额外工具参数不表示禁止文件内的附件对象。

默认按确认的候选顺序，每候选一个文件、一次顺序导出，不假设批量原子性。第一个必需同步结果失败、部分成功或未知时停止后续写入，保留后续选择。确认入队且异步同步尚 pending 本身不是同步提交失败。

后端负责核对已保存评分/当前 checksum/阈值、执行 Jira 和附件操作、把评分记录与 Jira 票关联到数据库、把 Jira 分数同步加入异步队列。本流程不另发旧 B 调用完成这些动作。

完整保存实际响应后分别检查以下五类事实；这是本地观测分类，不要求后端恰好返回同名字段：

导出响应为原始文本时完整保留；仅有已解码对象时完整序列化一次并标记保存模式，不声称字节原文。未知字段、错误及真实服务标识一并保存。

| 结果类别 | 需要什么证据 |
| --- | --- |
| issue | 明确的真实创建/更新结果和无歧义票据身份，返回 URL 时保留；更新返回 key 必须等于目标。 |
| attachments | 每个要求上传/替换/删除的结果，或真实文档保证覆盖这些精确操作的原子成功；授权计划为空才是零上传 N/A。 |
| databaseLink | 真实评分记录到票据的关联证据，或实际契约明确保证；只有票号不够。 |
| scoreSyncQueue | 真实 accepted/enqueued 证据或明确契约保证，真实返回队列 ID 才记录。 |
| jiraScoreSync | 真正完成/失败/pending 证据；入队只证明调度，没有完成证据则报告 pending/unknown。 |

若权威响应的明确语义保证多个结果，可记录该依据。一般 success、MCP `isError=false` 或“票已创建”不能证明全部步骤；还要检查嵌套业务失败、400、checksum 或阈值拒绝。可选 ID 缺失不自动算失败，必需效果缺证据则保留 unknown/pending。

同步提交完整需要 issue、要求附件、databaseLink 和 queue 均已确认；此时可以报告提交完成、Jira 分数同步待处理。不能因为工具返回或队列存在就说 Jira UI 分数已更新。某必需 SPEC 未确认时不能称整条 Story/SPEC 同步完成；明确 none 时说明远端附件有意不变。

## 16. 写入失败、部分成功和未知结果恢复

完整保留已收到的返回，包括未知字段、错误、服务标识和分项结果。若本地响应保存失败，只重存已有响应，不重新调用外部写入。

票据已成功但附件、关联或队列失败时保留真实票号和成功文件，不能重建或再次更新票来修其他步骤，也不能重传已成功附件、使用旧 B 或通用 helper、调用旧独立 upload/delete 绕过本路线。

超时、断连等结果不明必须记录为 unknown，不能视为未开始。只使用平台确实存在且受支持的只读核对：Jira 读可能证明票和附件，不能证明数据库关联或队列。没有无歧义结果和受支持恢复接口时进入 WAITING_TOOL，列出精确待处理效果。含糊“继续”不证明幂等。

校验/checksum/阈值拒绝时先确认是否有外部效果；输入陈旧或无效回到生成层有界修正、受影响审查/评分和新确认。只有真实契约证明没有外部效果，且必需修正/确认已完成，才允许新的受支持尝试。导出时不临时删字段或改文件，不自动盲重试。

替换的新上传未知时保留旧附件；只有新上传已确认成功才允许受支持后端继续删除所选旧附件。不能按名称删、重放成功上传或把追加文件说成已替换。

异步任务由后端处理。不要编造 polling/recovery 工具、循环等待或重导出来推动队列。报告真实 pending/failed 和存在的受支持下一步。缺后端 schema、恢复能力或结果证据是明确工具缺口，不等于成功。

## 17. 文件、状态、续跑和配置保存

新候选用户文件目录为 `outputs/ceaia/<slug>/`，内部目录为 `.ceaia-work/stories/<slug>/`。更新用户目录为 `outputs/ceaia/updates/<SOURCE>/<TICKET-KEY>/`，内部目录为 `.ceaia-work/updates/<SOURCE>/<TICKET-KEY>/`，不同 source 的同号票不能混用目录。

用户文件保留当前 `story.json4jira`、`TEST_CASE.md`、`<name>-spec.md` 及确实需要的不同 SPEC。内部保存 `state.json`，更新另有 `update-manifest.md`；编号证据包括 plan-revision、jira-assembly-revision、review-attempt、story-quality-score-attempt、jira-export-attempt，编号使用三位格式且不覆盖已完成记录。保留原始来源；不堆历史用户交付文件副本，完整响应和输入读回证据存编号记录，不重复塞满 state。

整批状态为 `.ceaia-work/batch-state.json`，包含有序 selectedCandidateIds、candidateId/statePath、整批 SR 覆盖、批准排除和 evidenceRevision 绑定。传交接时必须提供实际 batchStatePath。

候选 state 的 contractVersion=7，至少维护：身份/模式/source/ticket、evidenceRevision、phase/status/nextAction、Jira 意图；真实文件路径和已观察 JSON/test/SPEC body/file revisions；assembly 快照；review attempt/报告/全部 SPEC 绑定及 Header 读回；score attempt/实际输入/完整响应指针及当前性；修复累计和每原因机械恢复；pending 用户问题；当前预览确认；独立的实际 writeResults。

状态使用 IN_PROGRESS、READY、WAITING_USER、WAITING_TOOL、STOPPED、COMPLETE。未知值用 null/pending，不把模板示例写成成功。`specArtifacts.uploaded` 只是计划上传标志，真正远端结果放 writeResults。COMPLETE 可以描述同步提交已确认、异步 Jira 分数仍 pending，但最终报告必须说明这个区别；同步必需结果未知不能被 COMPLETE 掩盖。

续跑先读取最新实际文件和编号记录，检查绑定后才复用已完成门禁，不因新会话重置修复计数、忽略 pending 或把未知写入变未开始。确定未变且有完整读回时复用；本地计数不是远端锁或数据库证据。

只有真实 issue 和全部要求附件均成功，才把该候选当前、已验证的上游 assembly 快照保存到真实用户 Workspace 的 `ceaia-sdlc-jira/profiles.json`；不能从成功业务文案反推 schema。关联/队列/异步结果分别报告，不抹掉独立符合条件的配置保存；失败/未知的 issue 或必需附件不能覆盖旧成功配置。

profile 保留可信用户范围、source/project/type 绑定、适用字段 descriptor、实际 AC ID、按操作区分的能力及确认过的可复用默认。descriptor 包含真实 ID/name/schema.type/items/required/operations、观察到的默认和用过的选项 ID/编码。不能缓存 Story/AC 正文、payload、附件内容或路径、来源 register、凭据、会话 ID 或会话文件路径；不能从旧 profile 把 reporter 带回提交文件。保存时保留其他用户隔离项、项目和 capability，读回确认成功。

无法用户级持久化或 profile 保存失败时，单独报告配置未保存，保留真实远端结果，绝不重放 Jira。没有上游完整快照则返回生成角色处理配置问题，导出角色不现场重查或重建。

## 18. 版本迁移、边界和完成报告

本说明对应当前 JSON v6.1 预览路线。本机或历史包中的 `STORY.md → 旧评分 → 测试/SPEC`、`score_requirement_markdown`、独立 `ceaia-sdlc-only-jira-push-content`、旧 direct payload/五字段 B、评分后写 SPEC 分数 S2、单词 or 禁令、每 AC 强制正负配对以及三次 self-repair 规则，均不能混入当前主链。

`jira-createissue-helper`、`user-story-reviewer`、`review-requirements` 是本仓库独立包，不加入当前三 Skill Task，也不作为新接口不可用时的 fallback。其独立请求按各包原规则执行，不能用旧调用形态证明当前 JSON 后端集成通过。

未提交的旧 JSON/Markdown 包迁移时，先保留原始状态、来源、评分记录、修复计数和全部已知/未知远端结果，再用实际 metadata 生成当前 fields + 根级 AC ID 格式，移除旧包装/staffId/reporter、补真实更新目标并完成 Jira wiki 编码，按受影响内容同步、复核、重新评分与当前确认。不能把旧评分或旧 checksum 当当前授权。已提交或结果未知的操作先核对，不能为迁移重建/重导出，也不补旧 B/S2。

最终回复必须给实际文件路径、各候选状态、已确认票号/URL、独立审查结论、真实评分、来源覆盖、当前本地 SPEC 与远端附件结果、数据库关联、队列、异步同步状态、配置保存情况，以及每个剩余阻塞和一个明确下一步。缺失结果准确写未确认，不补造成功。

完整导出模式只有全部选定必需同步效果经证据确认才报告同步提交完成；异步 Jira 分数字段另报。用户明确停止则保留文件、未解决项和所有远端结果。只准备模式按用户意图结束并交付实际产物。等待输入、工具缺失、低分、拒绝确认、部分失败和未知写入都是可恢复状态，不能因达到尝试次数伪装成功或擅自取消。

## 19. 生成 Workflow 后的验收条件

生成结果至少应覆盖以下可验证行为。以下是待执行的验收要求，不表示已经完成平台实测。

1. 无配置时只询问缺失 source/必要 staffId/项目，不出现流程路线选择；完整 profile 时不重复问、不重复 discovery query。
2. 未确认 source 的多个 Jira 票各有独立选择；空独立 AC 配合可读 Description 能继续；真正票/source 错误不自动换源。
3. 新建没有 issueIdOrKey；更新使用准确目标；根级 AC 是实际字段 ID，正文从实际 fields 映射读取；无 root staffId/customFields/reporter。
4. Jira wiki 在评分前完成，SPEC Markdown 投影、AC 顺序/ID/代码/链接及测试保持一致，评分后导出无文件转换。
5. Story-only 业务更新自动同步所有本地 SPEC/测试；已有 SPEC 用 attachmentsJson.replace 并携带 readJiraAttachments 实际旧 ID，无 SPEC 用 add，明确保留远端省略对象且 attachments=[]。保护非 SPEC，不因已确认格式重复等待；真实服务拒绝和部分结果准确记录。
6. 审查先读原文，reviewer 只读不评分；合理 or/N/A/拒绝型 AC 不因旧模板规则误失败；真遗漏和无依据承诺被发现。
7. 内容修复共享两批、每机械原因一次，续跑不清零；真实新事实只影响对应候选。
8. 评分恰三参且发生于 Header 完成后；75、SKIPPED、VALIDATION_FAILED、AC 识别冲突、响应未保存均不能导出；分数不写 SPEC，无 S2/B。
9. 有候选待处理或 SR 未覆盖时禁止整批导出；前端改动使旧评分/确认失效，初始意图不替代最终确认。
10. 导出恰一参且与评分路径相同；批次按顺序逐候选；失败/部分/未知停止后续写入；票成功不自动证明附件/关联/队列成功。
11. 超时不重放，配置保存失败不重放，后端关联/队列失败不新建票；明确区分同步提交完成与异步 UI 分数待处理。
12. 用真实平台记录验证评分、预览、确认、Jira/附件、数据库关联和队列；本地检查、mock 推演和其他模型结果不能替代目标模型真实集成证据。

平台发布时上传三个活动 Skill，核对真实 skill ID，并把 Task 中 exporter 的占位 ID 替换成真实返回值，不猜测或填写旧 helper ID。兼容文件名仍为 `SDLC-workflow-v5.task.json`，schema 为 `enriched-agent.task/v2`，显示名为 `SDLC-workflow-v6.1 JSON preview`；保留原十工具顺序，评分项为 `nodejs-base-mcp.scoreUserStory`。模型在运行配置中选取。当前仓库的 checklist 尚未记录新接口的完整真实平台通过证据，生成与发布不能把规则说明当作后端已验证结论。

## 20. 原始规则索引

本节用于维护追溯。以上正文已展开工作流规则，平台不应只加载本索引而省略正文。

- [仓库说明与活动范围](README.md)、[当前 Task](SDLC-workflow-v5.task.json)、[工具契约](TOOLS-REFERENCE-2026-10-26.md)。
- [生成角色入口](ceaia-sdlc-story-spec-generation/SKILL.md)、[编排/状态/失效/修复](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)、[Q1–Q7 权威标准](ceaia-sdlc-story-spec-generation/references/quality-contract.md)。
- [来源摄取](ceaia-sdlc-story-spec-generation/references/source-enrichment.md)、[现有票更新](ceaia-sdlc-story-spec-generation/references/existing-jira-update.md)、[前置字段组装](ceaia-sdlc-story-spec-generation/references/jira-json-assembly.md)、[Jira wiki 编码](ceaia-sdlc-story-spec-generation/references/jira-text-format.md)。
- [产物规则](ceaia-sdlc-story-spec-generation/references/artifact-generation-rules.md)、[metadata/示例边界](ceaia-sdlc-story-spec-generation/references/metadata-and-examples.md)、[评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md)、[澄清与续跑](ceaia-sdlc-story-spec-generation/references/user-clarification-and-resume.md)。
- [生成模板目录](ceaia-sdlc-story-spec-generation/templates/)，含 create/update JSON、Story 投影、测试、SPEC、规划、score record、candidate state、batch state 模板。
- [独立审查入口](ceaia-spec-review/SKILL.md)、[审查契约](ceaia-spec-review/references/review-contract.md)、[审查报告模板](ceaia-spec-review/references/review-report-template.md)、[审查包同文质量副本](ceaia-spec-review/references/quality-contract.md)。
- [导出入口](ceaia-sdlc-jira-export/SKILL.md)、[交接门禁](ceaia-sdlc-jira-export/references/handoff-and-content.md)、[附件边界](ceaia-sdlc-jira-export/references/attachments.md)、[预览/写入/恢复](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md)、[用户级配置保存](ceaia-sdlc-jira-export/references/workspace-profile.md)、[调用示例](ceaia-sdlc-jira-export/references/call-examples.md)、[profile 模板](ceaia-sdlc-jira-export/templates/profile-template.json)。
- [平台验收清单及已记录验证范围](PLATFORM-TEST-CHECKLIST.md)、[功能归属与隔离边界](FUNCTIONAL-COVERAGE.md)。
