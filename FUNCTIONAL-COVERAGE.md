# v3 功能保留与调整对照

对照原始版 jira-sdlc-skills，而不只是 v2。目标是恢复具体业务约束和示例，集中管理重复规则；不以“越短越好”为目标。静态核对不能证明真实平台功能完全等价。

路径前缀：G = ceaia-sdlc-story-spec-generation；R = ceaia-spec-review；P = ceaia-sdlc-only-jira-push-content。

| 原有能力或问题 | v3 处理 | 主要落点 |
|---|---|---|
| 三 Skill 分阶段协作 | 保留生成→独立评审→Jira，helper 不替换 review | 三个 SKILL.md、task |
| 新建/已有票更新 | 保留分流，更新不能暗中转新建 | G/references/existing-jira-update.md |
| 逐票明确 Jira source | 保留五个来源选项，不按前缀猜、不自动换源 | G/references/source-enrichment.md |
| Description、AC 与附件 | 分开读取；Description 非空时独立 AC 缺失不阻断 intake | G/references/existing-jira-update.md |
| 多源需求证据 | 保留需求、UX、FSD、API、数据读取和来源分类 | G/references/source-enrichment.md |
| 拆分和规划 | 恢复细化证据、独立交付、INVEST、排除项检查 | G/references/business-planning-checklist.md、templates/planning-template.md |
| 审计/欺诈/留存/MI/下游/分层范围 | 明确逐项检查，仅纳入来源支持的要求 | G/references/business-planning-checklist.md |
| Story BDD | 保留 AC 编号和 Given/When/Then，标题改为精确 Acceptance Criteria | G/templates/story-template.md |
| 评分工具迁移 | 只接受真实工具分数，不再使用旧内置模型评分与旧总体分字段 | G/references/scoring-tool-contract.md |
| 76 分阈值 | 保留；75 以下失败，ok=true 不等于通过 | G/references/planning-and-scoring.md |
| AC 漏读与解析问题 | 高分但缺 AC 警告、计数冲突或解析错误不能放行 | G/references/scoring-tool-contract.md、references/validation.md |
| 新旧返回诊断字段 | 支持 sectionScores 和 scoreBreakdown/dimensions；保留完整当前原始结果 | G/references/scoring-tool-contract.md |
| 缓存与审计 | 默认不强制刷新；本地覆盖不会删除服务端审计 | 同上 |
| 模板元数据 | 恢复有来源的 Author/Branch/RequirementID/ParentID 等，不造未知值 | G/templates、references/metadata-and-examples.md |
| 已批准设计/架构链接 | 保留已提供且适用的链接，与禁止泄露内部出处区分 | G/templates/spec-template.md |
| Test Case 列与分类 | 保留原 11 列，恢复正向/负向/权限等示例和适用条件 | G/templates/test-case-template.md、metadata-and-examples.md |
| or 分支与组合 | 保留独立可执行用例；不按词数机械推导组合，不造无证据分支 | G/references/artifact-generation-rules.md |
| SPEC 结构与嵌入 | 保留完整结构、Story/测试同步及适用图示 | G/templates/spec-template.md、metadata-and-examples.md |
| 用户明确文件名 | 默认 kebab-case；恢复经确认的安全 .md 名称例外 | G/references/workflow-contract.md |
| 多个不同原附件 | 支持多个独立 reviewed SPEC 逐 ID 替换；多合一需明确确认 | G/R/P 对应 update 引用 |
| Jira 原内容保留 | 基线、差异、选择性字段与附件替换，不覆盖无关内容 | G/R/P 对应 update 引用 |
| 独立只读评审 | 保留来源、拆分、可测性、跨产物和保留风险检查 | R/references/core-review-gates.md、review-checklist.md |
| 评审字段 | 保留计数、分类问题、coverage、拆分建议、changeSummary 等名称 | R/references/review-report-template.md |
| SPEC 不通过返工 | 局部修复；Story 未变不重评，Story 字节变化必须重评分 | G/references/workflow-contract.md |
| 三轮 AI 修复 | 每候选/证据版本共享预算；无进展提前停，可诊断/补证据/有限追加 | 同上 |
| 生成历史 | 只保留当前产物和紧凑状态；保留原始证据与基线 | 同上 |
| 十个 Story 批量 | 状态/预算/路径隔离；独立工作可继续，全部选中项 PASS+全局覆盖才推 Jira | 同上、batch-state-template.json、task |
| 显式 Epic + Stories | 恢复字段映射及合并创建；不默认新增 Epic | P/references/work-item-field-mapping.md |
| 精确预览和审批 | 保留 request_user_approval、requestJson 和字段示例，变更使审批失效 | P/references/approval-interface-examples.md、ceaia-jira-write-gate.md |
| 部分写入/未知结果恢复 | 逐票记录，不重复已成功项，不虚报全成功 | P/references/ceaia-story-spec-update.md、task |
| 独立 UAT/Test Case 和通用导出 | 原 common/generic/UAT 三文件字节不变；CEAIA 规则隔离 | P/references/ceaia-jira-write-gate.md |
| jira-createissue-helper | 全部 15 文件字节不变，不接入本次 task | helper 全目录 |

## 兼容边界

- 已同意的行为变化包括局部返工、当前版本覆盖、修复预算与工具评分。
- task 平台 schema 仍为 enriched-agent.task/v2，产物状态/报告为 contractVersion 3；两者不是同一版本号。
- 旧评审字段名称虽保留，结构与含义有调整；原 v3 哈希字段已改成纯文本回读与版本记录。平台自定义解析器必须核对，不能据同名宣称完全兼容。
- 本版为纯文本执行：无脚本、无本地计算哈希要求。通过已保存内容的回读、实际修改记录和紧凑版本计数检查当前性；这不等于密码学或原子性保证。无法确认内容时仅重走受影响门禁，不因为平台没有脚本能力而阻塞。
- 短入口负责路由到必读资源，不代表引用资源可跳过。三个 Skill 的关联资源应一起部署，或支持按 Skill 名称+资源路径访问。
- 合并重复表达后 Markdown 仍比原版少；行数不证明功能完整或 Luna 成功率。需要实际平台验收。

详见 [版本说明](RELEASE-NOTES.md) 与 [平台验收清单](PLATFORM-TEST-CHECKLIST.md)。
