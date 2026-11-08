# v6.1 JSON4Jira flow 功能归属 — preview

版本 `6.1.0-preview.9`，当前主分支为 `main`；2026-11-07 合入原 `codex/sdlc-json4jira-flow` 重构版本。本表描述路径接口的职责与待验证边界，不代表真实后端已全部通过。

| 能力 | 唯一职责 / 参考 | 当前行为与边界 |
| --- | --- | --- |
| 主链、状态、失效与续跑 | generation [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md) | Task/普通对话同序；所有用户默认 Jira-backed 完整流程，入口不提供路线选择；最终已评分内容仍须 HTML 预览与明确确认。逐候选准备，保持阻塞项，导出前核对全部选定范围。 |
| 默认入口与缺项补齐 | generation intake/assembly | 先读真实 Workspace 配置/可信身份，首次只补缺失来源/项目并增加可跳过附加字段引导；已有配置生成前明确确认沿用/修改/切项目。类型/schema/AC ID 由查询工具取得，不要求业务用户填技术 ID。缺信息保留默认 Jira 意图，不推荐 preparation-only；仅遵从用户主动明确的只准备/不导出，且不伪造完整可评分 JSON。 |
| 固定内容标准 | generation [quality-contract](ceaia-sdlc-story-spec-generation/references/quality-contract.md)；review 内同文副本 | Q1–Q7 不另增标准；检查 JSON 业务内容、SPEC 投影和测试，不增加本地评分或第四个 Skill。 |
| 来源与拆分 | generation [source-enrichment](ceaia-sdlc-story-spec-generation/references/source-enrichment.md) / [authoring](ceaia-sdlc-story-spec-generation/references/artifact-generation-rules.md) | 原始要求有明确去向；不从 UI 推断后台，不按 AC 数拆票，不发明缺失业务事实。 |
| 前置项目/type/meta 与持久配置 | generation | create/update 均先读真实 user Workspace profiles.json，同用户/source/project/type 的稳定项目/类型/AC 映射按组件跨模式复用，不因旧 profile 只有 create capability 重跑三 query。权限、requiredness、操作/default 按模式区分；缺项、不适用、显式刷新/目标变化或真实 schema 错误才定向 query，不按 TTL 刷新。baseline/附件读取独立保留；export 不重新发现字段。 |
| JSON 字段结构与取值 | generation assembly/precheck；reviewer 读取 | 根级为 `jiraInstance/projectKey/issueType` 和实际 AC 字段 ID 字符串 `acceptanceCriteria`；不使用 `customFields` 包装，不提交根级 `staffId` 或 `fields.reporter`。身份由可信平台上下文提供，不收集 staffId，不在新 profile 中添加 staffId 后备键；旧共享配置保留兼容，缺少此字段不使 profile 无效。业务值在 `fields`；AC 正文为 `fields[acceptanceCriteria]` 的 Jira wiki 字符串。project/issuetype 与实际目标一致；取消旧顶层 summary/description/AC 正文或数组/BDD，不硬编码示例 ID。结构问题回到生成处理，不增加 Q1–Q7。 |
| Jira 文本格式 | generation assembly/precheck | 评分前将 description、映射 AC 及 metadata 确认的其他获准富文本写成 Jira wiki：标题 `h1.`/`h2.`，AC 如 `* *AC-001*: Given ...`；保留措辞、顺序、换行、代码及每条 AC。普通文本、summary、身份/路由/选项和未改动更新字段不转换；export 不再加工。 |
| 单一 Story 业务源 | generation 模板及 authoring | 每候选一个最终 `story.json4jira`；取消独立 `STORY.md`。业务内容和获准的提交/附件配置由 live schema 约束，不能凭旧 direct 参数猜 JSON 字段。 |
| 创建/更新路由 | generation create/update JSON 模板；exporter 核对 | 创建省略 `issueIdOrKey`；更新在 issueType 后加入该根级字段（大写 I），值为实际已确认票号，与 source/target/baseline 一致。两种操作共用 JSON 路径评分、确认和导出；工具参数不增加票号，更新不能省略目标回退创建，返回 key 必须匹配。 |
| 测试与 SPEC | generation | Story 业务变化自动同步本地 SPEC、受影响 TEST_CASE.md 和 Markdown 投影，无需用户提 update spec；每个相关 SPEC 保留原有义务、读回并与当前 JSON 对齐。none 仅控制远端附件，本地仍同步；纯路由/schema 只核对，不制造业务改动。SPEC 不显示分数。 |
| 独立内容审查 | review [contract](ceaia-spec-review/references/review-contract.md) / [report](ceaia-spec-review/references/review-report-template.md) | 独立读取原来源、JSON 的实际 fields 业务值和所有当前 SPEC/测试；先解析 AC 字段映射再读正文，不将字段 ID 当业务文字；返回 Q1–Q7 内容结论及绑定，不评分、不管理预算/导出就绪、不把机械偏好升级为业务阻塞。 |
| 局部修复与澄清 | generation workflow-contract / [clarification](ceaia-sdlc-story-spec-generation/references/user-clarification-and-resume.md) | review/score 共用两批内容修复；每个机械原因另限一次，仍保持变更失效。缺事实只问所需事实，续跑不重置计数。 |
| 路径评分与完整记录 | generation [scoring-tool-contract](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) | `scoreUserStory(story_json_workspace_path, specFileStatus, specFilePath)`；真实输入/完整响应保留，同一候选的最终评分路径必须等于导出路径。 |
| 评分与确认失效 | generation + exporter | JSON、已评分 SPEC、附件选择或目标字段改变后更新受影响投影/测试、执行所需独立复审和评分，并取得新内容确认。不得以旧 path 字符串相同代替当前内容有效。 |
| 前端 HTML 预览与明确确认 | exporter [handoff](ceaia-sdlc-jira-export/references/handoff-and-content.md) | 展示本次已评分业务内容、目标和附件操作；用户明确确认当前内容后才导出。初始意图、提前设置确认或旧确认不能替代。 |
| 单路径导出 | exporter [write-and-recovery](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) | `exportJiraByDynamicFields` 只传 `story_json_workspace_path`；根级 `attachmentsJson` 对象在已评分文件内，不是额外工具参数。不再发送 direct fields、旧 `issuesJson`、五字段 B 或 `requestJson`。 |
| 后端评分检查与写入 | 新导出后端 | 校验 score checksum/threshold，写入 Jira 及要求附件，回填数据库关联、入异步队列；本仓库保存真实请求/响应，不能凭一般 success 代替分项证据。 |
| 分项结果 | exporter | issue、attachments、linkage、queue、async sync 各自记录真实成功/失败/未知/待处理。入队不等于 Jira UI 分数同步完成；后端未确认时不得补造完成状态。 |
| 批量执行与恢复 | exporter | 每候选一个 JSON、默认逐票导出，保持选定顺序与个体结果。失败/partial/unknown 按契约停止或核对；不重放未知/成功写入，不因关联/队列问题重建票据。 |
| 现有票与附件边界 | generation [existing-jira-update](ceaia-sdlc-story-spec-generation/references/existing-jira-update.md)；exporter | 保留 source+target、baseline/AC 状态及完整附件清单。业务 Story 变更默认相关已有 SPEC→replace、无相关 SPEC→add。2026-11-06 用户实测确认文件内根级 `attachmentsJson` 对象沿用 `add`/`replace`：条目含 `relativePath`/`fileName`，replace 另含 `attachmentId`，原ID来自 `readJiraAttachments` 的同source/同票原件。先读原文确认SPEC身份，非SPEC、无关和未选项保留；distinct原件保留各自输出映射。使用该对象时禁止再用非空 `attachments` 数组重复上传。 |
| 显式保留远端附件 | generation + exporter | 用户明确不改远端附件才 none：`attachments: []` 且省略 `attachmentsJson`；本地SPEC/测试仍同步、审查，并通过 `specFilePath` 评分，无额外score-only能力证明。 |
| 附件替换确认与结果 | generation assembly；exporter结果核对 | 普通replace格式已确认，不再以schema未知或重复能力证明阻塞。最终预览展示实际原ID→输出及保留项；删除属于replace后端效果，不新增独立delete操作。真实服务错误、上传/旧件移除失败或未知仍分别记录，部分成功不称全完成，不重放已成功操作、不猜恢复或回退create。 |
| 旧会话/接口迁移 | generation workflow-contract | 保留原状态、编号记录、消耗计数及全部远端结果；旧 Markdown 评分/五字段绑定不能当新 JSON 评分证据。迁移不自动重放外部操作。 |

移除的是独立 Story Markdown 及派生分数显示链，不是评分与数据库保存职责。新接口由后端承担的 checksum 检查、关联与队列处理必须以实际证据分别确认；不再由前端额外执行旧 S2/B。

独立 `jira-createissue-helper` 已同步本次持久配置规则，仍不加入 SDLC Task 或作为其 fallback；`user-story-reviewer`、`review-requirements` 未改。真实平台、两种 Luna 模型及后端验证结果统一记录在 [测试清单](PLATFORM-TEST-CHECKLIST.md)，旧 v6 本地检查仅为历史记录。

## 2026-11-07 preview.8 配置生命周期补充

- SDLC 与通用 helper 的 create/update（含完整上游 JSON、批次子项）都先读真实 user Workspace；成功 issue 均进入合并保存/读回，其他远端效果部分失败不绕过配置收尾。
- profiles 数组兼容旧 Story 条目，按用户/source/project/issueType/capability 隔离多项目/类型/操作；只存已验证的路由/schema 和明确可复用字段，单次值与业务内容不作为默认。
- 首次 source → project → 可跳过附加字段；已有配置创建前确认沿用/修改/切项目，当前 run 确认覆盖未变批次及续跑。SDLC 固定 Story，通用导出支持实际其他类型。
- profilePersistence 明确 saved/未完成；saved 依赖真实用户作用域读回有意义条目。保存失败只恢复配置，不重放任何远端成功/未知操作。

## 2026-11-08 preview.9 身份处理

SDLC 入口、字段发现、评分、导出和配置持久化均不向用户索取 staff ID。查询按实时 schema 使用平台实际提供的身份，身份注入缺失属于技术缺口；已有完整配置不因无 staffId 重查。旧 staffId 待答问题解除，其他业务问题与所有远端结果保留。
