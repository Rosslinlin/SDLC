# v5.3 Platform Test Checklist

## 安装前

- [ ] 上传三个活动 Skill，并记录平台返回的 skill IDs。
- [ ] 替换 task 中的新 ceaia-sdlc-jira-export skill ID 占位值；不可使用原 helper ID。
- [ ] 确认平台提供 `ask_user_question`、Jira MCP tools 和 `nodejs-base-mcp.score_requirement_markdown`；SDLC 路由不依赖审批工具。
- [ ] 用实际运行配置的 GPT-6 Luna 跑下面的新建和更新样例；task/Skill 不硬编码旧模型名称。核对实时评分工具 schema 接受 `relativePath`、`specFileStatus`、`specFilePath` 三个参数。
- [ ] 若平台提供用量明细，记录单 Story 各阶段的 input tokens、工具调用次数及耗时；检查是否重复加载未变的 Skill/证据或重复执行 review/score。Task 精简不保证单独消除异常高用量。
- [ ] 确认平台能读取全部 Markdown/JSON 文本资源，包括已转换为纯文本的 `flow-testcase-source-export.md`；流程不调用 Python、Shell、JavaScript 或本地校验代码。
- [ ] 如需独立评分、通用导出或需求审查，分别更新保留 main 修复的 `user-story-reviewer`、`jira-createissue-helper` 和新 `review-requirements`；三者不加入新的 SDLC Task。

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
- [ ] 同一 candidate/evidenceRevision 的 review 与评分修复共用一次集中自动修复额度；修复后仍有问题立即等待，不再自动编辑、追加诊断 reviewer 或重置计数。追加轮次须有真实新证据/决策或明确有限授权。

## 场景 C：SPEC header 回写失败

- [ ] 首次 mismatch 只执行一次 header-only retry。
- [ ] 第二次 mismatch 进入 WAITING_TOOL。
- [ ] 新的 SDLC exporter 拒绝未验证的 SPEC，即使 review report 为 PASS。

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
- [ ] 非 SDLC 中，当前 Workspace 已评分 Story 的 create/update/batch 成功后仍按 helper 规则完成该轮五字段关联；真正未评分的 Story、其他 Issue 类型、association-only 和 Test Case 不新增评分关联调用，也不套用 SDLC 76 分门槛。

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
- [ ] 首次成功 SDLC issue/所需附件操作后，真实 user workspace 的 `ceaia-sdlc-jira/profiles.json` 保存对应身份/来源/项目/Story 类型/操作能力的精简字段 schema、字段 ID 和默认值；不能只保存最终 payload。
- [ ] 跨 session 再次导出命中完整配置时不调用项目/类型/metadata query，也不重读已确认 Epic；显示本次 source/project/Story type/Ticket Number/Epic/Parent/字段值并要求一次 Use/Change 确认。
- [ ] 只有缺失/不适用配置、用户明确变更/刷新或实际字段校验失败时查询受影响部分；不因 session 或时间经过强制刷新。

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


## 场景 J：跨 session 字段配置与身份范围

- [ ] 首次未命中时按需查询项目、Story 类型、metadata；只支持 Story，缺 Story 类型明确阻断，不提供 Task/Bug/Test fallback。
- [ ] Profile 保存所有 required 字段规则（含后端默认未显式提交的字段）、AC 映射、已选 optional 字段/option ID 与传输格式；不保存全 raw metadata、全 allowedValues、Story/AC 正文或会话文件路径。
- [ ] 只缺字段 schema 时不重新查询已确认的项目和 Story 类型；同批同配置只解析一次。
- [ ] 新 session 命中时三个 query 调用数均为 0；配置老旧本身不触发刷新。
- [ ] 当前用户/来源/项目/type 不匹配不能串用；JWT 身份优先，不能仅凭另一个 staffId 借用他人的配置。
- [ ] 旧 jira-user-info.md 仅迁移路由，不伪装成有完整字段 schema 的命中。
- [ ] 当前用户明确值覆盖适用默认值，但不能扩大 reviewed update scope；历史 Ticket Number 不能替代本次更新目标。
- [ ] 只有 issue 和全部所需附件成功的项可更新对应 profile；binding 失败时保留该成功配置，记录 binding pending，不宣布全流程成功。
- [ ] 配置写入失败不回滚成功 Jira，不重放 Jira 写入；明确报告没有跨 session 保存。
- [ ] 未暴露真实 user-workspace 能力时不把对话目录当作持久化；metadata 分页不完整时不声称缓存完整，不臆造工具参数。

## 场景 K：导出接入与读取去重

- [ ] Task、generation handoff 与活动 skill 列表均调用 ceaia-sdlc-jira-export，不加载通用 helper 或其公共模块。
- [ ] Update 正常路径从 upstream 已保存的 Description/AC 和附件 inventory 比对，不新增 getJiraInfos/readJiraAttachments；缺失、已知外部变更和不确定写入恢复分别处理。
- [ ] 首次 create 必须有 Epic 或明确 no-Epic；新的 Epic key 只读验证一次，命中保存的 Epic 不重复查询。
- [ ] 每次设置确认都展示更新 target key 与所有实际字段值；确认不要求用户重填 Story/AC，也不让用户挑哪些字段算必填。
- [ ] 保留整合前 main 的 helper/reviewer 最新修复，不采用实验分支中的旧版本；helper 仅 `SKILL.md` 和 `references/sdlc-gateway.md` 两个入口做新/legacy SDLC 分流，其余 helper 文件和全部 reviewer 文件与该 main 基线一致。
- [ ] 新 exporter 和 helper 同时安装时，普通对话的当前 bundled SDLC handoff 和 Task 均进入 `ceaia-sdlc-jira-export`；即使 helper 先被选中也转交新 exporter，不执行旧 gateway 或重复 Jira 写入。
- [ ] 只有明确选择 legacy SDLC 时才进入旧 gateway，并保留其既有门禁；新 exporter 缺失、失败或门禁未通过时不自动回退旧 gateway。普通非 SDLC Jira 和 Test Case 路由不被转交新 exporter。

## 场景 L：数据库回写与恢复

- [ ] 一条 Story 正常成功有 A（内容/附件）和 B（数据库关联）两次调用；B 恰好五个非空字符串且路径是评分输入 STORY.md。
- [ ] 多条 Story 首次 batch write 后，每个符合条件的成功 resource 单独 B，不丢关联，不交叉路径；首次 payload/各项均不带 markdownReviewRelativePath。
- [ ] B 的 status=400 即使 isError=false，也保留成功票号、标记 binding pending，不重建票、不重复上传、不自动重试。
- [ ] 附件失败或 unknown 的项不进入 B，原已成功 issue 保留；替换新文件未成功前不删除旧文件。
- [ ] update-only 列表按预览顺序逐票，第一个失败/partial/unknown 后不发后续 update；显式 mixed batch 才允许 integrated issuesJson。
- [ ] 内容/附件/关联各编号 attempt 不覆盖；当前 binding 预览覆盖 jira-preview.md 后，原 first-write payload 与结果仍可核查。

记录 GPT-6 Luna 实测的导出阶段耗时、三种 query 调用数、工具返回输入量、数据库 B 调用数及重复读取数。静态验证/离线场景通过不等于真实 Jira 集成通过。


## SPEC Story Score display (2026-10-28)

- [ ] SPEC starts with Result, Reviewer Notes, then Story Score before the first `---`; before real scoring the third field is `Not scored`, which does not block content review.
- [ ] A persisted `ok=true` finite numeric finalScore is copied exactly as `<number> / 100` to all current SPECs for that Story; decimals/low scores are not rounded or fabricated. Missing/non-numeric scores never become zero or a sibling's result.
- [ ] Only the score header changes; body, embedded Story/Test Case table, review Result/Notes and bodyRevision remain unchanged. Edited fileRevision advances.
- [ ] When the primary SPEC revision changed after a passing score, one normal cache/metadata call records that final revision; both raw attempt records remain intact. No force_review or display/rescore loop.
- [ ] The metadata-call result, displayed value and current binding agree before readiness. A disagreement has at most one correction/check, then bounded recovery; parser failure or a low score still blocks Jira.
- [ ] Resume with an unchanged verified header makes no extra write or scoring call. A Story change invalidates/resets its displayed score before new review/scoring.
- [ ] SPEC score display does not alter the current export route's first Jira write and separate database association call. The shown total stays out of Jira Description/AC and other issue fields; current bundled SDLC uses the new exporter, with the old gateway reserved for explicit legacy selection.

## Single automatic repair round — main (introduced 2026-10-29)

- [ ] Initial review and scoring pass without a repair; no extra content review is launched for SPEC header synchronization or score display.
- [ ] Initial review finds several supported defects: generation fixes them in one consolidated batch, increments active/cumulative repair counters once, then runs affected read-only re-review. Unchanged valid gates are retained.
- [ ] The post-repair review still fails: no second artifact-edit batch, additional diagnostic reviewer or unchanged rescore; readiness remains false and the exact recovery decision is returned once.
- [ ] Initial content PASS followed by a supported low-score defect uses the same one-round allowance, then independent re-review and eligible scoring. A later defect cannot start another automatic batch.
- [ ] A low score without a supported correction does not trigger cosmetic edits, force_review or repeated scoring. Missing business evidence waits immediately.
- [ ] After review begins, body/filename/reference formatting repairs share the same allowance; relabeling them mechanical or technical does not bypass it. Derived header-only writes and bounded tool/persistence recovery remain separate.
- [ ] Resume or an older three-round state preserves consumed counters and attempt history; round>=1 does not permit another default repair. A session/stage change or cosmetic rename does not reset the allowance.
- [ ] Genuine new source evidence/user decisions reset only the affected candidate's active evidence-revision allowance. Cumulative repair and review/score counts remain monotonic. An explicitly authorized extra round is finite, scoped and recorded before editing.
- [ ] Existing current PASS results can still continue through final scoring and export after the repair was used; exhausted allowance alone does not invalidate successful work. Repair-budget changes do not alter the required Jira content/attachment write and separate database binding phases.

## Content clarification popup and resume — main (2026-10-30)

- [ ] Exhausted repair with a genuine missing fact produces detailed chat findings (actual full path, section/item ID, current quote or precise absence, source-based reason and exact missing fact), then a real ask_user_question interaction. Popup uses a unique short locator, brief reason and concrete question, without full report/JSON; ambiguous basenames include their candidate folder.
- [ ] Multiple findings sharing one business decision are grouped. Partial answers preserve resolved facts and ask only remaining questions; no repeated full regeneration/review/scoring while material blockers remain.
- [ ] An answer is bound to the candidate/question/current item. A substantive clarification is recorded in new numbered planning evidence and resumes affected Story, dependent Test Case/SPEC generation, independent review, SPEC sync and normal score/parser/display gates. Unaffected candidates are not restarted.
- [ ] An already clear AI defect asks only for a scoped one-extra-repair decision after allowance exhaustion, not invented business facts. Approval adds exactly one authorized round without resetting evidence/cumulative counters; vague continue/dismissal is not approval.
- [ ] A vague/unmatched answer remains waiting; a sufficient actual business answer continues automatically without an extra continue popup. Old state can initialize recovery bindings without restoring consumed repair allowance.
- [ ] Clarification/resume changes preserve the one-round policy, independent review criteria, Story/Test Case/SPEC templates, scorer contract/76 threshold and current export/database binding gates. Check the actual small popup layout in GPT-6 Luna platform testing; offline validation does not verify visual fit.

## 场景 M：保留 main 的独立 reviewer 与普通 Story 关联修复

- [ ] 长篇 Jira 抓取/导出/粘贴记录即使自动保存为 Workspace Markdown，仍按主 Ticket 与已确认 source 走 Jira 评分；parent/Epic/linked/comment 引用不自动扩大评分目标。主目标或来源不明时正常询问，不回退为文件评分。
- [ ] 同 session 已导出的 Story 继续真实修改时，评分读取当前本地 Story 路径；更新原 Ticket 内容成功后完成该轮五字段关联，同 Ticket/同路径的旧关联不能替代新评分。
- [ ] 用户明确只要求回写已有评分时不重新评分；按实际评分调用决定四字段 Jira-mode update 或缺失的五字段 Workspace 绑定。缺必需 ID/路径正常询问，不猜值或以无路径四字段代替。
- [ ] 有效 Jira-mode 评分后自动触发四字段调用，不再询问是否同步；普通 success/pass 或票号返回不被描述为 Jira 分数字段已确认写入。不支持项目的后台触发不得被误报为分数字段成功。
- [ ] 真正未评分的普通 Story、Test Case、其他 Issue 类型和 association-only 保留原导出行为；评分关联失败只恢复缺少的步骤，不重建票或重放成功附件。

## 场景 N：独立 review-requirements

- [ ] 上传包只有 `SKILL.md` 与 `templates/review-report-template.md`；全部审查标准包含在主文件中，没有额外 references 依赖、执行脚本或外部遥测。
- [ ] 用户提供需求和 task plan 时完成两阶段审查并输出填好的报告；只有需求但要求完整审查时，缺失的 task plan 明确保持未完成，不声称完整 PASS。
- [ ] 四个 AC 仅触发拆分评估；紧密且不能独立交付的同一流程不被机械拆票。可理解性 PASS 阈值仍为 4/5。
- [ ] 候选需求问题按原规则由第二审查者确认；平台无法完成时明确标记验证未完成，不冒称独立验证或完整 PASS。
- [ ] 检查 PR 但没有发布授权时只读取/生成报告；用户明确要求后才调用可用评论工具。此 Skill 不调用评分/Jira 导出，也不增加现有 SDLC Task 阶段。
