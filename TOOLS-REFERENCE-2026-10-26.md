# SDLC JSON 工具契约 — 6.1.0-preview.10

身份规则更新：2026-11-08。主线合并日期：2026-11-07。文件路径沿用旧名；当前内容定义 main 的 SDLC JSON 路线，来源为 codex/sdlc-json4jira-flow 重构分支。目标模型为 GPT-5.6 Luna / GPT-6 Luna，纯文本调用已有平台能力，不运行脚本。

## 权威来源和边界

- 新后端截图说明 `.json4jira` 文件驱动、服务端格式校验/checksum/评分保存、创建时自动关联及异步同步队列。
- 用户随后明确：评分工具改名 **scoreUserStory**，仍传 **JSON 路径 + SPEC 状态 + SPEC 路径**；导出工具仍为 **exportJiraByDynamicFields**，只传该 JSON 路径；Story 分数暂不写入 SPEC。这些澄清优先于截图中的评分单参数样例及示意名称 create_jira。
- 2026-11-03 用户向开发确认的最新格式：根级 acceptanceCriteria 直接存实际 AC 字段 ID，不再有 customFields 包装或 fields.reporter/root staffId；AC Jira wiki 在 fields[acceptanceCriteria]。创建省略 issueIdOrKey；更新在 jiraInstance/projectKey/issueType 后加入根级 issueIdOrKey（大写 I），值为确认的现有票号。Description/AC 在生成时完成 Jira wiki 编码，SPEC 保留 Markdown 语义投影；字段 ID、查询身份与项目取真实上下文/metadata。
- 这个 Wiki 拆分前的维护分支仅追加附件。根级 `attachmentsJson.add` 的条目包含真实 `relativePath` 和 `fileName`，原附件保留。调用仍只传 JSON 路径。
- 工具实时 schema 和以上已确认文件契约是实际调用依据，不再把普通replace格式当未知或重复索取能力证明。缺失的返回字段及恢复能力不能由调用方编造。完整后端评分权重没有提供；Q1–Q7 是共享内容标准，不是替代评分器。

Task v3 采用阶段 Skill/输出关联，不内嵌旧 tools 数组；运行时仍需在平台启用本文件及自然语言编排所列的十项业务工具与实际 Workspace/预览/确认能力，接口参数未随 Task 格式迁移改变。

## 评分：nodejs-base-mcp.scoreUserStory

在独立内容 PASS、所有 SPEC Result/Reviewer Notes 同步并读回后调用：

```json
{
  "story_json_workspace_path": "outputs/ceaia/example-story/story.json4jira",
  "specFileStatus": "Pass",
  "specFilePath": "outputs/ceaia/example-story/example-story-spec.md"
}
```

仅这三个参数。specFileStatus 保留大小写严格的 Pass/Fail；正常 SDLC 在 review PASS 后传 Pass。JSON 是真实 Workspace 文件，使用 .json4jira 扩展供前端识别；传实际路径，不传原 Markdown、评分报告、URL、绝对路径或改写别名。session_id 由可信平台请求上下文提供，不能塞进调用参数或自行制造 Header。

服务端先校验 JSON；VALIDATION_FAILED 错误字符串和 SKIPPED 不等于 Story 评分通过。保存完整实际响应（JSON 或文本）和每次真实调用输入，不截断未知字段。保留当前 SDLC 76–100、ok=true、parser/AC识别有效的本地政策；后端具体阈值与成功/部分返回仍须实测核对。只从真实 finalScore 读取分数，不从维度反算。

新 checksum 涉及 JSON 和评分 SPEC。评分后冻结两者；预览编辑、字段变化或附件内容变化都需要当前包重新校验/评分。分数只在独立报告/UI显示，**不写 SPEC、不触发旧的显示分数 S2**。缓存命中是否另写审计/版本按实际返回，不沿用旧接口每次调用必新增审计的假设。完整规则见 [评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md)。

## 导出：java-base-mcp.exportJiraByDynamicFields

前端呈现已评分内容并取得当前版本的实际上传确认后，只传同一路径：

```json
{
  "story_json_workspace_path": "outputs/ceaia/example-story/story.json4jira"
}
```

不再把 Summary、Description、dynamicFieldsJson、attachmentsJson、issuesJson、身份/会话值或旧五字段关联作为工具参数；Story 文件也不再写根级 staffId。每候选一个完整 JSON，批次默认逐文件调用，不自行发明路径数组或批量 wrapper。路径必须与本轮评分输入原样一致；导出阶段不查询/转换/重组业务内容。

附件操作保存在该 `story.json4jira` 的根级 `attachmentsJson` 对象内。以下是文件片段，示例路径及文件名必须换为本候选真实值，不能作为工具入参或全局默认值：

```json
{
  "attachmentsJson": {
    "add": [
      {
        "relativePath": "outputs/ceaia/updates/WPB/EXAMPLE-1/account-notification-spec.md",
        "fileName": "account-notification-spec.md"
      }
    ]
  }
}
```

使用 `attachmentsJson.add` 追加经评审的当前 SPEC。旧件和其他证据附件保持原样。原 ID 只用于基线核对。明确不上传时保留 `attachments: []` 并省略 `attachmentsJson`，本地当前 SPEC 仍通过 `specFilePath` 评分。

提交前核对评分所用 JSON 与当前上传计划一致。按实际响应分别确认每个新增附件。遇到旧替换计划时先回到准备阶段确认当前计划并重新评分。未知结果通过支持的只读能力核对。

创建后端负责核评分记录/checksum/阈值、Jira/附件写入、匹配记录关联和同步任务入队。必须分别记录实际 issue、attachments、databaseLink、scoreSyncQueue 和 jiraScoreSync 结果；这些是本地规范化事实，不要求后端返回这些同名字段。普通成功或票号不能证明落库关联/入队全部完成。异步 PENDING 不等于 Jira 分数字段已同步。

旧 Agent B 调用不属于本路线。后端关联/队列失败时不能重建票据或重传成功附件，更不能用旧五字段调用试探补救。未知结果先通过实际支持的只读能力核对，无可用恢复接口就保留明确 pending/unknown。详见 [导出协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md)。

## 生成阶段的字段/证据工具

| 工具 | 用途 / 业务输入（身份由平台提供） |
| --- | --- |
| java-base-mcp.queryJiraProjectsByName | 生成 JSON 前确认项目；almType、projectName |
| java-base-mcp.queryJiraIssueTypesByProject | 确认真实 Story 类型；almType、projectKey |
| java-base-mcp.queryJiraCreateMetaFields | 字段required/type/options/AC等；almType、projectKey，及实时 schema 支持的 issueType或issueTypeId |
| java-base-mcp.getJiraInfos | 原票 Description/AC与可用事实、首次新Epic验证或必要异常核对；其余参数遵循实时schema |
| java-base-mcp.readJiraAttachments | 更新的附件清单及可读基线证据。原件 ID 绑定实际来源和票据，仅用于证据；工具参数遵循实时 schema。 |
| java-base-mcp.fetchConfluenceContent / fuzzyMatchingConfluence | 原始需求证据获取；遵循来源资源中的已知参数及实时schema |
| ask_user_question | 缺失事实/真实选择/设置、当前内容确认或有界恢复决定；不伪造用户确认 |

这些 query 在 generation 组装 Story 时完成。正常 SDLC 不向用户收集 staffId，不在模板或新 profile 中新增 staffId，也不创建 staff-id-fallback 身份键。表中只列业务输入，不宣称实际后端身份字段已删除：query 如需身份，只能使用可信平台实际提供且 schema 接受的值；缺少工具身份注入是技术 WAITING_TOOL，不能用 Reporter 问题的答案代替、解析 token、猜身份或以此重复完整查询。只有实际项目/操作要求 Reporter 且缺少已验证可用值时，才问一次该字段所需 staff ID，并按真实 schema 解析其原生值；不能假设 staff ID 等于 reporter.name/accountId。旧共享配置字段与其他项目条目保留，staffId 缺失不影响已验证用户作用域内的正常配置复用。身份不写入 Story JSON 根级或评分/导出参数；fields.reporter 只在实际必需或用户明确要求、且有适用有效值时提交，更新省略不清空原值。query 的 almType 不自动成为 JSON 字段名。根级 projectKey 与 fields.project.key 一致，issueType 是确认的类型名，fields.issuetype.id 是实际 ID；字段名大小写固定。根级 acceptanceCriteria 是非空字段 ID 字符串，正文放 fields[该ID]；description/AC 已是 Jira wiki，不留到导出时转换。其他获准业务字段继续按实际字段 ID/类型/选项放 fields；不再使用 customFields 包装或旧顶层 summary/description/BDD/AC 文本数组。持久配置按用户/项目/type/操作能力复用，缺项定向查询，不按 TTL 强刷。规范见 [前置组装](ceaia-sdlc-story-spec-generation/references/jira-json-assembly.md) 与 [文本编码](ceaia-sdlc-story-spec-generation/references/jira-text-format.md)。

创建与更新都先读取真实用户 Workspace 的 profiles.json，按相同用户/source/project/type 复用稳定项目、类型和 AC 字段 ID 等组件。已有 create profile 不因本次 update 就失效；权限、requiredness 与默认值保留原操作范围，不将 create metadata 当 edit 权限。配置与 baseline 信息足够时，三个 discovery query 均不调用；只有缺失/不适用组件、明确刷新/目标变更或实际 schema 错误时定向查询。getJiraInfos/readJiraAttachments 的当前票据/原 SPEC 读取仍必要，它们不是这些字段 discovery query。

更新目标取 JSON 根级实际 `issueIdOrKey`，并与确认的 source、target 和 baseline 一致。创建省略该键。业务变更仍同步本地 SPEC/测试/投影。更新默认追加经评审的 SPEC，原附件全部保留。明确不改远端附件时用 `attachments=[]` 并省略 `attachmentsJson`。评分、预览和导出使用同一路径。

平台文件能力可以是普通 Workspace 工具或当前代理实际可执行的原生保存、打开、读取动作：只要它们按真实 schema 选择用户 Workspace，或工具默认就是当前认证用户空间，就用于 profile 读写；复用本轮已成功的用户 Workspace 操作绑定。无需名称含 user-level 的专用 API、单独 read-back 工具、额外 scope 返回字段或数据库证明；读回就是正常读取刚保存的文件。先使用可用操作读取目标，not-found 时按实际目录工具或自动建父目录能力创建有意义的 JSON；没有可见工具调用不等于不支持或已保存；聊天声明、路径和代码块不足为据，不猜隐藏 API 或原生指令。仅在检查实际可用机制及结果后报告具体 read/write/read-back 失败，不按工具命名猜 unavailable。写入成功只证明可写，未读回不能报 saved，未知旧文件不可覆盖；会话目录仍不能冒充用户空间。独立只读 reviewer、.json4jira 内容预览和当前用户确认能力保留。Task 的十个业务工具位置不变，平台文件工具遵循其实际暴露方式，不杜撰工具名/参数。

## 独立包与未确认范围

jira-createissue-helper 独立通用导出已同步本次用户配置读写/引导规则，共用 profiles 文件但不加入 SDLC 三 Skill Task，也不作为其 fallback。user-story-reviewer、review-requirements 未改。各包的评分及业务行为仍按其自身契约；共用配置规则不代表全平台后端集成已通过。

当前维护分支保留已有票根级目标和三参数评分。导出仍使用同一 JSON 路径，附件计划仅支持 add 或明确 none。记录实际成功、部分和未知结果。平台验收见 [测试清单](PLATFORM-TEST-CHECKLIST.md)。
