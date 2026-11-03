# v6.1 JSON4Jira flow 功能归属 — preview

版本 `6.1.0-preview.4`，日期 `2026-11-03`，分支 `codex/sdlc-json4jira-flow`，未合入 `main`。本表描述新路径接口的职责与待验证边界，不代表真实后端已通过。

| 能力 | 唯一职责 / 参考 | 当前行为与边界 |
| --- | --- | --- |
| 主链、状态、失效与续跑 | generation [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md) | Task/普通对话同序；所有用户默认 Jira-backed 完整流程，入口不提供路线选择；最终已评分内容仍须 HTML 预览与明确确认。逐候选准备，保持阻塞项，导出前核对全部选定范围。 |
| 默认入口与缺项补齐 | generation intake/assembly | 先读真实 Workspace 配置/可信身份，只问尚缺的来源、staffId、项目名称/已知 key 或实际项目选择。类型/schema/AC ID 由查询工具取得，不要求业务用户填技术 ID。缺信息保留默认 Jira 意图，不推荐 preparation-only；仅遵从用户主动明确的只准备/不导出，且不伪造完整可评分 JSON。 |
| 固定内容标准 | generation [quality-contract](ceaia-sdlc-story-spec-generation/references/quality-contract.md)；review 内同文副本 | Q1–Q7 不另增标准；检查 JSON 业务内容、SPEC 投影和测试，不增加本地评分或第四个 Skill。 |
| 来源与拆分 | generation [source-enrichment](ceaia-sdlc-story-spec-generation/references/source-enrichment.md) / [authoring](ceaia-sdlc-story-spec-generation/references/artifact-generation-rules.md) | 原始要求有明确去向；不从 UI 推断后台，不按 AC 数拆票，不发明缺失业务事实。 |
| 前置项目/type/meta 与缓存 | generation | 在生成最终 JSON 前完成适用查询、确认字段类型/选项/必要值；命中真实用户工作区 profile 时复用，缺项定向查询；票据及要求附件确认成功后保存上游已确认快照；关联/队列结果另记。export 不另组装一套字段。 |
| JSON 字段结构与取值 | generation assembly/precheck；reviewer 读取 | 根级为 `jiraInstance/projectKey/issueType`，删除根级 `staffId`；身份配置和查询工具所需员工号仍按实际 schema 保留。业务值在 `fields`；`customFields.acceptanceCriteria` 仅映射实际字段 ID，AC 正文为 `fields[该ID]` 的 Jira wiki 字符串。project/issuetype 与实际目标一致，reporter 按 metadata 要求/授权使用真实身份；取消旧顶层 summary/description/AC/BDD，不硬编码示例 ID。结构问题回到生成处理，不增加 Q1–Q7。 |
| Jira 文本格式 | generation assembly/precheck | 评分前将 description、映射 AC 及 metadata 确认的其他获准富文本写成 Jira wiki：标题 `h1.`/`h2.`，AC 如 `* *AC-001*: Given ...`；保留措辞、顺序、换行、代码及每条 AC。普通文本、summary、身份/路由/选项和未改动更新字段不转换；export 不再加工。 |
| 单一 Story 业务源 | generation 模板及 authoring | 每候选一个最终 `story.json4jira`；取消独立 `STORY.md`。业务内容和获准的提交/附件配置由 live schema 约束，不能凭旧 direct 参数猜 JSON 字段。 |
| 测试与 SPEC | generation | 保留 `TEST_CASE.md`；SPEC 仍为 Markdown，Story 投影来自当前 JSON 中 Jira wiki 的相同业务语义，测试表来自当前测试文件，不直接复制 wiki 标记作为 Markdown。保留必要元数据与实际文件/引用一致性；SPEC 不显示分数。 |
| 独立内容审查 | review [contract](ceaia-spec-review/references/review-contract.md) / [report](ceaia-spec-review/references/review-report-template.md) | 独立读取原来源、JSON 的实际 fields 业务值和所有当前 SPEC/测试；先解析 AC 字段映射再读正文，不将字段 ID 当业务文字；返回 Q1–Q7 内容结论及绑定，不评分、不管理预算/导出就绪、不把机械偏好升级为业务阻塞。 |
| 局部修复与澄清 | generation workflow-contract / [clarification](ceaia-sdlc-story-spec-generation/references/user-clarification-and-resume.md) | review/score 共用两批内容修复；每个机械原因另限一次，仍保持变更失效。缺事实只问所需事实，续跑不重置计数。 |
| 路径评分与完整记录 | generation [scoring-tool-contract](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) | `scoreUserStory(story_json_workspace_path, specFileStatus, specFilePath)`；真实输入/完整响应保留，同一候选的最终评分路径必须等于导出路径。 |
| 评分与确认失效 | generation + exporter | JSON、已评分 SPEC、附件选择或目标字段改变后更新受影响投影/测试、执行所需独立复审和评分，并取得新内容确认。不得以旧 path 字符串相同代替当前内容有效。 |
| 前端 HTML 预览与明确确认 | exporter [handoff](ceaia-sdlc-jira-export/references/handoff-and-content.md) | 展示本次已评分业务内容、目标和附件操作；用户明确确认当前内容后才导出。初始意图、缓存设置或旧确认不能替代。 |
| 单路径导出 | exporter [write-and-recovery](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) | `exportJiraByDynamicFields` 只传 `story_json_workspace_path`；不再发送 direct fields、旧 `issuesJson`、五字段 B 或 `requestJson`。 |
| 后端评分检查与写入 | 新导出后端 | 校验 score checksum/threshold，写入 Jira 及要求附件，回填数据库关联、入异步队列；本仓库保存真实请求/响应，不能凭一般 success 代替分项证据。 |
| 分项结果 | exporter | issue、attachments、linkage、queue、async sync 各自记录真实成功/失败/未知/待处理。入队不等于 Jira UI 分数同步完成；后端未确认时不得补造完成状态。 |
| 批量执行与恢复 | exporter | 每候选一个 JSON、默认逐票导出，保持选定顺序与个体结果。失败/partial/unknown 按契约停止或核对；不重放未知/成功写入，不因关联/队列问题重建票据。 |
| 现有票与附件边界 | generation [existing-jira-update](ceaia-sdlc-story-spec-generation/references/existing-jira-update.md)；exporter | 保留 source+target、baseline/AC 状态、完整附件清单与 none/replace 语义。live schema 未暴露目标/替换语义时 WAITING_TOOL；不猜字段、不改成 create、不上传 score-only SPEC。 |
| 旧会话/接口迁移 | generation workflow-contract | 保留原状态、编号记录、消耗计数及全部远端结果；旧 Markdown 评分/五字段绑定不能当新 JSON 评分证据。迁移不自动重放外部操作。 |

移除的是独立 Story Markdown 及派生分数显示链，不是评分与数据库保存职责。新接口由后端承担的 checksum 检查、关联与队列处理必须以实际证据分别确认；不再由前端额外执行旧 S2/B。

三个独立包 `jira-createissue-helper`、`user-story-reviewer`、`review-requirements` 完全不改；本分支不扩写其旧工具路由。真实平台、两种 Luna 模型及后端验证结果统一记录在 [测试清单](PLATFORM-TEST-CHECKLIST.md)，旧 v6 本地检查仅为历史记录。
