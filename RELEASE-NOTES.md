# 2026-11-07 — 6.1.0-preview.8：持久用户配置与提前字段确认

创建和更新的用户配置读取/保存成为必达生命周期，覆盖 SDLC 和通用导出的完整上游 JSON 路径。成功 issue 后立即保存独立已验证配置，不再被附件/关联/队列的部分失败阻断；明确 profilePersistence 状态，只有真实用户 Workspace 的有意义条目读回后才报 saved。保存失败只恢复配置，不重放 Jira、附件或评分。

保留原路径和 profiles 数组，规范 issueType，兼容旧 Story-only storyType，新 Story 保留一致别名；多项目、类型、操作隔离。只存可复用已验证事实和用户明确要记住的字段；一次性值、baseline 和业务正文不自动成为默认。不完整 metadata 可保存已知路由事实并标 complete=false。

首次设置增加 source → project → 可跳过附加字段引导；已有配置创建前展示并确认沿用/修改/切项目，当前 run 未变批次和续跑不重问。SDLC 固定 Story，通用导出保留 Epic/Bug 等实际类型；最终文件预览改为友好检查提示，明确确认门禁保留。

同步 Task 与用于生成工作流的自然语言输入，移除旧 assembly 编号指令冲突。现有评分三参、导出单路径、Q1–Q7、附件保护与未知结果不重放保持不变。配置字段为 additive，旧 state 保留 contractVersion=7。通用 helper 版本为 3.0.0-json-preview.2。

本次为仓库源码修改，不代表测试平台包已更新或真实数据库跨 session 验证通过。发布仍须绑定真实 exporter Skill ID 和用户 Workspace 能力；详见平台清单。

---

# 2026-11-07 — JSON4Jira 重构版本合入 main

将 `codex/sdlc-json4jira-flow` 合入 `main`，保留双方提交历史。主线采用重构分支的 JSON-first Skills/Task，包括附件 add/replace、SPEC 格式及状态/用户配置持久化修复。版本仍为 `6.1.0-preview.7`，平台验证结果不因合并而变更；以下记录保留各版本发布时的分支状态。

---

# 2026-11-06 — 轻量 SPEC 格式稳定性修复

将格式核对并入现有一次生产自检，复用已读取文件，不新增角色、工具、独立审核报告、评分调用或修复循环；版本号及历史说明保持原样。

- 有证据支持的 User Flow 使用一个非空、闭合并标记 `mermaid` 的代码块，简单流程也画最小图；确实不适用时写 `Not applicable` 和事实理由，不以缺事实或虚构分支替代。
- SPEC 内 Jira Story 投影从 `###` 起，仅下调标题层级，保留原业务小节内容、顺序、ID、代码和链接；同次自检核对围栏、标题层级、未替换模板占位和 11 列/嵌入测试表一致性，保留真实业务字面量。
- 机械缺陷局部有界修正，不增加 Q 项或无故全量重审；业务流程变化仍复核受影响内容，评分后 SPEC 改动仍重新评分及确认。平台清单新增待执行回归，不宣称已经平台实测通过。

---

# 2026-11-06 确认 — 路径 JSON 内的附件 add/replace

用户已实测截图所示旧 `attachmentsJson.add`/`replace` 格式适用于当前新路径 JSON 流程，并确认 `readJiraAttachments` 返回实际原件 ID。本次记录该确认，不另调整既有版本号或发布日期体系；以下历史章节保留当时说明，当前附件格式以本条及活动 Skill 为准。

- 在评分及导出的同一 `story.json4jira` 根级写 `attachmentsJson` 对象：`add` 条目使用 `relativePath`、`fileName`；`replace` 条目再加 `attachmentId`，ID来自同source/同目标票reader返回的实际原件。原文仍须证明其为相关SPEC；非SPEC、无关和未选原件保留，不按文件名或读取顺序猜删除目标。
- 使用该对象时，不再使用非空 `attachments` 数组重复上传。显式none维持 `attachments: []` 且省略 `attachmentsJson`；本地SPEC/测试仍同步、审查，当前SPEC仍经 `specFilePath` 评分。
- 普通replace格式已确认，不再以schema未知或重复能力证明进入WAITING。删除是replace后端效果，不新增独立delete操作支持；真实服务错误及上传/旧件移除部分成功或未知仍逐项保留，不能虚报完成或重放成功写入/上传。
- 导出工具仍只接收 `story_json_workspace_path`，附件对象在文件内，不恢复旧direct参数或独立API。评分三参数、Q1–Q7、当前内容确认和后端关联/队列结果核验保持不变；本次确认不代表全链后端效果均已验证。

---

# CEAIA SDLC v6.1.0-preview.6 — 更新复用配置与自动同步 SPEC

日期：2026-11-03。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。本次修正 update 模式的配置复用和 Story/SPEC 同步行为，Q1–Q7 业务判定及现有评分/导出参数保持不变。

- 创建、更新都先读取真实用户 Workspace 的 `ceaia-sdlc-jira/profiles.json`。同用户/source/project/Story type 的稳定项目/类型/AC 映射按组件跨模式复用，不因 profile 只有 create capability 而重跑三个 query。create/edit 权限、requiredness、操作和默认值仍分别保留；已有 baseline 可补实际目标信息，缺项、不适用、显式刷新/目标变化或真实 schema 错误才定向查询，不按 TTL 强刷。
- 更新原票的 Description/AC 和完整附件 inventory 仍通过实际 baseline 读取取得；这些读取不由配置 profile 替代，也不能误记为重复字段 discovery。
- Story 业务变更自动同步本地 SPEC、受影响测试和业务投影，不等待用户额外说“update spec”。每个相关 SPEC 保留其原有义务并核对当前 JSON，完成受影响审查及 Header 同步后评分；纯路由/schema 变化只核对业务一致性，不制造文件改动。
- 默认远端 SPEC 同步计划为相关已有 SPEC→`replace`、无相关 SPEC→`add`；用户明确不改远端附件才 `none`，本地 SPEC 仍必须同步。原件歧义只问具体选择，最终预览包含真实附件计划；替换编码及上传后删除/覆盖保证不足时准确等待，不编造控制或旧 API，不跳过同步后宣布全完成。
- 根级 AC 映射、无 reporter/root staffId、更新 issueIdOrKey、评分三参数、导出单路径、完整原始响应及数据库结果职责继续保留。三个独立包不改，新增平台场景结果留空；本次规则变更不代表真实后端已验证。

---

# 历史记录：v6.1.0-preview.5 — AC 映射修正与现有 Story 更新

以下保留当时发布说明；当前 profile 复用与 SPEC 同步规则以本文顶部 preview.6 和活动 Skill 为准。

日期：2026-11-03。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。按用户转述的最新后端确认修正文件协议，Q1–Q7 业务判定保持不变；质量契约读取说明同步为 preview.5。

- 将实际 AC 字段 ID 字符串放在 JSON 根级 `acceptanceCriteria`，移除旧 `customFields` 包装；验收标准正文仍是 `fields[acceptanceCriteria]` 的 Jira wiki 字符串，不恢复旧 AC 正文/数组结构。
- 创建和更新 JSON 均移除 `fields.reporter`，不从旧模板或 profile 重新加入；根级 `staffId` 继续省略。现有查询工具与身份配置所需 staffId 保留，更新省略 reporter 不清空 Jira 上的原值。
- 创建与更新共用生成、独立审查、评分及单路径导出主链。更新在 `issueType` 之后加入根级 `issueIdOrKey`（大写 I），值为已确认实际票号，与可信 source/target/baseline 一致；创建直接省略，不填空值或示例票号。更新模板及返回 key 核对防止误建新票；工具参数不额外增加票号。
- 更新保留原始义务和获准增量。普通 none 使用 `attachments: []` 保留远端附件，本地 SPEC 通过既有 `specFilePath` 评分后正常确认/导出，不再先要求目标编码或 score-only 能力证明。replace 的具体编码和顺序保证仍需真实后端依据；实际明确拒绝或对应替换能力缺失时才记录准确缺口，不猜控制、追加上传或回退创建。
- Description/AC 在生成阶段完成 Jira wiki 编码，SPEC 仍为 Markdown 投影。评分三参数、导出单路径、当前文件冻结与确认、原始响应及数据库结果保存职责保持原协议。
- 旧 JSON 迁移字段后重新评估当前包并确认，不重放已成功或未知的外部写入。三个独立包保持不改；新增平台场景结果留空，不宣称真实后端已验证。

---

# 历史记录：v6.1.0-preview.4 — Jira 文本格式与身份字段修正

以下保留当时发布说明；当前 AC 映射、reporter 和更新目标规则以本文顶部 preview.6 和活动 Skill 为准。

日期：2026-11-03。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。本次调整 Story JSON 的提交格式，Q1–Q7 业务判定保持不变；质量契约的读取说明同步为 preview.4。

- Story JSON 模板与组装规则移除根级 `staffId`，保留 `jiraInstance`、`projectKey`、`issueType`。身份配置及现有查询工具所需的员工号按实际 schema 保留；`fields.reporter.name` 仍按 metadata 要求与授权使用真实身份，不依赖根级字段。
- 在生成、评分之前将 `fields.description` 和 `fields[customFields.acceptanceCriteria]` 写成 Jira wiki 字符串：标题使用 `h1.` / `h2.`，AC 例如 `* *AC-001*: Given ... When ... Then ...`。其他获准富文本遵从 metadata，保留措辞、顺序、换行、代码和 AC；不转换 summary、路由/身份/选项、普通文本或更新中未改动的字段。
- SPEC 保留 Markdown 格式，从 JSON 中同一业务语义生成可读投影。生成、自检、reviewer 与评分继续解析同一动态 AC 映射，不新增评分门槛；导出阶段不再转换文字。
- 评分仍为 `scoreUserStory` 三参数，导出仍为 `exportJiraByDynamicFields` 单路径，提交与评分相同的当前文件。旧 JSON 删除字段或转换格式后需重新评分和内容确认，不能沿用旧 checksum 或重放已成功/未知写入。
- 三个独立包保持不改。平台清单补充字段移除、wiki 编码和 Markdown 投影场景，真实平台结果留空，历史本地及隔离验证不作为本次后端实测。

---

# 历史记录：v6.1.0-preview.3 — 默认 Jira 入口与缺项补齐

以下保留当时发布说明；当前 JSON 字段与文本格式以本文顶部 preview.6 和活动 Skill 为准。

日期：2026-11-03。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。本次调整入口行为，JSON fields 结构和 Q1–Q7 质量标准保持 preview.2 的规则。

- 所有用户默认继续 Jira-backed 完整流程，入口不提供 Preparation-only / Jira-backed 路线选择。缺少配置或身份不取消默认 Jira 意图，不推荐 preparation-only 作为替代路线。
- 先读取真实用户 Workspace 中适用的已确认配置与可信身份，只询问尚缺的来源、staffId、项目名称/已知 key，或让用户从工具返回的实际项目中选择。已确认项直接复用。
- Story 类型、字段 schema 与 AC 字段 ID 通过现有查询工具取得，不要求业务用户填写技术 ID。元数据未补齐时保留明确待办，不能伪造完整可评分 JSON。
- 仅在用户主动明确要求“只准备/不导出”时遵从；完整流程仍须评分后的当前内容预览和实际用户确认，再以同一 JSON 路径导出。评分三参数、导出单路径和后端分项结果规则不变。
- 平台清单新增首次无配置、已有配置、用户主动不导出三类场景，真实结果留空。preview.2 本地/隔离记录保留为历史，不代表本次入口行为已经平台实测。

---

# 历史记录：v6.1.0-preview.2（入口行为由 preview.3 补充）

以下保留当时发布说明；当前规则以本文顶部 preview.6 和活动 Skill 为准。

# CEAIA SDLC v6.1.0-preview.2 — fields 结构修正

日期：2026-11-03。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。按开发截图及用户后续确认修正 JSON 组装/读取约定，不表示新 schema 已通过真实评分或后端集成测试。

- 根级保留实际 `staffId`、`jiraInstance`、`projectKey`、`issueType`；实际 Jira 写入业务字段放入 `fields`。`fields.project.key` 对应根级项目，`fields.issuetype.id` 使用查询到的 Story 类型 ID，`fields.reporter.name` 对应实际 `staffId`。
- `customFields.acceptanceCriteria` 仅存实际字段 ID 映射；AC 正文存放在 `fields[customFields.acceptanceCriteria]`，值为 Markdown 字符串。其他已获准的自定义业务值也按实际字段 ID 放入 `fields`，不把映射 ID 当正文或 AC 数量依据。
- `fields.summary` 为标题，`fields.description` 为 Markdown 字符串；删除旧顶层 `summary`、`description`、`acceptanceCriteria`、`bddScenarios`。员工号、项目、类型 ID 与自定义字段 ID 全部来自实际上下文/metadata，不硬编码截图示例。
- 生成模板、组装自检、SPEC/测试投影、reviewer 读取及评分 AC 识别按同一字段结构处理。缺失映射、数组描述、目标/身份冲突等属于结构问题，Q1–Q7 业务审查标准不加严。
- 评分仍为 `scoreUserStory` 三参数，导出仍为 `exportJiraByDynamicFields` 单路径；SPEC 不写分数、后端关联/队列分项记录和失败恢复规则不变。转换旧 JSON 后须重新评分并确认当前内容，不重放已成功或未知的远端写入。
- 本次仍不修改三个独立包；平台清单补充新字段结构场景，真实平台结果留空。preview.1 的本地检查和 mock 轨迹属于历史证据，不能替代本次 schema 验证。

---

# 历史记录：v6.1.0-preview.1（JSON 字段约定已由 preview.2 修正）

以下保留当时发布说明；当前字段结构以本文顶部 preview.6 和活动 Skill 为准。

# CEAIA SDLC v6.1.0-preview.1 — JSON4Jira flow

日期：2026-11-02。工作分支：`codex/sdlc-json4jira-flow`，尚未合入 `main`。运行目标仍为平台 GPT-5.6 Luna / GPT-6 Luna，纯文本执行。本预览按新接口重构活动三 Skill，不表示后端集成或两种模型已完成实测。

- 每候选的最终 `story.json4jira` 成为唯一 Story 业务源及提交文件。取消独立 `STORY.md`，SPEC 中 Story 投影来自 JSON；`TEST_CASE.md` 保留。
- 项目、Story 类型、字段 metadata 查询和跨会话配置复用前移至 generation，在最终 JSON 写成前完成。成功后保存已确认 profile，export 不再另组装一套提交字段。
- 评分改用 `nodejs-base-mcp.scoreUserStory`，仅传 `story_json_workspace_path`、`specFileStatus`（精确 `Pass` / `Fail`）和 `specFilePath`。完整响应与真实输入/标识保留；主链内容 PASS 不代替真实评分及持久化。
- 分数不写 SPEC，取消旧显示字段及仅为显示引起的 S2。导出工具仍为 `java-base-mcp.exportJiraByDynamicFields`，但**仅传 `story_json_workspace_path`**；评分与导出使用同一候选最终 JSON 路径。
- 真实评分通过后，先展示当前业务内容、目标与附件操作的前端 HTML 预览，用户明确确认当前内容后才导出。内容变化使旧评分/确认失效，必须重新完成相应步骤。
- 后端导出负责校验 score checksum/threshold、写入 Jira 与所需附件、回填数据库关联并入异步队列。前端不再执行旧五字段 B；分别记录 issue、attachments、linkage、queue、async sync 真实证据，不能把普通 success 或入队当作数据库/Jira UI 分数全部完成。
- 批量仍为每候选一个 JSON，默认逐票调用，不使用旧 `issuesJson`。外部未知/部分结果先核对，不能为修复关联或队列状态重建已成功票据。
- Q1–Q7 标准保持，审查对象更新为 JSON 业务内容与 SPEC 投影；review/score 共享两批内容修复、机械 cause 一次的规则保留。
- 更新 `none` / `replace` 业务语义保留，但 live schema 未暴露 target/replace 时明确 `WAITING_TOOL`。不猜新字段、不改成 create、不上传 score-only SPEC；预览版不冒称更新后端已集成。
- `jira-createissue-helper`、`user-story-reviewer`、`review-requirements` 三个独立包完全不改，不为旧工具所有路由新增兼容代码或脚本。

Task 兼容文件名仍为 `SDLC-workflow-v5.task.json`，显示名改为 `SDLC-workflow-v6.1 JSON preview`；schema 与原 10 工具顺序保留，最后评分工具 ID 替换为 `nodejs-base-mcp.scoreUserStory`。现有 generation/review ID 与 exporter 占位仍须核对实际安装结果，不能伪造平台 ID。

当前行为以 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)、[评分契约](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 与 [导出协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) 为准。新接口平台结果在 [测试清单](PLATFORM-TEST-CHECKLIST.md) 留空等待实测；旧 v6 本地、旧 S1–S2–A–B 验证仅为历史，不适用于新调用形态。

---

# 历史发布记录：v6.0 及更早版本（不作为 v6.1 运行指令）

以下原文包含旧工具、Markdown Story、SPEC 分数显示、S2 和五字段 B 等当时协议，均不适用于当前 JSON 主链；只保留版本历史。其“当前”“默认”“本次”等词仅指当时版本。

# CEAIA SDLC v6.0.0

发布日期：2026-10-31。运行目标：平台 GPT-5.6 Luna / GPT-6 Luna；纯文本执行。

本次重构保留三个活动 Skill，将重复的内容标准、编排和工具协议分开：

- generation 在写作前必读固定 Q1–Q7，review 包内携带同文副本。review 独立核查原来源与当前内容，保留遗漏/编造/可验收性/测试/SPEC/更新保留检查；移除SCORE=DEFERRED、标题禁or及预算/readiness等兼容门禁。
- 模板布局、`*-spec.md`、11列测试表和镜像引用集中在生产检查；真实内容或引用不一致仍须修复。删除已归并的独立planning-and-scoring、business-planning-checklist、validation资源。
- Task与普通对话遵循同一编排：每候选串行准备至review/score，保留阻塞项；全部选定候选及整批原始覆盖通过后才导出。默认全流程、显式preparation-only和阶段调用边界保留。
- review/score共用的内容修复由一批调整为两批，无进展提前停止；每个纯机械原因另限一次，变更失效规则不豁免。续跑保留计数，实际新证据或明确有限授权按契约处理；不改变外部操作各自的恢复规则。
- 真实S1评分、完整响应、76分/parser门槛、SPEC分数显示与必要S2最终元数据保存全部保留；不以共享标准保证后端高分，不以缓存命中省略持久化。
- Jira A及所需附件成功后逐票五字段B关联全部保留；分开记录外部结果，未知先核对，B失败不重放A。跨会话profile、设置确认、AC分区、wiki转换及附件替换顺序保留。
- 当前更新主链仍为none/replace；review逐SPEC记录保留义务。独立helper、user-story-reviewer及review-requirements原文件不改；legacy仅在明确选择时使用。
- 升级旧状态保留全部版本、计数、评分元数据和已知/未知远端操作结果，不通过重放调用迁移。

兼容文件路径仍为 `SDLC-workflow-v5.task.json`，显示名为v6；Task schema、10工具列表及score最后的位置保留，现有generation/review ID与exporter占位值仍须按实际平台核对。不在Task添加未经平台确认的模型参数，也不表示已完成平台安装。

当前行为以 [workflow-contract](ceaia-sdlc-story-spec-generation/references/workflow-contract.md)、[质量标准](ceaia-sdlc-story-spec-generation/references/quality-contract.md)、[评分协议](ceaia-sdlc-story-spec-generation/references/scoring-tool-contract.md) 和 [Jira A/B协议](ceaia-sdlc-jira-export/references/jira-write-and-recovery.md) 为准。文本结构/链接检查不代表真实平台回归；两种模型及Jira/附件/数据库结果须填写 [平台测试清单](PLATFORM-TEST-CHECKLIST.md)。

---

# 历史发布记录（不作为v6运行指令）

以下保留原发布文本，其中“当前”“默认”“本次”等词仅指当时版本。旧修复预算、旧review字段、旧helper路由与评分来源说明可能已被后续修正；执行一律以本文顶部v6和活动Skill为准。

# CEAIA SDLC Skill Bundle v5.3

2026-10-30：统一到 `main`。

- 默认 SDLC 链路为 `ceaia-sdlc-story-spec-generation` → `ceaia-spec-review` → `ceaia-sdlc-jira-export`；Task 同步采用新导出 Skill，字段配置复用、SPEC 附件及逐票五字段数据库关联保留。
- 合入原实验分支最新的单轮自动修复、SPEC 分数显示及问题澄清/绑定答案续跑规则。
- 保留合并前 `main` 的 `user-story-reviewer` 与 `jira-createissue-helper` 全部修复，包括长/批量 Jira 输入来源路由、同会话本地续改、实际评分模式决定四/五字段及漏关联恢复。通用 Jira/Test Case 功能不迁移到新 exporter。
- helper 的主入口及旧 gateway 增加当前/旧版分流：Task 和普通对话的当前 SDLC handoff 统一交给新 exporter；旧 gateway 仅供明确选择的旧版兼容流程，通用及评分绑定业务资源保持原 main 内容。
- 新增独立 `review-requirements`，仅含 `SKILL.md` 和 `templates/review-report-template.md`，不加入 SDLC Task；按纯文本平台适配，不执行脚本、命令或遥测，PR 评论仅按用户明确请求发布。
- 本次完成静态合并验证；真实 GPT-6 Luna、Jira、附件和数据库集成仍按测试清单验证，不把离线检查当作平台实测。

2026-10-30 内容澄清与续跑（已合入 main）：review/最终评分阻塞时，生成方在聊天区展示具体文件、条目、当前内容、失败原因和所需事实；ask_user_question 弹窗仅保留唯一简短定位、简短原因及具体问题。补充信息与当前问题绑定，充分回答后更新受影响 Story/Test Case/SPEC 并重新独立审查、评分；明确的 AI 编写错误只询问有限追加修复决定，不要求用户重复提供已知需求。保留单轮预算、所有质量门禁与导出/数据库回写。

2026-10-29 单轮修复调整（已合入 main）：每个 candidate / evidenceRevision 的默认自动修复由三轮降为一轮，review 与评分问题共用；初审后最多一个集中修复批次及受影响内容复审。仍需修改时立即暂停并返回现有问题，不再额外启动诊断 reviewer。续跑保留已消耗额度；额外修复仅来自真实新增实质证据/决策或用户明确的有限加轮授权。独立审查、76 分、SPEC 分数显示、工具恢复及导出/逐票数据库关联均保留，两个 exporter 目录未改。

v5.3 导出专用化最初实现于 2026-10-27，本次统一到 main 的日期为 2026-10-30。目标运行模型：GPT-6 Luna。

新增独立 `ceaia-sdlc-jira-export`，仅处理已通过 SDLC 门禁的 Story 创建、更新与批量导出，保留 SPEC 附件及首次 Jira 成功后的逐票五字段数据库评分关联。保留合并前 main 的 `jira-createissue-helper/` 与独立 `user-story-reviewer/` 最新修复。Task 和 generation handoff 已接入新 exporter；平台导入前须填写新 skill 的实际 ID。

字段配置和默认值成功后保存到真实用户工作区 `ceaia-sdlc-jira/profiles.json`；完整命中跨 session 跳过项目/type/metadata query，不按 TTL 刷新。每次确认实际目标、Ticket Number、Epic/Parent 与字段值。Update 复用生成阶段的 baseline 和完整附件清单，不重复远端 intake。首次 Epic/no-Epic、76 分、独立 PASS、SPEC sync、全局覆盖、内容保真、附件替换顺序和失败恢复门禁保留。

v5.3 最初的导出专用化改动保留生成候选串行/阶段依赖和评分阈值；当前 main 还包含上述单轮修复与澄清续跑规则。静态和离线验证不能代替 GPT-6 Luna 平台上的真实 Jira/附件/数据库关联回归；实测按 PLATFORM-TEST-CHECKLIST.md 执行。

---

# 历史发布说明：CEAIA SDLC Skill Bundle v5.2

以下记录当时的实现；其中三轮预算、旧 helper 接入及旧 reviewer 路由均以当前 v5.3 和对应 Skill 为准，不能作为当前运行指令。

更新日期：2026-10-23


2026-10-28 小改动：SPEC 的 Review Result 区新增第三字段 `Story Score`，显示当前 Story 的真实评分（`<number> / 100`）；首次评分前显示 `Not scored`。生成阶段负责后置回写、全文读回和必要的正常缓存 metadata 绑定；业务正文、76 分门槛、导出工具参数及逐票数据库关联不变。
本次将 SDLC Task 从约 24 KB 精简至约 12 KB，保留四阶段调度、关键门禁及原有 Skill/工具配置。执行细节仍由各 Skill 负责；不改变 Story、Test Case、SPEC、审查和 Jira 功能。评分工具的 `specFileStatus` 只接受大小写严格的 `Pass`/`Fail`。SPEC 审查结果仍记录 `PASS`，在评分调用边界映射为 `Pass`；非 PASS 审查不会进入评分。普通 Jira 和 Test Case 导出规则未改。

## 历史发布说明：v5.1

本次调整 SDLC Task 与三个活动 Skill 的顺序：先生成 Story、Test Case、SPEC，再进行独立内容审查；同步并读回 SPEC `Review Result` / `Result` 后，使用 Workspace `STORY.md` 路径及主 SPEC 状态/路径调用更新后的评分工具。新建与更新均走 Workspace 评分，只有最终真实分数通过才进入 Jira。工具正常复用缓存分数时仍保存本次 SPEC 状态/路径，后续五字段 Jira 绑定关联本次最终通过的评分记录。数据库现阶段只要求最终 SPEC 状态。独立 `user-story-reviewer` 的 Jira 直评分路由保持独立。

独立审查报告中的 SCORE 改为 DEFERRED；内容 PASS 不直接代表 Jira 可写。评分或审查失败均按受影响文件局部修复，修复后重新执行必要内容审查与最终评分，共用原三轮预算。评分记录的人类摘要缩短，完整原始 JSON 仍保留。SDLC 导出只读取当前操作相关的通用说明，Task 更新逐票执行；首次导出调用已成功处理的附件不再次上传。此次规则按 GPT-6 Luna 后续运行保持简明的阶段边界，未改变评分阈值及原 Jira 字段映射。

本次纯文本审计将此前误以 `.md` 命名的 WPS/OLE 二进制 Test Case 引用改为真正的 Markdown，按原有共享规则写明 individual/single 两种导出形态及字段保真要求。修正通用 Helper 文档中未暴露的 `issues` 参数、Test Details JSON 字符串类型、SDLC 与普通流程的确认边界，以及附件示例文件名。Task 中 Jira Helper 的平台 skill ID 仍是安装时填写的配置值，不是运行脚本。

## 历史发布说明：v5.0.0

发布日期：2026-10-06

## 补充：独立 Story Reviewer 使用 Jira 直评分

`user-story-reviewer` 现在按 Ticket Number 路由：任何带 Ticket Number 的 Story 直接使用 `jira_number` 与用户明确提供的 `source` 调用评分工具；多条 Story 缺少 source 时一次性收集，不读取 `jira-user-info.md`，也不从 Ticket 前缀推断。Jira 模式不再生成评分输入 Markdown，不再调用 Jira 读取或独立数据库回写工具；评分工具自行读取 Jira、保存结果并关联 Ticket。只有没有 Ticket Number 的输入继续使用 Workspace Markdown 模式。暂不传递 `specFileStatus` 与 `specFilePath`。

## 补充：统一 SPEC 文件名

所有新生成的 SPEC，包括不上传的更新用本地 SPEC，都必须使用与 Story/业务功能对应的 `<name>-spec.md`。生成阶段不再允许 `SPEC.md` 或普通 `<name>.md` 的文件名例外；多个不同替换 SPEC 使用不同的业务功能名称。独立审查核对实际文件、SPEC 内 File Name、Story/Test Case 引用、状态和更新映射；最终对话从实际 Workspace 文件读回并显示相同名称。此次未修改任务 JSON、`VERSION.json` 或 `jira-createissue-helper`。

## v5：Jira Ticket 与 Story 评分记录关联

在 v4.2 的 Jira create/update 调用及要求的附件操作确认成功、取得该项返回的 Ticket Key 后，`jira-createissue-helper` 的 SDLC gateway 才对该项执行第二次 `exportJiraByDynamicFields` 调用。第二次调用恰好使用五个真实非空字段：`staffId`、`almType`、`issueIdOrKey`（返回的 Ticket Key）、`conversationId`、`markdownReviewRelativePath`（本次 PASS 评分所用的 `STORY.md` 工作区相对路径）。第一次调用不带 `markdownReviewRelativePath`；第二次调用不带 Jira 内容、附件或批量字段。

批量 Jira 返回按每项结果和路径分别关联。Jira 写入、附件、`markdownReview` 绑定结果分别留痕和报告；绑定失败或未知时保留已成功的 Ticket Key，不重建 Ticket、不重放已成功的 Jira 写入。所有绑定完成前不宣布整条流程成功。普通 Jira 和 Test Case 路由保持 v4.2 行为。

v5 的 task 文件是 `SDLC-workflow-v5.task.json`。其 helper skill ID 仍须替换为平台上传后返回的实际 ID。

## 继承的 v4.2 行为

v4 以 v3 为基线，将最后的 Jira 写入阶段从 `ceaia-sdlc-only-jira-push-content` 替换为 `jira-createissue-helper` 的独立 SDLC gateway；v5 保留这条链路并增加上述第二次关联调用。活动链路为：

1. `ceaia-sdlc-story-spec-generation`
2. `ceaia-spec-review`
3. `jira-createissue-helper` → `references/sdlc-gateway.md`

旧 push Skill 未复制到 v4 包，仍完整保留在 v3 版本目录中，便于回滚。

## 主要改动

### 1. Helper 中新增独立 SDLC gateway

- 新增 `jira-createissue-helper/references/sdlc-gateway.md`。
- SDLC handoff 优先于 generic create/update/batch；失败时禁止降级绕过。
- gateway 校验评分、独立 review、全局覆盖、Test Case 标题检查、每个 SPEC 的 review header 回写和附件绑定。
- generic create、update、association、普通 batch 和 Test Case 业务流程未被改写为 SDLC 流程。

### 2. Jira wiki 数据组装

- Workspace 中的 `STORY.md` 保持不变。
- Jira-bound Description 是单独的 transport rendering。
- Markdown 标题按层级转换为 `h1.`—`h6.`，例如 `## Test` → `h2. Test`。
- 列表、代码块和 inline code 采用 Jira wiki 表示；转换仅改变表现语法，不得改写业务语义。
- preview 前检查代码块之外是否仍有 Markdown 标题；无法无损转换时阻塞导出。

### 3. 评分结果改为可读 Markdown

- 每次实际评分生成新的 `story-quality-score-attempt-<nnn>.md`。
- Markdown 顶部展示绑定、结果、AC/parser 状态和诊断摘要。
- 完整原始 JSON 响应保存在 fenced `json` block 中，未知字段也必须保留。
- 不再把新评分覆盖到同一个 `.json` 文件。

### 4. 输出最新版，中间记录保留

- `outputs/ceaia/...` 的 `STORY.md`、`TEST_CASE.md` 和 SPEC 始终是最新版，可被修复后的版本替换。
- `.ceaia-work/...` 中的 plan、score、review、post-repair verification 和 Jira export-attempt records 单调编号、不可覆盖。
- compact state / batch state 仍可更新，只保存指针、计数和状态，不复制完整文档正文。
- 不创建历史 Story/Test Case/SPEC 副本，避免十个 Story 时重复占用大量上下文。

### 5. 强化 SPEC review result 同步

- 独立 reviewer 仍然只读，不直接修改 SPEC。
- parent 在 substantive PASS 后逐个更新所有 intended SPEC 的 `Result` 和 `Reviewer Notes`。
- `Reviewer Notes` 记录当前 review attempt。
- 更新后必须立即读回完整 SPEC，确认 header 与最新 PASS 一致且 body 未变化。
- 首次 header write/read mismatch 只允许一次 targeted retry；再次失败进入 `WAITING_TOOL`，不得设置 `jiraReady=true`。
- helper gateway 再次检查这一状态，避免出现 review 已 PASS 但 SPEC 仍显示旧结果。

### 6. Helper 截图增量

- popup/selection UI 成为 projectName、projectKey、issueType 等允许输入的控制契约，不能用普通 chat 代替。
- batch flow 补载 project/issue-type validation。
- Test Case Jira-visible 字段统一使用 newline characters，禁止 literal `<br>` 及其 escaped variants。
- `common-field-assembly.md`、`common-guardrails.md` 和 preview validation 增加对应检查。

### 7. v4.1 直接导出与路由修正

- 删除 SDLC 对额外审批工具的依赖；最终 preview、payload read-back 和附件 preflight 通过后直接调用 `exportJiraByDynamicFields`。
- `handoffType=ceaia-sdlc-validated` 仅作为内部路由状态，禁止进入任何 Jira MCP 参数或序列化字段。
- SDLC 批量导出使用工具实际暴露的 `issuesJson`；所有参数直接传递，不使用 `requestJson` wrapper。
- 项目、issue type 和 metadata 查询参数与当前工具契约对齐。
- `jira-user-info.md` 增加 CEAIA SDLC 默认配置，可复用已验证的 source、project、Story type、Epic Link 和 Parent Link。
- 当时的 v4.1 顺序是 generation → score → review；当前 v5.1 顺序以本文顶部和 Task 为准。

### 8. v4.2 默认全流程、通用文本转换与 SDLC 用户配置

- SDLC 默认继续到 Jira create/update；初始输入没有 push/export 字样也不会在 review PASS 后停止。只有用户明确要求 preparation-only/no-Jira、明确停止或存在阻塞时才不进入 Jira 阶段。
- 新增 `common-jira-text-rendering.md`，供普通非 Test create/update/batch 和 SDLC transport rendering 使用。Workspace 源文件不修改，preview 展示即将发送的 Jira wiki 文本。
- Test Case 专用路由、Test issue item、表格、步骤、数据和预期结果明确排除在通用转换之外；`flow-testcase-source-export.md` 保持字节不变。
- `jira-user-info.md` 改为 SDLC 专属。普通 create/update/association/batch/Test Case 不读取、不创建、也不更新该文件。
- 首次 SDLC create 在 Jira 阶段主动询问并验证 Epic Link，或记录用户明确选择无 Epic；不会在 generation intake 提前询问。
- 后续 SDLC 运行会先重新验证保存的 source/project/Story type/Epic/Parent，再要求一次“使用这些设置/修改设置”的配置确认。该确认不是最终写入审批，最终 preview/preflight 后仍直接导出。

## 未修改或保留的范围

- v4 阶段 `flow-create-issue.md`、`flow-update-issue.md` 和 `flow-batch-export.md` 主要接入非 Test rich-text transport rendering；它们的业务路由、metadata、preview/confirmation 和写入规则在当前版本继续保留。v5.1 另修正旧工具参数示例与文本引用格式。
- v4 时 `flow-testcase-source-export.md` 实际是 WPS/OLE 二进制文档，曾为避免改变旧流程而保留原字节；v5.1 已将它替换为纯文本 Markdown，并依据现有通用 Test Case 规则明确两种 payload 形态。平台仍需按清单做真实导出回归测试。
- 评分阈值仍为 `ok === true` 且 `finalScore >= 76`，并要求 AC/parser 完整性。
- 历史 v5.2 使用三轮自动修复；当前 v5.3 改为每个 candidate / evidenceRevision 共用一轮，保留无进展停止及用户明确授权有限追加轮次。

## 当前 Task 使用前需要填写的值

当前 `SDLC-workflow-v5.task.json` 中 SDLC exporter 的平台 skill ID 使用占位值：

`REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID`

在平台上传/更新 `ceaia-sdlc-jira-export` 后，用平台实际返回的 skill ID 替换该值。Skill name 已设置为 `ceaia-sdlc-jira-export`，评分工具仍位于 tools 列表最后。`jira-createissue-helper`、`user-story-reviewer` 与 `review-requirements` 独立安装，不加入当前 SDLC Task。
