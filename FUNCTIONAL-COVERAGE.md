# v6.0 功能归属

当前活动包：`ceaia-sdlc-story-spec-generation`、`ceaia-spec-review`、`ceaia-sdlc-jira-export`。运行目标为平台 GPT-5.6 Luna / GPT-6 Luna，全部采用文本资源和现有工具。

| 能力 | 唯一职责 / 参考 | v6 行为 |
| --- | --- | --- |
| 主链、状态、失效与续跑 | generation [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md) | Task/普通对话同序；逐候选串行准备含 review/score，全部选定候选和整批覆盖通过后导出；默认全流程。 |
| 固定内容标准 | generation [quality-contract](ceaia-sdlc-story-spec-generation/references/quality-contract.md)；review 内同文副本 | Q1–Q7 在生成前加载并供独立审查复用；不新增 Skill，不计算本地分数。 |
| 来源与拆分 | generation [source-enrichment](ceaia-sdlc-story-spec-generation/references/source-enrichment.md) 和 [authoring](ceaia-sdlc-story-spec-generation/references/artifact-generation-rules.md) | 原始要求有明确去向；不从 UI 推断后台、不按 AC 数机械拆分、不发明缺失事实。 |
| 现有 Jira 更新 | generation [existing-jira-update](ceaia-sdlc-story-spec-generation/references/existing-jira-update.md) | 显式 source+target、原 Description/AC 状态、完整附件清单；保留义务，主链 none/replace，多份不同替换 SPEC 独立保留。 |
| 产物与生产检查 | generation 模板及 authoring | 最新 Story/Test Case/SPEC；11 列测试表、实际 `*-spec.md`、镜像和路径一致性集中检查，不能冒充业务审查标准。 |
| 独立内容审查 | review [contract](ceaia-spec-review/references/review-contract.md) / [report](ceaia-spec-review/references/review-report-template.md) | 独立读来源与当前内容；Q1–Q7、具体问题、全部 SPEC 绑定/保留结论；无评分、预算、readiness兼容字段或标题禁词门禁。 |
| 局部修复与澄清 | generation workflow-contract / [clarification](ceaia-sdlc-story-spec-generation/references/user-clarification-and-resume.md) | 每 candidate/evidenceRevision 两批内容修复共享 review/score；纯机械原因另限一次且保留失效规则。缺事实只问具体问题，续跑不重置预算。 |
| 真实评分及完整记录 | generation [scoring-tool-contract](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) | Workspace Story + 主 SPEC 的 Pass/path；实际 `ok=true`、数值 76–100 与 parser/AC有效；完整原始响应单调编号保留。 |
| SPEC 状态/分数及最终元数据 | generation [spec-score-display](ceaia-sdlc-story-spec-generation/references/spec-score-display.md) | S1 后写真实总分，仅必要时以正常缓存 S2 完成最终元数据；不以显示值替代评分，也不删除持久化调用。 |
| handoff与字段传输 | exporter [handoff](ceaia-sdlc-jira-export/references/handoff-and-content.md) / [fields](ceaia-sdlc-jira-export/references/fields-and-rendering.md) | 核对当前证据，不重做业务review或常规远端intake；非AC与AC分字段，Jira wiki无损转换，保留AC后的章节。 |
| 跨会话配置 | exporter [workspace-profile](ceaia-sdlc-jira-export/references/workspace-profile.md) | 真实用户工作区精简profile；完整命中跳过发现查询，不按TTL刷新；每次确认实际设置，首次create解决Epic/no-Epic。 |
| 附件 | exporter [attachments](ceaia-sdlc-jira-export/references/attachments.md) | 仅授权的SPEC；replace先确认上传再删旧，逐原件映射，成功附件不重传。 |
| Jira A / 数据库 B | exporter [write-and-recovery](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) | A不带评分路径；每项A及附件成功后单独五字段B，验证markdownReview；逐项记录partial/unknown，失败不重放成功步骤。 |
| 旧状态迁移 | generation workflow-contract | 记录旧状态并保留来源、版本、计数、元数据及所有已知/未知远端结果；不靠调用重放迁移。 |

`planning-and-scoring.md`、`business-planning-checklist.md`、`validation.md` 的活动规则已归并到对应权威文件，不再作为独立必读清单。模板负责布局，内容标准负责质量，编排负责阶段和预算，工具协议负责调用及持久化。

独立包保持原文件：通用 Jira/Test Case 使用 `jira-createissue-helper`，独立评分使用 `user-story-reviewer`，独立需求/任务拆分审查使用 `review-requirements`。它们不替代本链路的评分或 reviewer；旧 SDLC gateway 仅在明确选择 legacy 时使用。平台集成证据按 [测试清单](PLATFORM-TEST-CHECKLIST.md) 记录，本文件不代表已完成实测。
