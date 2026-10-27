# CEAIA SDLC v5.3

仓库根目录现为 v5。此前的 v4.2 内容保留在 Git 历史中。

所有 Skill 资源均为可读取的 Markdown/JSON 文本；Task 也是 JSON 文本配置。目标平台只需调用已提供的文件、评分、独立审查和 Jira 工具，不运行本地脚本或代码。

活动 Skill：

- `ceaia-sdlc-story-spec-generation`
- `ceaia-spec-review`
- `ceaia-sdlc-jira-export`：仅导出已审查的 Story，保留 SPEC 附件和逐票数据库评分关联。

`jira-createissue-helper` 原目录及全部通用功能原样保留，供普通 Jira/Test Case 等业务单独使用；新的 SDLC Task 不再加载它。整套 SDLC 的目标运行模型统一为 **GPT-6 Luna**，需在平台运行配置中选择该模型；Task 不添加未经平台确认的模型参数。

独立评分 Skill：

- `user-story-reviewer`：供非 SDLC 的 Story 生成后或单独评分时使用。任何带 Ticket Number 的输入（包括上传 JSON）都直接使用评分工具的 Jira 模式；缺少 source 时，按批次一次收集 `WPB`、`ALM`、`DATA`、`FCR` 或 `GO`，不从 Ticket 前缀或 `jira-user-info.md` 推断。评分工具自行读取 Jira、保存评分并关联 Ticket；评分完成后仅对用户确认的现有 Ticket，额外通过导出工具的四参数调用同步分数到 Jira。Jira 模式仍只生成 `<TICKET>-score-result.md`。没有 Ticket Number 的输入仍转换为标准 Markdown，生成 `story-001-score-input.md` 和对应结果文件，不触发 Jira 分数同步。再次评估仍放在同一文件夹并使用 `-v2` 等版本号。它不参与下方三 Skill 安装顺序，也不替代 SDLC 的 76 分放行规则。

推荐安装顺序：

1. 分别上传/更新三个 Skill。
2. 记录平台返回的实际 skill ID。
3. 在 `SDLC-workflow-v5.task.json` 中，将 `REPLACE_WITH_PLATFORM_CEAIA_SDLC_JIRA_EXPORT_SKILL_ID` 替换为新导出 skill 的实际 ID；不要填原通用 helper 的 ID。
4. 导入 task，确认 tools 列表中的评分工具位于最后。
5. 按 `PLATFORM-TEST-CHECKLIST.md` 先跑一个新 Story 和一个现有 Jira 更新场景。

SDLC 默认执行完整 Story/Test Case/SPEC 生成 → 独立内容审查 → SPEC Result 同步 → 最终 Workspace Story 评分 → Jira create/update → Ticket 关联链路，即使初始输入没有写 push/export。新建和更新都用 `STORY.md` 的工作区相对路径评分，调用时把主 SPEC 的 `Review Result`/`Result: PASS` 映射为大小写严格的 `specFileStatus: Pass`，并传入该 SPEC 的工作区相对路径；分数和 SPEC 状态在导出前一起保存。独立 reviewer 的 PASS 只表示内容通过；最终分数也通过后才可导出。评分正常命中缓存时仍保存本次 SPEC 元数据，不为刷新元数据强制重评。GPT-6 Luna 运行时沿用这些明确的阶段门禁和局部修复规则，模型名称不改变评分阈值或工具契约。

新的 `ceaia-sdlc-jira-export` 在真实、可跨 session 的用户工作区 `ceaia-sdlc-jira/profiles.json` 保存按用户/来源/项目/Story 类型/操作能力区分的精简字段规则、字段 ID 和已确认默认值。首次缺配置时按需查询；成功导出及所需附件完成后保存。后续完整命中直接复用，不因跨 session 或时间经过重新查询；只有缺项、显式变更/刷新或明确字段校验失败才查询受影响部分。不能把对话文件冒充用户级持久化。旧 `jira-user-info.md` 可提供迁移路由默认值，不能替代字段 schema；原 helper 的旧规则不改。

每次导出前确认本次项目、Story 类型、更新 Ticket Number、Epic/Parent 决策和字段值。首次创建仍主动收集 Epic Link/明确无 Epic；命中的已确认 Epic 不重复查询。更新复用生成阶段已保存的 Ticket baseline 和附件清单，不在导出时重复远端读取。字段缺失仍须解决，配置确认不扩大已审查的更新范围。

最终 `jira-preview.md`、payload read-back 和本地附件 preflight 通过后直接调用 `exportJiraByDynamicFields`。每个 Jira Story 及要求的附件操作成功、返回真实 Ticket Key 后，**必须再次单独调用同一工具**，仅传 `staffId`、`almType`、`issueIdOrKey`、`conversationId`、`markdownReviewRelativePath` 五个非空字符串，关联本次最终通过评分的 Workspace `STORY.md`。首次调用不带该路径。两步结果分别保留；关联失败不重建票、不重传已成功附件，也不宣布整条流程成功。

普通非 Test Jira create/update/batch 会把 Markdown rich text 转换为 Jira wiki transport syntax；Test Case 路由完全保留其原有专用格式、换行与 payload 规则。

工具用途、参数边界及各 Skill 的工具配置见 [TOOLS-REFERENCE-2026-10-26.md](TOOLS-REFERENCE-2026-10-26.md)。详细变化和测试要点见 `RELEASE-NOTES.md`、`PLATFORM-TEST-CHECKLIST.md`。
