# CEAIA Skill Bundle v3.0.2（纯文本审批修订） — 交付与修改说明

2026-09-20 开始整理，2026-09-21 完成。状态：可供平台测试的候选版，尚未生产联调。

## v3.0.2 Jira 审批调用修复

本次只修改 `ceaia-sdlc-only-jira-push-content`，task 和前两个 CEAIA Skill 不变：

- 对照原始版确认：用户原先确实提供过 `request_user_approval` 的完整创建/更新参数结构，而不只是工具名称。
- 恢复创建与更新批次的 title、reason、action_type、target、details、按钮字段和 action_binding/arguments_preview。
- 强制 Luna 在最终预览后执行真实工具调用；不得把工具名或 JSON 当作聊天文字输出，也不得要求用户输入 continue/approve。
- 明确异步调用后结束当前轮并等待真实 decision；同步完成的 decision 可立即处理。
- 如果平台没有暴露审批能力，返回一次明确 WAITING_TOOL，绝不绕过审批写 Jira，也不反复只说工具名称。
- task 的 tools 数组未添加虚构 ID：原始 task 截图同样未把审批能力列入该数组。该能力应由平台作为内建工具暴露；如平台实际要求注册 ID，需要平台提供真实 ID/schema 后再配置。

## 本次纯文本平台修订

按用户补充的平台限制，直接修改 v3，不另建 v4：

- 删除产物校验器和两份包级测试脚本，共 3 个文件；移除对应空目录与执行命令。
- validation.md 改为分阶段的纯文本检查清单，仍检查评分、AC、用例、SPEC、独立评审与审批。
- 删除本地 SHA-256/脚本运行前提，状态和报告改为 readback-and-revision：读取实际内容、记录真实修改、确认当前结果。无脚本或哈希工具不再成为阻塞理由。
- task、生成/评审/推送规则与模板同步调整。JSON 是任务配置、状态模板或工具参数示例，不是可执行程序。
- 评分工具、业务门禁、局部修复和三轮预算保持。helper 和受保护 Test Case 资源保持原样。
- 纯文本检查不是自动化或密码学完整性保证。若无法确认当前内容，重走受影响检查/审批，不假装文件已被技术锁定。
- 删除的脚本可从交付目录之外的本地备份恢复；更新后的交付包不包含它们。

## 交付内容与使用

- 四个 Skill 独立放在 jira-sdlc-skills-v3-2026-09-20。原始目录、v2 和本机已安装 Skill 未修改，也未自动发布到平台。
- 主流程仍是 generation → ceaia-spec-review → bundled push；jira-createissue-helper 原样附带，不替换 review。
- [task JSON](SDLC-workflow-v3.task.json)：按截图重建并对齐 v3，不是旧 JSON 的逐字转录。平台 schema 保持 enriched-agent.task/v2，未擅自添加模型配置字段。
- [功能保留对照](FUNCTIONAL-COVERAGE.md)、[平台测试清单](PLATFORM-TEST-CHECKLIST.md)、[逐文件校验清单](VERSION.json)。

建议先按测试清单运行单个 Story、局部修复和已有票更新，再测十个 Story；通过后再替换生产。

## 这次重点修复

1. **AC 识别**：Story 模板使用精确标题 `## Acceptance Criteria`，保留 AC-001 与 Given/When/Then，不再使用合并标题。
2. **评分工具契约**：明确 nodejs-base-mcp.score_requirement_markdown，默认只传真实工作区相对 Markdown 路径。解析单个 JSON 文本内容块，保留当前完整响应，不再依赖旧内置评分格式。
3. **评分放行**：ok=true 不等于业务通过；保持 finalScore 至少 76、范围不超过 100，并检查解析错误、缺 AC 警告及提供的 AC 计数一致性。兼容资料中两种诊断字段结构；不手工改分或伪造缺失字段。
4. **业务内容补回**：补齐规划和分层范围检查、模板元数据、已批准设计/架构链接、用例分类与示例、明确 Epic+Stories 请求及字段映射。
5. **附件能力补回**：支持同一已有 Jira 的多个不同 SPEC，逐附件 ID 绑定、逐份评审，不能强制合并成一份。
6. **局部修复**：只改 SPEC 或测试不重新生成未变 Story、不重新评分；Story 内容变化则旧分失效，并同步受影响下游。正常同步 review header 不重复评分。
7. **当前版本与三轮预算**：覆盖当前生成产物，保留紧凑状态而非 Story 历史副本。每候选/证据版本的评分和评审共享三轮内容修复预算；连续无进展提前停，缺证据立即问；可读诊断、实质新证据或明确有限追加预算都有出口。
8. **批量和审批**：十个候选独立路径/状态/预算；阻塞项不妨碍无依赖工作，但所有选中项当前 PASS 且全局需求覆盖通过后才准备 Jira。审批绑定精确 payload 与附件内容，变化即失效。
9. **task 同步**：保留四阶段组织，评分工具追加在 tools 数组最后；实际调用在 Story 后、测试/SPEC 前，不是等最后才评分。删除旧评分与历史报告要求，避免与 Skill 冲突。

## 明确没有修改的范围

- jira-createissue-helper 全部 15 个文件逐字节保持原版。
- push 的 references/common-jira-write-gate.md、references/generic-work-item-export.md、references/uat-testcase-export.md 三文件逐字节不变。CEAIA 改造使用隔离引用，不补写其他项目的 Test Case 内容。
- helper 原有非 UTF-8 资源与 UAT 空文件均原样保留，不当作本次缺陷“修复”。
- 原始用户/Jira 证据及基线保留；只覆盖生成结果。本地覆盖不等于删除评分服务端的不可变审计记录。

## 内容增加还是减少

口径仅四个 Skill 目录，不含包级说明、测试、task、清单和 ZIP。Markdown 行数不等于 token；非文本原资源仅计入总字节。

| 指标 | 原版 | v2 | v3 |
|---|---:|---:|---:|
| 可读 Markdown 行数 | 4,277 | 2,895 | 3,288 |
| 前三个 CEAIA Markdown 行数 | 2,753 | 1,371 | 1,764 |
| 可读 Markdown 字节数 | 286,797 | 184,667 | 224,444 |
| 全部 Skill 文件字节数 | 296,013 | 215,481 | 236,320 |
| 文件数 | 36 | 42 | 46 |

| Skill | 原版 Markdown 行 | v2 行 | v3 行 |
|---|---:|---:|---:|
| ceaia-sdlc-story-spec-generation | 1040 | 545 | 781 |
| ceaia-spec-review | 743 | 249 | 284 |
| ceaia-sdlc-only-jira-push-content | 970 | 577 | 699 |
| jira-createissue-helper | 1524 | 1524 | 1524 |

纯文本修订的 Markdown 总行数为 3,288，较 v2 增加 393 行；前三个 CEAIA 的 Markdown 为 1,764 行，较 v2 增加 393 行，较原版减少 989 行。规则与示例仍在，减少来自重复表达合并；不能以篇幅证明 Luna 效果。JSON 状态模板保留为纯文本数据，不包含运行程序。

## 完整 Skill 文件变动（相对原版）

修改 18 个原文件、新增 10 个、18 个保持不变、0 个原版文件删除。相对 v2，Skill 文件总数增加 4 个；下列“新增”均以原始版为基线。

### ceaia-sdlc-story-spec-generation

- 修改：[SKILL.md](ceaia-sdlc-story-spec-generation/SKILL.md)
- 修改：[references/artifact-generation-rules.md](ceaia-sdlc-story-spec-generation/references/artifact-generation-rules.md)
- 修改：[references/existing-jira-update.md](ceaia-sdlc-story-spec-generation/references/existing-jira-update.md)
- 修改：[references/planning-and-scoring.md](ceaia-sdlc-story-spec-generation/references/planning-and-scoring.md)
- 修改：[references/source-enrichment.md](ceaia-sdlc-story-spec-generation/references/source-enrichment.md)
- 修改：[templates/planning-template.md](ceaia-sdlc-story-spec-generation/templates/planning-template.md)
- 修改：[templates/spec-template.md](ceaia-sdlc-story-spec-generation/templates/spec-template.md)
- 修改：[templates/story-template.md](ceaia-sdlc-story-spec-generation/templates/story-template.md)
- 修改：[templates/test-case-template.md](ceaia-sdlc-story-spec-generation/templates/test-case-template.md)
- 新增：[references/business-planning-checklist.md](ceaia-sdlc-story-spec-generation/references/business-planning-checklist.md)
- 新增：[references/metadata-and-examples.md](ceaia-sdlc-story-spec-generation/references/metadata-and-examples.md)
- 新增：[references/scoring-tool-contract.md](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md)
- 新增：[references/validation.md](ceaia-sdlc-story-spec-generation/references/validation.md)
- 新增：[references/workflow-contract.md](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)
- 新增：[templates/batch-state-template.json](ceaia-sdlc-story-spec-generation/templates/batch-state-template.json)
- 新增：[templates/state-template.json](ceaia-sdlc-story-spec-generation/templates/state-template.json)

### ceaia-spec-review

- 修改：[SKILL.md](ceaia-spec-review/SKILL.md)
- 修改：[references/core-review-gates.md](ceaia-spec-review/references/core-review-gates.md)
- 修改：[references/existing-jira-update-review.md](ceaia-spec-review/references/existing-jira-update-review.md)
- 修改：[references/review-checklist.md](ceaia-spec-review/references/review-checklist.md)
- 修改：[references/review-contract.md](ceaia-spec-review/references/review-contract.md)
- 修改：[references/review-report-template.md](ceaia-spec-review/references/review-report-template.md)

### ceaia-sdlc-only-jira-push-content

- 修改：[SKILL.md](ceaia-sdlc-only-jira-push-content/SKILL.md)
- 修改：[references/ceaia-story-spec-export.md](ceaia-sdlc-only-jira-push-content/references/ceaia-story-spec-export.md)
- 修改：[references/ceaia-story-spec-update.md](ceaia-sdlc-only-jira-push-content/references/ceaia-story-spec-update.md)
- 新增：[references/approval-interface-examples.md](ceaia-sdlc-only-jira-push-content/references/approval-interface-examples.md)
- 新增：[references/ceaia-jira-write-gate.md](ceaia-sdlc-only-jira-push-content/references/ceaia-jira-write-gate.md)
- 新增：[references/work-item-field-mapping.md](ceaia-sdlc-only-jira-push-content/references/work-item-field-mapping.md)

### jira-createissue-helper

全部 15 文件不变。另有 push 的上述三个资源不变。每个文件的哈希与完整清单见 VERSION.json。

### 包级文件

- 更新：RELEASE-NOTES.md、VERSION.json、FUNCTIONAL-COVERAGE.md、PLATFORM-TEST-CHECKLIST.md、SDLC-workflow-v3.task.json。
- 已移除包级 validation 目录及两个测试脚本；Skill 内 scripts 目录及产物校验脚本也已移除。
- 四个 Skill 共 46 文件，另有 5 个包级文本/JSON 文件；更新 ZIP 共 51 文件。

## 验证结果与边界

- 本次在编辑环境核对文件清单、JSON 示例、引用链接、纯文本模板/状态/task 一致性以及保护文件未变；这些检查不要求目标平台执行任何脚本。
- 上次提到的 76 项自动测试属于删脚本前的实现，不作为本次纯文本执行效果的证明。旧脚本已不随包交付。
- 未调用真实评分服务、Jira、审批 UI；未做平台 task 导入或 Luna 端到端验证。
- 之前独立场景核对因额度限制中断，不能计为完整独立审查通过。
- VERSION.json 的文件摘要仅为编辑端制作的包清单，不要求平台计算或核验摘要。
- 请用平台测试清单逐场景验证纯文本运行，尤其是 AC 识别、SPEC-only 修复、审批后变更、批量隔离和中断恢复。

## 平台接入注意事项

1. task 三个 Skill ID 沿用截图。同一条目更新可保留；新建条目或换环境需换成真实 ID。
2. 工具截图对 force_review/metadata 类型不一致，默认只传 relativePath。用户明确要求新评审时，按实际 schema 提交可选参数，不能因低分就强制刷新。
3. finalStatus 的成功枚举资料不完整，不编造固定成功字符串；采用真实 finalScore 与解析/完整性门禁，再进行独立业务评审。
4. AC 标题修复只解决识别风险，不自动补齐示例 36 分中的设计、证据和失败场景。
5. 平台只需原有文件读写、评分、评审与 Jira/审批工具。不要运行脚本、计算哈希或为此安装执行环境。无法确认内容时按影响重审/重批。
6. 关联资源一起部署或支持按 Skill 名称+资源路径读取；短入口不等于只上传 SKILL.md。
7. 状态/报告 contractVersion 保持 3，新增 executionProfile=text-only。哈希字段已换成版本和回读记录，如有自定义消费方需同步字段。task schema_version 仍为 enriched-agent.task/v2。
