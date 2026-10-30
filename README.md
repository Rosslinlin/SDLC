# CEAIA SDLC v5.3

`main` 现为统一的 v5.3 当前版本：包含最新 SDLC 导出流程、现有 reviewer 修复及独立 `review-requirements`。此前 v4.2/v5.2 与实验阶段内容保留在 Git 历史中。

所有 Skill 资源均为可读取的 Markdown/JSON 文本；Task 也是 JSON 文本配置。目标平台只需调用已提供的文件、评分、独立审查和 Jira 工具，不运行本地脚本或代码。

活动 Skill：

- `ceaia-sdlc-story-spec-generation`
- `ceaia-spec-review`
- `ceaia-sdlc-jira-export`：仅导出已审查的 Story，保留 SPEC 附件和逐票数据库评分关联。

`jira-createissue-helper` 保留本次合并前 `main` 的最新修复及全部通用功能，供普通 Jira/Test Case 等业务单独使用；新的 SDLC Task 不再加载它。整套 SDLC 的目标运行模型统一为 **GPT-6 Luna**，需在平台运行配置中选择该模型；Task 不添加未经平台确认的模型参数。

Task 和普通对话中的当前 SDLC handoff 都统一交给 `ceaia-sdlc-jira-export`；helper 入口遇到此类 handoff 时只转交，不重复执行旧 gateway。旧 helper-based SDLC gateway 保留给明确选择的旧版兼容流程，不能因新 exporter 不可用而自动回退。

独立评分 Skill：

- `user-story-reviewer`：供非 SDLC 的 Story 评分及已有 Jira 评分回写触发使用。明确只要求回写已有分数时，先按本次评分来源选调用：Workspace 评分在对应内容已成功写入后用五字段带实际评分路径补关联；Jira 直接评分用四字段，不重新评分。已有 Ticket 不能作为选择四字段的理由。评分请求先确定业务来源：长篇 Jira 抓取、导出、粘贴内容即使被平台自动保存成 Workspace 文件，仍提取主 Ticket 并按 `jira_number`/`source` 评分，关联票据不自动扩大评分范围；文件路径和纯格式转换不代表本地修订。实际本地创作或用户明确选择的业务修订（包括同会话导出后继续修改的 Story）才按 `relativePath` 评分。主目标或来源不清时正常询问。Jira 模式有效评分后自动执行四字段 update；Workspace 模式交回本次评分路径、结果版本和已知 Ticket 映射；每轮新评分重置待关联状态，导出/更新后必须由 helper 完成该轮五字段路径关联。内容已更新但漏关联时，只补五字段调用，保留已成功的内容写入。缺少 source、员工号等必填值仍正常询问；来源不从 Ticket 前缀或 `jira-user-info.md` 推断。保留完整评分结果和版本记录，普通工具 success 不代表 Jira 分数字段已写入。它不参与下方三 Skill 安装顺序，也不替代 SDLC 的 76 分门槛。

独立需求与拆分审查 Skill：

- [review-requirements](review-requirements/SKILL.md)：根据原始截图整理，仅包含 `SKILL.md`（含全部审查标准）和 `templates/review-report-template.md` 两个文件，覆盖需求质量、Story/AC/INVEST、可理解性、拆分建议，以及任务映射、粒度、描述、依赖和命名审查。使用平台工具读取文件/PR，纯文本运行，不执行命令或外部遥测；仅在用户要求时发布 PR 评论。单独使用，不自动接入或替换现有 SDLC Task、评分和导出流程。

两个独立 Skill 按需单独安装，不加入 SDLC Task 的三 Skill 列表。

推荐安装顺序：

1. 分别上传/更新三个 Skill。
2. 记录平台返回的实际 skill ID。
3. 在 `SDLC-workflow-v5.task.json` 中，将 `REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID` 替换为新导出 skill 的实际 ID；不要填原通用 helper 的 ID。
4. 导入 task，确认 tools 列表中的评分工具位于最后。
5. 按 `PLATFORM-TEST-CHECKLIST.md` 先跑一个新 Story 和一个现有 Jira 更新场景。

SDLC 默认执行完整 Story/Test Case/SPEC 生成 → 独立内容审查 → SPEC Result 同步 → 最终 Workspace Story 评分 → Jira create/update → Ticket 关联链路，即使初始输入没有写 push/export。新建和更新都用 `STORY.md` 的工作区相对路径评分，调用时把主 SPEC 的 `Review Result`/`Result: PASS` 映射为大小写严格的 `specFileStatus: Pass`，并传入该 SPEC 的工作区相对路径；分数和 SPEC 状态在导出前一起保存。独立 reviewer 的 PASS 只表示内容通过；最终分数也通过后才可导出。评分正常命中缓存时仍保存本次 SPEC 元数据，不为刷新元数据强制重评。GPT-6 Luna 运行时沿用这些明确的阶段门禁和局部修复规则，模型名称不改变评分阈值或工具契约。

每个 candidate / evidenceRevision 默认只有一轮自动修复，由内容审查和最终评分共用；续跑不重置已用额度。未解决的问题以具体定位和简短弹窗收集所需事实或有限追加修复决定，再按受影响范围恢复，所有质量门禁保持有效。

`ceaia-sdlc-jira-export` 在真实、可跨 session 的用户工作区 `ceaia-sdlc-jira/profiles.json` 保存按用户/来源/项目/Story 类型/操作能力区分的精简字段规则、字段 ID 和已确认默认值。首次缺配置时按需查询；成功导出及所需附件完成后保存。后续完整命中直接复用，不因跨 session 或时间经过重新查询；只有缺项、显式变更/刷新或明确字段校验失败才查询受影响部分。不能把对话文件冒充用户级持久化。旧 `jira-user-info.md` 可提供迁移路由默认值，不能替代字段 schema；原 helper 的旧规则不改。

每次导出前确认本次项目、Story 类型、更新 Ticket Number、Epic/Parent 决策和字段值。首次创建仍主动收集 Epic Link/明确无 Epic；命中的已确认 Epic 不重复查询。更新复用生成阶段已保存的 Ticket baseline 和附件清单，不在导出时重复远端读取。字段缺失仍须解决，配置确认不扩大已审查的更新范围。

最终 `jira-preview.md`、payload read-back 和本地附件 preflight 通过后直接调用 `exportJiraByDynamicFields`。每个 Jira Story 及要求的附件操作成功、返回真实 Ticket Key 后，**必须再次单独调用同一工具**，仅传 `staffId`、`almType`、`issueIdOrKey`、`conversationId`、`markdownReviewRelativePath` 五个非空字符串，关联本次最终通过评分的 Workspace `STORY.md`。首次调用不带该路径。两步结果分别保留；关联失败不重建票、不重传已成功附件，也不宣布整条流程成功。

普通非 Test Jira create/update/batch 会把 Markdown rich text 转换为 Jira wiki transport syntax；Test Case 路由完全保留其原有专用格式、换行与 payload 规则。

工具用途、参数边界及各 Skill 的工具配置见 [TOOLS-REFERENCE-2026-10-26.md](TOOLS-REFERENCE-2026-10-26.md)。详细变化和测试要点见 `RELEASE-NOTES.md`、`PLATFORM-TEST-CHECKLIST.md`。
