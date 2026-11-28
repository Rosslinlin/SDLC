# v6.1 JSON4Jira flow 功能归属 — preview

版本 `6.1.0-preview.10`，当前主分支为 `main`；2026-11-07 合入原 `codex/sdlc-json4jira-flow` 重构版本。本表描述路径接口的职责与待验证边界，不代表真实后端已全部通过。

| 能力 | 唯一职责 / 参考 | 当前行为与边界 |
| --- | --- | --- |
| 主链、状态、失效与续跑 | generation [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md) | Task/普通对话同序；所有用户默认 Jira-backed 完整流程，入口不提供路线选择；最终已评分内容仍须 HTML 预览与明确确认。逐候选准备，保持阻塞项，导出前核对全部选定范围。 |
| 默认入口与缺项补齐 | generation intake/assembly | 先读真实 Workspace 配置/可信身份，实际缺少适用 saved profile 即首次引导，来源/项目已给也不能代替可选字段的实际回答/明确跳过；只记录一个 optionalFieldsDecision，不新增审查循环。首次提供的受支持附加字段默认留作后续同项目/类型/操作设置，明确“仅本次”除外；已有配置生成前展示字段名/值并确认沿用/修改/切项目。类型/schema/AC ID 由查询工具取得，不要求业务用户填技术 ID。缺信息保留默认 Jira 意图，不推荐 preparation-only；仅遵从用户主动明确的只准备/不导出，且不伪造完整可评分 JSON。 |
| 固定内容标准 | generation [quality-contract](ceaia-sdlc-story-spec-generation/references/quality-contract.md)；review 内同文副本 | Q1–Q7 不另增标准；检查 JSON 业务内容、SPEC 投影和测试，不增加本地评分或第四个 Skill。 |
| 来源与拆分 | generation [source-enrichment](ceaia-sdlc-story-spec-generation/references/source-enrichment.md) / [authoring](ceaia-sdlc-story-spec-generation/references/artifact-generation-rules.md) | 原始要求有明确去向；不从 UI 推断后台，不按 AC 数拆票，不发明缺失业务事实。 |
| 前置项目/type/meta 与持久配置 | generation | create/update 均先读真实 user Workspace profiles.json，同用户/source/project/type 的稳定项目/类型/AC 映射按组件跨模式复用，不因旧 profile 只有 create capability 重跑三 query。权限、requiredness、操作/default 按模式区分；缺项、不适用、显式刷新/目标变化或真实 schema 错误才定向 query，不按 TTL 刷新。baseline/附件读取独立保留；export 不重新发现字段。 |
| JSON 字段结构与取值 | generation assembly/precheck；reviewer 读取 | 根级为 `jiraInstance/projectKey/issueType` 和实际 AC 字段 ID 字符串 `acceptanceCriteria`；不使用 `customFields` 包装或根级 `staffId`。`fields.reporter` 仅在实际项目/操作要求且有已验证值，或用户明确要求更改时，按真实 schema 加入；更新省略则保留原 Reporter。正常流程不问 staffId，仅在必需 Reporter 无已验证值时问一次，再解析为原生字段表示；工具身份仍由可信平台上下文提供，staffId 不作 profile 身份键。Summary 和其他获准业务字段在 `fields`；Description、AC 分别在根级 `descPath`、`acPath` 引用的两个独立 `.jirawiki` 文件中。project/issuetype 与实际目标一致；取消旧顶层 summary/description/AC 正文或数组/BDD，不硬编码示例 ID。结构问题回到生成处理，不增加 Q1–Q7。 |
| Jira 文本格式 | generation assembly/precheck | 评分前将 description、映射 AC 及 metadata 确认的其他获准富文本写成 Jira wiki：标题 `h1.`/`h2.`，AC 如 `* *AC-001*: Given ...`；保留措辞、顺序、换行、代码及每条 AC。普通文本、summary、身份/路由/选项和未改动更新字段不转换；export 不再加工。 |
| 单一 Story 业务源 | generation 模板及 authoring | 每候选一个最终 `story.json4jira` 及其引用的两个独立 `.jirawiki` 文件，共同构成权威 Story 包。业务内容和获准的提交/附件配置由 live schema 约束，不能凭旧 direct 参数猜 JSON 字段。 |
| 创建/更新路由 | generation create/update JSON 模板；exporter 核对 | 创建省略 `issueIdOrKey`；更新在 issueType 后加入该根级字段（大写 I），值为实际已确认票号，与 source/target/baseline 一致。两种操作共用 JSON 路径评分、确认和导出；工具参数不增加票号，更新不能省略目标回退创建，返回 key 必须匹配。 |
| 测试与 SPEC | generation | Story 业务变化自动同步本地 SPEC、受影响 test-case.md 和 Markdown 投影，无需用户提 update spec；每个相关 SPEC 保留原有义务、读回并与当前 JSON 对齐。纯路由/schema 只核对，不制造业务改动。SPEC 不显示分数。 |
| 独立内容审查 | review [contract](ceaia-spec-review/references/review-contract.md) / [report](ceaia-spec-review/references/review-report-template.md) | 独立读取原来源和所有当前 SPEC；按需读取 Story JSON、两个引用 Wiki 文件及测试作为对照，返回 SPEC 的 Q1–Q7 内容结论及绑定，不评分、不管理预算/导出就绪、不把机械偏好升级为业务阻塞。 |
| 局部修复与澄清 | generation workflow-contract / [clarification](ceaia-sdlc-story-spec-generation/references/user-clarification-and-resume.md) | review/score 共用两批内容修复；每个机械原因另限一次，仍保持变更失效。缺事实只问所需事实，续跑不重置计数。 |
| 路径评分与完整记录 | generation [scoring-tool-contract](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) | `scoreUserStory(story_json_workspace_path, specFileStatus, specFilePath)`；真实输入/完整响应保留，同一候选的最终评分路径必须等于导出路径。 |
| 评分与确认失效 | generation + exporter | JSON、任一引用 Wiki 文件、已评分 SPEC、附件选择或目标字段改变后更新受影响投影/测试、执行所需独立复审和评分，并取得新内容确认。不得以旧 path 字符串相同代替当前内容有效。 |
| 前端 HTML 预览与明确确认 | exporter [handoff](ceaia-sdlc-jira-export/references/handoff-and-content.md) | 展示本次已评分业务内容、目标和附件操作；用户明确确认当前内容后才导出。初始意图、提前设置确认或旧确认不能替代。 |
| 单路径导出 | exporter [write-and-recovery](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) | `exportJiraByDynamicFieldsV2` 只传 `story_json_workspace_path`；根级 `attachmentsJson` 对象在已评分文件内，不是额外工具参数。不再发送 direct fields、旧 `issuesJson`、五字段 B 或 `requestJson`。 |
| 后端评分检查与写入 | 新导出后端 | 校验 score checksum/threshold，写入 Jira 及要求附件，回填数据库关联、入异步队列；本仓库保存真实请求/响应，不能凭一般 success 代替分项证据。 |
| 分项结果 | exporter | issue、attachments、linkage、queue、async sync 各自记录真实成功/失败/未知/待处理。入队不等于 Jira UI 分数同步完成；后端未确认时不得补造完成状态。 |
| 批量执行与恢复 | exporter | 每候选一个 JSON、默认逐票导出，保持选定顺序与个体结果。失败/partial/unknown 按契约停止或核对；不重放未知/成功写入，不因关联/队列问题重建票据。 |
| 现有票与附件边界 | generation [existing-jira-update](ceaia-sdlc-story-spec-generation/references/existing-jira-update.md)；exporter | 保留 source+target、baseline/AC 和附件证据。更新时将当前已评审 SPEC 作为新附件上传，使用根级 `attachmentsJson.add` 的实际路径和文件名。所有原附件均保留。 |
| 必须上传 SPEC | generation + exporter | 创建和更新均要求非空的 `attachmentsJson.add`，至少上传一份已评审的当前 SPEC。更新没有 SPEC 正文改动时上传已评审的主 SPEC。 |
| 附件追加确认与结果 | generation assembly；exporter 结果核对 | 预览展示新增文件及保留的旧件。缺少有效新增计划的待提交包返回准备、评审、评分和确认。分开记录 Story 写入和每项新上传的实际结果，保留已成功的操作证据。 |
| 旧会话/接口迁移 | generation workflow-contract | 保留原状态、编号记录、消耗计数及全部远端结果；旧 Markdown 评分/五字段绑定不能当新 JSON 评分证据。迁移不自动重放外部操作。 |

移除的是独立 Story Markdown 及派生分数显示链，不是评分与数据库保存职责。新接口由后端承担的 checksum 检查、关联与队列处理必须以实际证据分别确认；不再由前端额外执行旧 S2/B。

独立 `jira-createissue-helper` 已同步本次持久配置规则，仍不加入 SDLC Task 或作为其 fallback；`user-story-reviewer` 按路径版 Story 包评分及交接，`review-requirements` 在相关 Story 输入参与评审时读取实际 Wiki 正文。真实平台、两种 Luna 模型及后端验证结果统一记录在 [测试清单](PLATFORM-TEST-CHECKLIST.md)，旧 v6 本地检查仅为历史记录。

## 2026-11-07 preview.8 配置生命周期补充

- SDLC 与通用 helper 的 create/update（含完整上游 JSON、批次子项）都先读真实 user Workspace；成功 issue 均进入合并保存/读回，其他远端效果部分失败不绕过配置收尾。
- profiles 数组兼容旧 Story 条目，按用户/source/project/issueType/capability 隔离多项目/类型/操作；`confirmedDefaults` 按真实字段 ID 保存首次受支持的附加字段名/值，明确仅本次的值不保存，临时覆盖不替换原默认；业务内容不作为默认。
- 首次 source → project → 可跳过附加字段；已有配置创建前确认沿用/修改/切项目，当前 run 确认覆盖未变批次及续跑。SDLC 固定 Story，通用导出支持实际其他类型。
- profilePersistence 明确 saved/未完成；saved 依赖真实用户作用域读回有意义条目。保存失败只恢复配置，不重放任何远端成功/未知操作。

## 2026-11-08 preview.9 身份处理

SDLC 正常入口、字段发现、评分、导出和配置持久化不索取 staff ID。仅实际项目/操作要求 Reporter 且无已验证值时，为该业务字段问一次所需身份，并按实时 schema 解析原生 Reporter 值；工具身份仍只能由平台提供，身份注入缺失属于技术缺口。已有完整配置不因无 staffId 重查；无 Reporter 要求时解除旧 staffId 待答问题，保留其他业务问题与所有远端结果。

## 2026-11-09 preview.10 用户 Workspace 能力复用

普通工具或当前代理可执行的原生文件动作支持实际用户目的地即可用于 profile；可见工具记录的有无不能单独证明能力或保存成功，复用已成功操作，读回是正常文件读；不要求专用 API 名称或 scope 返回。真实 not-found 用现有目录工具或自动父目录写入创建 populated profile。保存必须先于 issue 成功后的退出；写与读的缺口分别报告，不能按名称猜 unavailable。已创建 issue 的 profile-only 恢复不补提问、不重评分或重放 Jira。
