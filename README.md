# CEAIA SDLC v6.0

本项目把需求整理为可交付的 Story、Test Case、SPEC，并完成独立审查、真实评分和 Jira 导出。当前版本 `6.0.0`，更新日期 `2026-10-31`；目标平台可选 GPT-5.6 Luna 或 GPT-6 Luna。全部资源是 Markdown/JSON，运行时仅使用平台现有工具，不执行本地脚本。

用于平台“自然语言生成 workflow”的试验输入见 [SDLC 工作流自然语言说明](SDLC-WORKFLOW-NATURAL-LANGUAGE.md)。该文档记录功能分支 `6.1.0-preview.6` JSON 流程的完整规则，并固定来源版本；本分支现有 Skills/Task 的运行版本仍为上述 v6.0。

活动流程由三个 Skill 构成：

| Skill | 职责 |
| --- | --- |
| `ceaia-sdlc-story-spec-generation` | 来源读取、拆分、生成、自检、局部修复、状态与最终 Workspace 评分 |
| `ceaia-spec-review` | 独立读取原始来源和当前产物，按共享内容标准返回结论与问题 |
| `ceaia-sdlc-jira-export` | 字段配置、准确预览、Story/SPEC 写入及逐票评分记录关联 |

每个候选串行完成计划 → Story → 测试 → SPEC → 生产自检 → 独立 review → SPEC 状态同步 → 最终评分/分数显示。阻塞候选保持待办，其他独立候选继续准备；全部选定候选通过并完成整批来源覆盖核对后才导出。默认执行完整流程，即使请求没有写 push/export；只有明确 preparation-only/no-Jira、范围调整或停止会改变这一意图。阶段调用只完成指定阶段。唯一编排规则见 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)。

生成前读取 [Q1–Q7 共享内容标准](ceaia-sdlc-story-spec-generation/references/quality-contract.md)，review 包携带逐字相同的本地副本，不新增第四个 Skill。标准覆盖来源完整性、价值与范围、可验证 AC、相关场景、可执行测试、SPEC 一致性和已有票保留。review 不打分、不管理修复预算或 Jira readiness；文件名、11 列测试表等仍是生产/传输约定。字词偏好、`or` 或负向标签放在哪一行不构成业务失败。

每个 candidate/evidenceRevision 默认有 **2 个内容修复批次**，review 与评分共用；无进展时提前停止。保持业务含义的机械修正每个原因另限一次，仍须使受影响的 review/评分/预览重新有效。续跑保留计数和外部操作结果；这些本地额度不授权重放 Jira、附件或数据库操作。

评分与数据库协议保持分阶段执行：

| 步骤 | 必须保留的效果 |
| --- | --- |
| S1：真实评分 | 以最终 `STORY.md` 的 `relativePath`、已验证主 SPEC 的 `specFilePath` 和精确 `specFileStatus: Pass` 调用评分；完整响应保留。`ok=true`、数值 76–100 且 AC/解析有效才通过。 |
| S2：必要的最终元数据调用 | 真实分数写入 SPEC 后，若主 SPEC 的最终版本绑定改变，按 [分数显示协议](ceaia-sdlc-story-spec-generation/references/spec-score-display.md) 正常调用并允许缓存复用，保存最终 SPEC 元数据；不强制重评或循环刷分。 |
| A：Jira 内容与附件 | 精确预览/read-back/preflight 后直接写入，首次调用不带 `markdownReviewRelativePath`。 |
| B：逐票数据库关联 | A 及该票全部要求的附件成功后，再单独调用同一工具，恰好传 `staffId`、`almType`、返回的 `issueIdOrKey`、`conversationId`、最终评分 `STORY.md` 的 `markdownReviewRelativePath` 五个非空字符串；核对 `markdownReview` 确认结果。 |

内容 PASS 不保证外部评分达到 76；仓库没有完整后端评分权重。缓存命中不免除必要元数据保存，A 成功不替代 B；绑定失败只恢复缺失步骤。完整边界见 [评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 和 [Jira A/B 协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md)。

安装与升级：

1. 分别上传/更新三个活动 Skill，记录平台实际返回的 skill ID；保留每包自己的资源，保持两份质量标准一致。
2. 核对 Task 已配置的 generation/review ID，并将 exporter 的 `REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID` 替换为实际 ID，不能填旧 helper ID。本仓库不表示这些包已安装到平台。
3. 导入兼容路径 [SDLC-workflow-v5.task.json](SDLC-workflow-v5.task.json)；文件名保留，显示名为 v6，schema 不变。核对原有 10 个工具和最后的评分工具，在平台运行配置中选择 5.6 Luna 或 6 Luna，不添加未经确认的模型参数。
4. 按 [平台测试清单](PLATFORM-TEST-CHECKLIST.md) 分别记录两种模型的真实结果；升级旧状态按 workflow-contract 保留全部已知/未知远端结果，不能通过重放调用完成迁移。

导出仅处理 Story。更新主链支持附件 `none`/`replace`，不默认扩展 add/delete；Test Case 是工作区交付物，只有授权的 SPEC 作为附件。跨会话字段规则保存在真实用户工作区 `ceaia-sdlc-jira/profiles.json`，完整命中不按时间强制刷新，每次导出仍确认实际设置。

`jira-createissue-helper`、`user-story-reviewer`、`review-requirements` 保持独立，不加入本 Task。当前 SDLC handoff 交给专用 exporter；只有明确选择 legacy 流程才用旧 gateway，不因新 exporter 不可用而自动回退。详见 [功能归属](FUNCTIONAL-COVERAGE.md)、[工具参考](TOOLS-REFERENCE-2026-10-26.md) 和 [发布记录](RELEASE-NOTES.md)。
