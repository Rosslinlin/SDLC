# v5 Platform Test Checklist

## 安装前

- [ ] 上传三个活动 Skill，并记录平台返回的 skill IDs。
- [ ] 替换 task 中的 helper skill ID 占位值。
- [ ] 确认平台提供 `ask_user_question`、Jira MCP tools 和 `nodejs-base-mcp.score_requirement_markdown`；SDLC 路由不依赖审批工具。
- [ ] 用实际运行配置的 GPT-6 Luna 跑下面的新建和更新样例；task/Skill 不硬编码旧模型名称。核对实时评分工具 schema 接受 `relativePath`、`specFileStatus`、`specFilePath` 三个参数。
- [ ] 若平台提供用量明细，记录单 Story 各阶段的 input tokens、工具调用次数及耗时；检查是否重复加载未变的 Skill/证据或重复执行 review/score。Task 精简不保证单独消除异常高用量。
- [ ] 确认平台能读取全部 Markdown/JSON 文本资源，包括已转换为纯文本的 `flow-testcase-source-export.md`；流程不调用 Python、Shell、JavaScript 或本地校验代码。

## 场景 A：新 Story 正常链路

- [ ] 初始输入不包含 push/export 字样时，内容审查、SPEC 状态同步和最终 Story 评分全部通过后仍自动进入 Jira create/update 阶段。
- [ ] Story 只有一个 `## Acceptance Criteria`，AC 使用稳定编号和 Given/When/Then。
- [ ] 先生成 Story、Test Case 和 `<name>-spec.md`，完成独立内容审查；首次审查时无评分记录仍可返回内容 PASS，报告中 SCORE=DEFERRED、jiraReady=false。
- [ ] 内容 PASS 后先同步 SPEC `Review Result`/`Result` 并完整读回，再调用评分工具。入参为 `relativePath`=最终 STORY.md、`specFileStatus`=由已验证 `Result: PASS` 映射的精确字符串 `Pass`（不得传 `PASS`/`pass`）、`specFilePath`=该 SPEC 的工作区相对路径；不传 Jira key/source。
- [ ] 评分后产生 `story-quality-score-attempt-001.md`，完整 raw JSON 保留；可读摘要简洁且与 raw JSON 一致，记录实际 Story/SPEC 入参与审查 attempt。
- [ ] review 产生 `review-attempt-001.md`，不是覆盖固定 `review.md`。
- [ ] SPEC header 显示 PASS 和正确 attempt，完整复读成功且后续最终 Story 评分通过后才 `jiraReady=true`。
- [ ] Jira preview 的 Description 使用 `h2.` 等 Jira wiki 标题，不包含代码块外的 `## `。
- [ ] 最终 preview/read-back/preflight 通过后直接调用 `exportJiraByDynamicFields`，不出现额外批准等待。
- [ ] 外层参数、`dynamicFieldsJson`、`attachmentsJson` 和 `issuesJson` 中均不存在 `handoffType`。
- [ ] 多个 SDLC 新建可使用 `issuesJson`；Task 中的多个更新按预览顺序逐票执行。不发送未暴露的 `issues` 参数或 `requestJson` wrapper。
- [ ] 第一次 Jira create 调用不包含 `markdownReviewRelativePath`；成功返回真实 Ticket Key 且要求的附件操作成功后，第二次才调用关联。
- [ ] 第二次调用恰好包含非空 `staffId`、`almType`、`issueIdOrKey`、`conversationId`、`markdownReviewRelativePath` 五个直接参数；`issueIdOrKey` 等于第一次实际返回的 Ticket Key，路径等于本次 PASS 评分提交的 `STORY.md` 路径。
- [ ] 第二次调用不含 Summary、Description、dynamicFieldsJson、attachmentsJson、issuesJson 或其他 Jira 变更字段；只在 `markdownReview` 返回确认成功后才将该项标记为完整成功。

## 场景 B：评分或 review 修复

- [ ] Story 修复后 outputs 中 STORY 被替换，不产生历史 Story 副本。
- [ ] 内容审查阶段修 Story 后，重新同步受影响测试/SPEC 并复审；首次评分仍只在最终内容通过后发生。
- [ ] 最终评分不足 76、AC 未识别或解析失败时，Jira 被阻断；受影响内容局部修复后重新独立核对，再产生下一次编号评分记录，旧记录保留。
- [ ] 新 review 写入新的编号文件，旧 review 保留；同一轮失败不无限反复修改。
- [ ] SPEC-only 修复不触发 Story 重写；如之前已评分，同 Story 的新 SPEC 元数据通过正常缓存调用保存，不设置 force_review。
- [ ] 同一 Story 路径出现旧低分和新高分记录时，最终 Jira Ticket 关联本次最终通过且带当前 SPEC 状态的记录。
- [ ] 已有旧分数但缺少最终 SPEC 状态时，补一次携带真实 SPEC 元数据的正常评分调用；缓存复用分数仍正确保存本次状态/路径。
- [ ] 三轮预算用尽或连续两轮无进展后停止自动编辑并请求明确决策。

## 场景 C：SPEC header 回写失败

- [ ] 首次 mismatch 只执行一次 header-only retry。
- [ ] 第二次 mismatch 进入 WAITING_TOOL。
- [ ] helper gateway 拒绝未验证的 SPEC，即使 review report 为 PASS。

## 场景 D：Jira wiki 转换

- [ ] `#` 至 `######` 分别转换为 `h1.` 至 `h6.`。
- [ ] code fence 内的 `#`/`##` 不转换。
- [ ] 有序/无序列表层级保持。
- [ ] AC IDs、Given/When/Then 和业务文案没有改写或重排。
- [ ] Workspace `STORY.md` 未因 Jira rendering 被修改。
- [ ] 普通非 Test create/update/batch 的 Markdown rich text 也转换为 Jira wiki，并在 preview 展示准确 outgoing value。
- [ ] 普通 update 只转换本次请求修改的字段，不重排未修改的现有字段。

## 场景 E：现有 Jira 更新

- [ ] 更新的评分输入仍是本次修改后的 Workspace `STORY.md` 相对路径及对应 SPEC 状态/路径；已知 Ticket Key 不发送给评分工具。
- [ ] `attachmentAction=none` 可完成 description-only update。
- [ ] 多个不同 replacement SPEC 保持独立 mapping，均有独立 review/header binding。
- [ ] preview 显示 original→replacement mapping。
- [ ] 第一个失败/partial/unknown 后暂停剩余更新，不重放已成功操作。
- [ ] 更新成功后第二次调用使用返回且与目标一致的 Ticket Key；数据库关联失败时保留 Jira 已更新状态，不重发第一次更新。

## 场景 E2：绑定、批量与恢复

- [ ] 批量首次调用的成功项逐项用各自返回的 Key 和对应评分 Story 路径进行第二次调用；失败项不绑定，不交叉关联。
- [ ] 首次 Jira 或附件操作失败/partial/unknown 的项目不进入绑定步骤。
- [ ] 首次调用的 `attachmentsJson` 已返回成功时，后续只记录该结果，不重复上传 SPEC；附件失败只针对该附件恢复，不重放成功的 Jira 写入。
- [ ] 绑定返回 partial、失败、未知或业务 `status=400` 时分别记录 Jira/附件和绑定结果；保留 Key/URL，不重建 Ticket、不自动重试不确定的绑定、不宣布整体成功。
- [ ] 首次写入预览和结果保留在编号内部记录；取得 Key 后，当前 `jira-preview.md` 展示并复读第二次调用的精确五字段 payload。
- [ ] 普通 create/update/batch/association 和 Test Case 导出没有第二次评分关联调用。

## 场景 F：Helper 通用流程回归

- [ ] 普通 create/update/association/batch 不进入 SDLC gateway。
- [ ] project/issue type 需要选择时使用 popup，而不是普通 chat。
- [ ] Test Case Jira-visible 换行使用 newline，不出现 `<br>` 或 escaped equivalents。
- [ ] Test Case route/item/table/Test Details 未加载或应用通用 Jira text rendering，现有 payload shape 与字段内容保持不变。
- [ ] Test Case 选择 individual 时使用 `issuesJson` 字符串，逐项 `testDetailsJson` 也是一次序列化的 JSON 字符串；选择 single 时不传 `issuesJson`，Description 仅含五列表格，并按需要传顶层 `testDetailsJson` 字符串。
- [ ] 普通 create/update/association/batch/Test Case 均不读取、创建或更新 `jira-user-info.md`。

## 场景 G：首次请求与用户工作区复用

- [ ] 无论初次输入是否包含 “push to Jira”，都先运行 generation → independent content review → SPEC 状态同步 → final Workspace Story scoring，不在 intake 阶段询问 project 或 Epic Link；全部放行后进入 Jira。
- [ ] 第一次 SDLC create 在 Jira 阶段主动询问可验证的 Epic Link或明确的 no-Epic 选择。
- [ ] 首次成功 SDLC 导出后，user workspace 的 `jira-user-info.md` 只有一个 CEAIA SDLC 默认配置区。
- [ ] 下一次 SDLC 导出先读取并验证保存的 source、project、Story type、Epic/Parent Link，然后显示全部设置并要求一次 Use/Change 确认。
- [ ] 保存值失效或用户明确变更时，只询问受影响字段并重新验证。

## 场景 H：SPEC 文件名与对话一致

- [ ] 新 Story 的实际 Workspace SPEC 文件名是 `<name>-spec.md`；SPEC 内的 File Name、Story 附件名、Test Case Related SPEC 和最终对话显示同一个真实文件。
- [ ] 多个不同的更新替换 SPEC 使用不同的业务功能 `<name>`，每个文件都以 `-spec.md` 结尾，且与原附件到新文件的 mapping 一一对应。
- [ ] `attachmentAction=none` 时，本地生成但不上传的 SPEC 仍使用 `<name>-spec.md`；Story 不误称它已上传。
- [ ] 用户提出 `SPEC.md` 或 `<name>.md` 时，在生成前确定符合规则的名称；不生成不合规文件，也不靠上传时改显示名掩盖实际文件名。
- [ ] 修复旧文件名时同步实际文件、内部 File Name、状态、Story/Test Case 引用、更新 manifest 和审查绑定；如 Story 内容变化则重新评分，并重新审查受影响内容。
- [ ] 最终回复在读回实际 SPEC 后列出相同的文件名和路径，没有计划名、旧名或简称。

## 场景 I：SDLC Story 的 Description / AC 字段分配

- [ ] Task 模式和普通对话模式的新建 Story：`STORY.md` 保持完整且不被改动；首次 Jira payload 的 Description 包含 AC 节以外的所有 Story 内容（包括 AC 后的章节），独立 AC 字段包含每个 AC 一次，Description 中没有 AC 声明或正文。
- [ ] 更新 Story：当前 Jira AC 字段与已审查 Story 的 AC 相同时可保持 Description-only/零附件更新；不同时仅将审查过的 AC 写入元数据确认的字段，原有未授权字段保持不变。
- [ ] 批量导出：逐项解码 `issuesJson` 与各项的 `dynamicFieldsJson`，确认每个 Story 的 Description/AC 独立配对，没有继承其他项目的 AC 或遗漏 AC 字段。
- [ ] 将实际 `STORY.md`、首次调用的 `jira-preview.md` payload 和 Jira 读取到的 Description/Acceptance Criteria 分别比较；只接受 Markdown→Jira wiki 的展示语法变化，以及 AC 从 Story 节迁移到独立字段。若 Jira 读取结果与预览值不符，记录具体字段，不重复创建或更新 Ticket。
- [ ] 普通非 SDLC create/update/batch 和 Test Case 的字段映射与原有流程一致。


## SPEC Story Score display (2026-10-28)

- [ ] SPEC starts with Result, Reviewer Notes, then Story Score before the first `---`; before real scoring the third field is `Not scored`, which does not block content review.
- [ ] A persisted `ok=true` finite numeric finalScore is copied exactly as `<number> / 100` to all current SPECs for that Story; decimals/low scores are not rounded or fabricated. Missing/non-numeric scores never become zero or a sibling's result.
- [ ] Only the score header changes; body, embedded Story/Test Case table, review Result/Notes and bodyRevision remain unchanged. Edited fileRevision advances.
- [ ] When the primary SPEC revision changed after a passing score, one normal cache/metadata call records that final revision; both raw attempt records remain intact. No force_review or display/rescore loop.
- [ ] The metadata-call result, displayed value and current binding agree before readiness. A disagreement has at most one correction/check, then bounded recovery; parser failure or a low score still blocks Jira.
- [ ] Resume with an unchanged verified header makes no extra write or scoring call. A Story change invalidates/resets its displayed score before new review/scoring.
- [ ] Both export skill directories remain unchanged, including the existing first Jira write and separate database association call. The shown total stays out of Jira Description/AC and other issue fields.
