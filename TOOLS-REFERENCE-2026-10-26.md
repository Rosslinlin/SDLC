# SDLC 与 Story/Jira 工具说明

整理日期：2026-10-26；2026-10-30 同步 main 的 v5.3 SDLC 导出接入、既有 reviewer 修复和独立需求审查。适用于当前 SDLC Task、三个活动 Skill，以及独立的 `user-story-reviewer`、`jira-createissue-helper` 和 `review-requirements`。这是配置和排查用的工具速查；实际调用仍须核对平台当时暴露的工具 schema。

## 资料与边界

- `java-base-mcp.exportJiraByDynamicFields` 的参数和返回规则，以用户在本对话提供的开发说明为依据。
- `nodejs-base-mcp.score_requirement_markdown` 的两种入口及返回概述，以用户提供的 [评分工具交接文档](/Users/rosshu/Documents/Codex/2026-10-09/users-rosshu-documents-codex-2026-09-2/outputs/story-scoring-tools-handoff-2026-10-23.md) 和本仓库 [评分工具契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 为依据。交接文档早于后来确认的大小写规则：`specFileStatus` 现仅接受精确的 `Pass` 或 `Fail`。
- 各 Skill 的调用顺序、评分门槛、确认方式和绑定条件是**流程规则**，不一定是工具本身的必填参数。以下以当前仓库规则为准；旧交接文档中“独立 reviewer 不再调用导出工具”的说法已过时。
- 本文不包含可执行脚本、Token 或真实用户的员工号、会话 ID。

## 1. 当前工具清单与用途

| 工具或平台能力 | 主要用途 | 使用位置 |
| --- | --- | --- |
| `nodejs-base-mcp.score_requirement_markdown` | 读取 Workspace Story 或 Jira Issue、评分并保存评分/审计记录 | SDLC 最终 Story 评分；独立 `user-story-reviewer` |
| `java-base-mcp.exportJiraByDynamicFields` | Jira 创建、更新、批量、附件、Issue 关联，以及评分记录与 Ticket 的关联/同步 | `ceaia-sdlc-jira-export`（SDLC）；`jira-createissue-helper`（通用）；独立 reviewer 的自动后台 update 触发 |
| `java-base-mcp.queryJiraProjectsByName` | 按来源和项目名检索、验证 Jira 项目 | Jira 导出准备 |
| `java-base-mcp.queryJiraIssueTypesByProject` | 获取选定项目可用的 Issue 类型 | Jira 导出准备 |
| `java-base-mcp.queryJiraCreateMetaFields` | 获取项目/类型的字段元数据，确认 AC 等字段可写及其 ID | Jira 创建/更新字段组装 |
| `java-base-mcp.getJiraInfos` | 读取已有 Issue 信息 | SDLC generation 的更新基线；新 exporter 验证新 Epic 或异常核对；普通更新/关联；需求审查的可选 Jira 上下文 |
| `java-base-mcp.readJiraAttachments` | 读取已有 Issue 的附件清单和可读内容 | SDLC 现有 Ticket 更新的证据 intake |
| `java-base-mcp.fetchConfluenceContent` / `java-base-mcp.fuzzyMatchingConfluence` | 获取或搜索用户指定的 Confluence 需求证据 | SDLC 需求 intake |
| `ask_user_question` 或平台等效弹窗 | 收集缺少的 Jira source、必填选择、配置确认或恢复决策 | 各 Skill 按自身规则使用 |
| 平台 Workspace 读写能力、独立只读 reviewer | 保存/复读 Story、Test Case、SPEC、评分与审查记录；执行独立内容审查；真实用户级 Workspace 保存跨 session 导出配置 | Workspace 操作是平台能力，reviewer 是执行角色，均不是本文中命名的 Jira MCP 工具；用户级存储不能用会话文件冒充 |
| 平台 GitHub/PR 读取及经授权的评论发布能力、独立验证 agent | 读取 PR 文档、diff 和现有评论，验证候选问题，按用户明确请求发布报告 | 仅独立 `review-requirements` 按需使用；无固定工具名，不执行 gh CLI、脚本或外部遥测 |

Jira 读取、Confluence 和上述平台能力的完整实时参数 schema 未在本对话提供。表格只说明当前仓库如何使用它们，不推断未展示的参数或工具名。

## 2. Story 评分工具：`nodejs-base-mcp.score_requirement_markdown`

### 两种互斥入口

| 模式 | 必要输入 | 当前流程 |
| --- | --- | --- |
| Workspace Markdown | `relativePath`：真实、已保存的工作区相对 `.md`/`.markdown` 路径 | SDLC 新建和更新都使用最终 `STORY.md` 路径；独立评分中无 Ticket/本地创作的 Story，以及用户明确选择的当前业务修订（即使已导出并有 Ticket），也使用 Workspace 模式；自动落盘的 Jira 抓取不属于本地修订 |
| Jira Issue | `jira_number` 与 `source` 成对提供；`source` 为 `WPB`、`ALM`、`DATA`、`FCR` 或 `GO` | 独立 reviewer 对 Jira 当前版本、原始抓取/导出/粘贴记录提取主 Ticket 评分；长内容自动落盘后仍不传载体路径 |

两种入口不能混用。SDLC **更新**即使已知 Ticket Key，评分时仍传修改后的 Workspace `STORY.md` 路径，不传 `jira_number`/`source`。评分调用不传 `staffId` 或 `conversationId`；Workspace 身份由可信请求头 `X-AF-Conversation-ID` 提供。不要把上传 JSON 文件路径当作 Workspace Markdown 评分输入。

两个工具的来源值对应关系为 `WPB→wpb`、`ALM→alm`、`DATA→data`、`FCR→fcr`、`GO→go`。这只是已确认来源的格式转换，不能从 Ticket 前缀推断来源。

### SPEC 元数据与可选参数

| 参数 | 规则 |
| --- | --- |
| `specFileStatus` | 工具只接受大小写严格的 `Pass` 或 `Fail`。SDLC 在独立审查和 SPEC header 都为 `PASS` 后，调用时将其映射成 `Pass`；**不得传 `PASS` 或 `pass`**。非 PASS 审查在正常 SDLC 链路中阻断评分，因此不会发送 `Fail`。 |
| `specFilePath` | 与本次最终 Story 对应的主 SPEC 工作区相对路径。正常一条新 Story 对应一个主 SPEC。 |
| `force_review` | 正常省略；只有用户明确要求对相同内容重新评估时才考虑。具体类型遵循实时 schema。 |
| `metadata` | 可选审计信息，正常省略；不传秘密或完整业务正文。具体类型遵循实时 schema。 |

`specFileStatus`/`specFilePath` 随评分记录保存，不改变 Story 分数。缓存命中时仍可保存本次传入的 SPEC 元数据；为了刷新这两个字段不强制重评。独立 `user-story-reviewer` 当前省略这两个 SPEC 参数。

工具返回通常为**一个含完整 JSON 的文本块**。按实际返回保存所有字段；已知可能包括 `ok`、`finalScore`、`cache_hit`、`review_id`、`audit_id`、`acCount`、`parseErrors`、`warnings` 和分项诊断，但不要求每次都出现全部字段。不要从分项分数反算总分。`ok=true` 表示调用成功，不表示通过 SDLC 质量门槛。

**SDLC 放行门槛**是流程规则：当前完整结果中的 `ok === true`、数值型 `finalScore >= 76`，且 AC 识别和解析没有未解决问题；此前还必须有独立内容审查 `PASS`、SPEC header 同步和全局覆盖通过。独立 reviewer 与普通导出的评分关联没有这个 76 分门槛；低分（包括 0）也可能是有效评分记录。

## 3. Jira 导出工具：`java-base-mcp.exportJiraByDynamicFields`

**直接传命名参数**，不套 `request` 或 `requestJson`。同一工具在有 `issueIdOrKey` 时更新已有 Issue，否则创建；`issuesJson` 用于批量。

| 参数 | 工具契约重点 |
| --- | --- |
| `almType` | 必填；小写 `wpb`、`alm`、`data`、`fcr` 或 `go`。与评分工具 Jira 模式的大写 `source` 区分。 |
| `staffId` | 请求没有有效 JWT 身份时，用于加载 Jira Token 的备用员工号；有效 JWT 的 `sub` 优先。当前 SDLC 第二次绑定调用仍要求提供真实非空值。 |
| `projectKey`、`summary`、`issueType` | 创建必需；更新时不改这些字段则省略。批量创建可在 item 中提供。 |
| `issueIdOrKey` | 现有 Issue 的 ID 或 Key，提供后走更新；Markdown 绑定更新必须使用 Ticket **Key**，不能使用纯数字 ID。批量更新逐 item 提供。 |
| `description`、`projectName`、`epicName`、`epicLink`、`parentLink` | 按实际 Jira 操作需要传入；不编造或用占位值补齐。 |
| `dynamicFieldsJson` | **JSON 编码的字符串**，解码后为对象；用于动态 Jira 字段。SDLC 的 AC 放在元数据确认的独立可写字段中，不放进 Description。 |
| `testDetailsJson` | JSON 编码的字符串，解码后为数组、对象或字符串；仅在 Test Details 要变更时使用。 |
| `attachmentsJson` | JSON 编码的字符串，解码后为对象或数组；支持 `add`、`replace`、`deleteAttachmentIds`。替换会先上传新附件，成功后才删除旧附件。 |
| `linkedIssueKeys`、`linkType` | 前者是**原生**非空 Ticket Key 字符串数组，不是 JSON 字符串；后者为 Jira 关联类型，默认 `Relates`。关联是新增，不删除已有链接。 |
| `issuesJson` | JSON 编码的非空对象数组字符串，用于批量；不要发送未暴露的 `issues` 参数。批量的更新目标、附件及 Markdown 绑定路径按 item 指定。 |
| `conversationId` | 工作区相对附件和 Markdown 绑定的备用会话 ID；请求中的会话 Header 优先。 |
| `markdownReviewRelativePath` | 仅在明确进行 Markdown Review Ticket 绑定时传入评分**输入 Story 文件**的工作区相对路径；不是本地评分结果文件。需要真实会话 ID。批量时只放在对应 item 内，顶层路径不继承。 |

四个 `*Json` 参数是外层调用中的**字符串**，内部各编码一次合法 JSON；`dynamicFieldsJson` 解码为对象，`testDetailsJson` 为数组/对象/字符串，`attachmentsJson` 为对象/数组，`issuesJson` 为非空对象数组。未使用的可选参数应省略或传真正的 JSON `null`；JSON 字符串参数也可为空字符串。不要传 `.`、`...`、`N/A`、`undefined` 或字符串 `"null"`，也不要猜缺少的必填值。

`dynamicFieldsJson` 可放顶层 Jira 字段，或使用 `fields`、`update`、`customFields` 对象；不得塞入附件或 Issue 关联控制字段。Test Issue 的测试步骤也可按工具文档中的 `testDetails`、`testSteps`、`steps`/`step`、`data`/`testData`、`result`/`expectedResult` 形式提供；这些会作为 Test Details 处理。纯文本若不是合法 JSON 字符串，不能作为 `testDetailsJson`。附件 `add` 支持工作区相对路径或 Base64 内容；仅 Base64 附件本身不需要 `conversationId`，Markdown 绑定需要。

认证优先级按开发说明为 `Authorization: Basic <JWT>` 高于 `X-HSBC-E2E-Trust-Token`；JWT 的 `sub` 覆盖入参中的 `staffId`。请求中的会话 Header 优先于 `conversationId` 参数。无效 JWT 身份是 MCP 工具错误。

单条成功返回 `ticketKey`、`ticketUrl`；带附件、Issue 关联或 Markdown 绑定时还需检查各自结果和 `pass`/`partial` 状态。批量返回 `uploadType=multiple`、`successCount`、`failureCount` 和逐项 `resources`。数字 `status=400` 是参数业务失败，即使 MCP 外层 `isError=false` 也不能当作成功。绑定失败不抹去已保存的 Jira Ticket；保留 Key/URL，不重建或重放成功的第一步。

当前 Jira 写入 Skill 还要求：Jira 后端会在导出时添加 `CEAIA_GEN` 标签，调用方不要把它再放进工具 payload。这个标签行为来自仓库现行 Skill，不是上述参数表的必填输入。

## 4. 同一 Jira 工具的三种后续调用，不要混淆

| 场景 | 首次 Jira 操作后 | 后续调用 | 完成判定 |
| --- | --- | --- | --- |
| **SDLC Story** | 已通过独立审查、SPEC 同步及 76 分门槛；第一次创建/更新 Jira 与要求的附件成功，得到该项真实 `ticketKey` | 每个成功项各调用一次，**恰好五个非空直接参数**：`staffId`、`almType`、`issueIdOrKey`（返回的 Key）、`conversationId`、`markdownReviewRelativePath`（本次通过评分所用的 `STORY.md` 路径） | `markdownReview` 明确确认绑定成功；失败时保留已成功的 Jira Ticket |
| **普通 Story 的 Workspace 评分及补回写** | 当前评分所对应的创建/更新成功；确认该轮实际评分输入路径与 Ticket 映射 | 每个符合条件的成功项使用五字段；单票更新也必须继续此步。新评分重置待关联状态，同路径/同 Ticket 的旧关联不能代替本轮。漏关联时只补这一步；已知已评分但缺路径时询问并保持待关联 | 分别记录内容写入和本轮绑定；真正未评分的普通 Story 保持原导出行为，不套用 SDLC 门槛 |
| **Jira 模式评分的 update 触发** | Jira 模式返回有效数值评分后自动执行；或用户明确要求回写所指的 Jira 模式评分。不因 Ticket 已存在就假定属于此模式；来源不明时正常询问 | 自动以**四个非空直接参数**触发后台 update：`staffId`、`almType`、`issueIdOrKey`、`conversationId`；**不传** `markdownReviewRelativePath`，不询问是否同步；缺少必需 ID 时正常询问并等待，补齐后自动继续 | 普通 success 只证明调用完成，不证明项目已开放或分数字段已写入；有匹配评分文件时追加响应，否则保留会话工具结果；评分后的自动触发不展示常规状态，明确只回写时答复请求已提交。失败不重新评分或自动重试 |

同一会话里生成并导出过的本地 Story 继续修改时，reviewer 读取当前本地文件，以实际 `relativePath` 评分，保留原 Ticket/source 映射。用户要求更新 Jira 时，helper 先更新该 Ticket 的内容，再以本轮评分输入路径执行五字段关联；每次新评分均有独立的待关联状态。后续补说“回写”时，按这轮 Workspace 评分恢复缺失的五字段步骤，不再把已有 Ticket 直接归为四字段。Workspace 评分本身不自动触发四字段调用。

SDLC 第一次 Jira 写入不带 `markdownReviewRelativePath`，因为新建时尚无 Ticket Key。第二次只做上述关联，不带 Summary、Description、动态字段、附件、批量字段或其他 Jira 内容变更参数。多个 SDLC 新建可先用 `issuesJson`，但其返回的每个成功 Story 都需要以各自的 Key 和路径分别完成后续关联；Task 更新逐票执行。

## 5. 其余 Jira 与证据工具的已知调用边界

| 工具 | 当前已确认的直接参数或规则 |
| --- | --- |
| `queryJiraProjectsByName` | `staffId`、`almType`、`projectName` |
| `queryJiraIssueTypesByProject` | `staffId`、`almType`、`projectKey` |
| `queryJiraCreateMetaFields` | `staffId`、`almType`、`projectKey`，再加 `issueType` 或 `issueTypeId` 中至少一个 |
| `getJiraInfos` | SDLC generation intake 分别读取原 Description 与 Acceptance Criteria，并保存结果供新 exporter 复用；导出正常路径不例行重读。新选择 Epic 可验证一次，实际证据缺口、已知外部变化或不确定写入结果按需核对。完整参数 schema 未在本对话给出。 |
| `readJiraAttachments` | SDLC 更新在规划前完成附件清单 intake，并保留原附件、ID、可读内容及无法读取的原因。完整参数 schema 未在本对话给出。 |
| `fetchConfluenceContent` / `fuzzyMatchingConfluence` | 只用于获取相关需求证据；完整参数 schema 未在本对话给出。 |

## 6. 按 Skill 配置

| Skill | 应能使用的工具或平台能力 |
| --- | --- |
| `ceaia-sdlc-story-spec-generation` | Workspace 文件读写；`getJiraInfos`、`readJiraAttachments`（现有 Ticket 更新）；`fetchConfluenceContent`、`fuzzyMatchingConfluence`（涉及 Confluence 证据时）；`ask_user_question`；最终 `score_requirement_markdown`；独立 reviewer 的平台调用能力 |
| `ceaia-spec-review` | 独立只读审查和 Workspace/证据读取；不调用评分或 Jira 写入工具 |
| `ceaia-sdlc-jira-export` | 正常配置命中：真实用户级/当前会话 Workspace、`ask_user_question`、`exportJiraByDynamicFields`；缺配置、变更或恢复时按需使用三个查询工具；新 Epic 或异常核对时 `getJiraInfos`，不例行重复 intake |
| `jira-createissue-helper`（通用，独立保留） | 三个 Jira 项目/类型/字段查询工具、`getJiraInfos`、`exportJiraByDynamicFields`、`ask_user_question` 与 Workspace 预览/复读能力；保留 main 的通用已评分 Story 绑定与恢复修复，仅在主入口和旧 gateway 入口分流当前 bundled SDLC 与明确选择的 legacy SDLC |
| `user-story-reviewer`（非 SDLC） | `score_requirement_markdown`；Jira 模式评分用四字段触发；Workspace 内容已写成功后的补关联用带实际输入路径的五字段 `exportJiraByDynamicFields`；Workspace 读写；`ask_user_question` |
| `review-requirements`（独立需求/拆分审查） | Workspace/文档读取与报告保存；PR 模式按需使用 GitHub/PR 读取；有 title Jira ID 时可选 Jira 读取；独立验证 agent。只有用户明确要求时才使用可用的 GitHub 评论写入工具；不调用 Story 评分、Jira 导出或遥测 |

本仓库 [Task JSON](SDLC-workflow-v5.task.json) 的 `tools` 列表列出 SDLC 所需的 10 个命名工具；Workspace 文件操作和独立 reviewer 能力由平台另行提供，不在该列表中。Skill 的具体路由和字段规则见各自 `SKILL.md`，尤其是 [SDLC Story exporter](ceaia-sdlc-jira-export/SKILL.md)。通用 helper 的旧路由及独立需求审查不加载进新的 SDLC Task。

新 SDLC exporter 的字段 profile 位于真实用户工作区 `ceaia-sdlc-jira/profiles.json`，跨 session 复用精简字段规则、ID 与已确认默认值。完整命中跳过三个 query 和已确认 Epic 读取，不因 session 或时间经过刷新；仅对缺失/不适用配置、用户明确变更/刷新或明确字段校验失败查询受影响部分。每次导出仍确认本次目标、字段值及 Epic/Parent 决策。原 helper 的 metadata 复验/确认规则保留在其独立目录，不用于新的 SDLC 路径。

两种 Jira 导出 Skill 同时安装时，当前 bundled SDLC 的 Task 和普通对话 handoff 均交给 `ceaia-sdlc-jira-export`；helper 即使先被选中，也须转交新 exporter，不自动执行旧 gateway。只有明确选择 legacy SDLC 时才使用旧 gateway；新 exporter 缺失或门禁失败不能自动回退旧流程。此次仅调整 helper 的 `SKILL.md` 和 `references/sdlc-gateway.md` 两个入口，其余 helper 文件及全部独立 reviewer 文件保留整合前 main 基线，普通 Jira/Test Case 流程不改。

`review-requirements` 仅有 `SKILL.md`（全部审查标准）与 `templates/review-report-template.md`（报告模板）两个文件，作为独立审查能力使用，不替代 SDLC 的 `ceaia-spec-review` 或新增 Task 阶段。
