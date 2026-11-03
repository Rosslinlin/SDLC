# SDLC JSON 工具契约 — 6.1.0-preview.6

更新日期：2026-11-03。文件路径沿用旧名；当前内容只定义本 feature 分支的 SDLC JSON 路线。目标模型为 GPT-5.6 Luna / GPT-6 Luna，纯文本调用已有平台能力，不运行脚本。

## 权威来源和边界

- 新后端截图说明 `.json4jira` 文件驱动、服务端格式校验/checksum/评分保存、创建时自动关联及异步同步队列。
- 用户随后明确：评分工具改名 **scoreUserStory**，仍传 **JSON 路径 + SPEC 状态 + SPEC 路径**；导出工具仍为 **exportJiraByDynamicFields**，只传该 JSON 路径；Story 分数暂不写入 SPEC。这些澄清优先于截图中的评分单参数样例及示意名称 create_jira。
- 2026-11-03 用户向开发确认的最新格式：根级 acceptanceCriteria 直接存实际 AC 字段 ID，不再有 customFields 包装或 fields.reporter/root staffId；AC Jira wiki 在 fields[acceptanceCriteria]。创建省略 issueIdOrKey；更新在 jiraInstance/projectKey/issueType 后加入根级 issueIdOrKey（大写 I），值为确认的现有票号。Description/AC 在生成时完成 Jira wiki 编码，SPEC 保留 Markdown 语义投影；字段 ID、查询身份与项目取真实上下文/metadata。
- 工具实时 schema 是实际调用依据。缺失的返回字段、更新/替换编码和恢复能力不能由调用方编造。完整后端评分权重没有提供；Q1–Q7 是共享内容标准，不是替代评分器。

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

创建后端负责核评分记录/checksum/阈值、Jira/附件写入、匹配记录关联和同步任务入队。必须分别记录实际 issue、attachments、databaseLink、scoreSyncQueue 和 jiraScoreSync 结果；这些是本地规范化事实，不要求后端返回这些同名字段。普通成功或票号不能证明落库关联/入队全部完成。异步 PENDING 不等于 Jira 分数字段已同步。

旧 Agent B 调用不属于本路线。后端关联/队列失败时不能重建票据或重传成功附件，更不能用旧五字段调用试探补救。未知结果先通过实际支持的只读能力核对，无可用恢复接口就保留明确 pending/unknown。详见 [导出协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md)。

## 生成阶段的字段/证据工具

| 工具 | 用途 / 已知输入 |
| --- | --- |
| java-base-mcp.queryJiraProjectsByName | 生成 JSON 前确认项目；staffId、almType、projectName |
| java-base-mcp.queryJiraIssueTypesByProject | 确认真实 Story 类型；staffId、almType、projectKey |
| java-base-mcp.queryJiraCreateMetaFields | 字段required/type/options/AC等；staffId、almType、projectKey，及issueType或issueTypeId |
| java-base-mcp.getJiraInfos | 原票 Description/AC与可用事实、首次新Epic验证或必要异常核对；其余参数遵循实时schema |
| java-base-mcp.readJiraAttachments | 更新的完整附件清单、原ID及可读证据；遵循实时schema |
| java-base-mcp.fetchConfluenceContent / fuzzyMatchingConfluence | 原始需求证据获取；遵循来源资源中的已知参数及实时schema |
| ask_user_question | 缺失事实/真实选择/设置、当前内容确认或有界恢复决定；不伪造用户确认 |

这些 query 在 generation 组装 Story 时完成。现有 query 参数 staffId 与 profile 身份保留，不写入 Story JSON 根级或评分/导出参数；创建与更新均不提交 fields.reporter，旧 profile/基线也不能把它带回。query 的 almType 不自动成为 JSON 字段名。根级 projectKey 与 fields.project.key 一致，issueType 是确认的类型名，fields.issuetype.id 是实际 ID；字段名大小写固定。根级 acceptanceCriteria 是非空字段 ID 字符串，正文放 fields[该ID]；description/AC 已是 Jira wiki，不留到导出时转换。其他获准业务字段继续按实际字段 ID/类型/选项放 fields；不再使用 customFields 包装或旧顶层 summary/description/BDD/AC 文本数组。缓存按用户/项目/type/操作能力复用，缺项定向查询，不按 TTL 强刷。规范见 [前置组装](ceaia-sdlc-story-spec-generation/references/jira-json-assembly.md) 与 [文本编码](ceaia-sdlc-story-spec-generation/references/jira-text-format.md)。

创建与更新都先读取真实用户 Workspace 的 profiles.json，按相同用户/source/project/type 复用稳定项目、类型和 AC 字段 ID 等组件。已有 create profile 不因本次 update 就失效；权限、requiredness 与默认值保留原操作范围，不将 create metadata 当 edit 权限。缓存与 baseline 信息足够时，三个 discovery query 均不调用；只有缺失/不适用组件、明确刷新/目标变更或实际 schema 错误时定向查询。getJiraInfos/readJiraAttachments 的当前票据/原 SPEC 读取仍必要，它们不是这些字段 discovery query。

更新目标由用户确认的新契约确定：JSON 根级 issueIdOrKey 必须与该候选真实 source/target/baseline 一致；创建完全省略。这个值不是额外工具参数，两种模式仍按同一 JSON 路径评分、预览和导出。Story 业务变更自动同步所有受影响本地 SPEC/测试/投影，不等用户说 update spec；默认已有相关远端 SPEC→replace，无原 SPEC→首次 add，只有明确不改远端才 none/attachments=[]，本地仍更新并经 specFilePath 评分。最终预览展示实际路径/原件映射。replace 未知编码与先上传后删除保证仍按真实证据处理，不能默默跳过 SPEC、猜控制或回退创建。实际后端拒绝保留精确契约缺口，不额外索取普通 none 的 score-only 能力证明。

平台另需真实 Workspace 读写、独立只读 reviewer、.json4jira内容预览和当前用户确认能力。Task保留10个命名工具位置，最后评分工具替换为scoreUserStory；不为这些平台能力杜撰工具名或模型参数。

## 独立包与未确认范围

jira-createissue-helper、user-story-reviewer、review-requirements 的文件保持不动。其原有 Markdown、四/五字段或通用导出协议属于各自已有包，**不作为本 JSON 路线的 fallback**，也不代表已验证其对新全局后端的兼容。截图提及的全平台回归不扩大本次仅 SDLC 的修改范围。

已有票根级目标与三参数评分已确认；SPEC 的 add/replace/explicit none 计划、上传列表、批量原子性、幂等/超时核对、分项返回和异步状态仍按实际接口处理。所需替换能力缺失时准确保留待处理项，不捏造支持或宣布远端 SPEC 已同步。平台验收见 [测试清单](PLATFORM-TEST-CHECKLIST.md)。
