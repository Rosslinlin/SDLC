# CEAIA SDLC v5

仓库根目录现为 v5。此前的 v4.2 内容保留在 Git 历史中。

所有 Skill 资源均为可读取的 Markdown/JSON 文本；Task 也是 JSON 文本配置。目标平台只需调用已提供的文件、评分、独立审查和 Jira 工具，不运行本地脚本或代码。

活动 Skill：

- `ceaia-sdlc-story-spec-generation`
- `ceaia-spec-review`
- `jira-createissue-helper`

独立评分 Skill：

- `user-story-reviewer`：供非 SDLC 的 Story 生成后或单独评分时使用。任何带 Ticket Number 的输入（包括上传 JSON）都直接使用评分工具的 Jira 模式；缺少 source 时，按批次一次收集 `WPB`、`ALM`、`DATA`、`FCR` 或 `GO`，不从 Ticket 前缀或 `jira-user-info.md` 推断。Jira 模式只生成 `<TICKET>-score-result.md`，由评分工具自行读取 Jira、保存评分并关联 Ticket，不再配置 Jira 读取或独立数据库回写工具。没有 Ticket Number 的输入才转换为标准 Markdown，生成 `story-001-score-input.md` 和对应结果文件。再次评估仍放在同一文件夹并使用 `-v2` 等版本号。它不参与下方三 Skill 安装顺序，也不替代 SDLC 的 76 分放行规则。

推荐安装顺序：

1. 分别上传/更新三个 Skill。
2. 记录平台返回的实际 skill ID。
3. 在 `SDLC-workflow-v5.task.json` 中，将 `REPLACE_WITH_PLATFORM_JIRA_CREATEISSUE_HELPER_SKILL_ID` 替换为 helper 的实际 ID。
4. 导入 task，确认 tools 列表中的评分工具位于最后。
5. 按 `PLATFORM-TEST-CHECKLIST.md` 先跑一个新 Story 和一个现有 Jira 更新场景。

SDLC 默认执行完整 Story/Test Case/SPEC 生成 → 独立内容审查 → SPEC Result 同步 → 最终 Workspace Story 评分 → Jira create/update → Ticket 关联链路，即使初始输入没有写 push/export。新建和更新都用 `STORY.md` 的工作区相对路径评分，调用时附上主 SPEC 的 `Review Result`/`Result` 字符串及同类相对路径；分数和 SPEC 状态在导出前一起保存。独立 reviewer 的 PASS 只表示内容通过；最终分数也通过后才可导出。评分正常命中缓存时仍保存本次 SPEC 元数据，不为刷新元数据强制重评。GPT-6 Luna 运行时沿用这些明确的阶段门禁和局部修复规则，模型名称不改变评分阈值或工具契约。

在 `jira-createissue-helper` 内，`jira-user-info.md` 仍仅供其 SDLC 路由使用，普通导出/Test Case 路由和独立 `user-story-reviewer` 都不使用。SDLC 首次创建主动收集 Epic Link/明确无 Epic，后续运行先复验并确认配置。最终 `jira-preview.md`、payload read-back 和附件 preflight 通过后直接调用 `exportJiraByDynamicFields`，不依赖额外审批工具。每个 Jira Story 及要求的附件操作成功、返回 Ticket Key 后，再以五个确定字段单独调用同一工具，将 Ticket 与本次最终通过的 Story 评分记录关联；两次调用分别记录结果。

普通非 Test Jira create/update/batch 会把 Markdown rich text 转换为 Jira wiki transport syntax；Test Case 路由完全保留其原有专用格式、换行与 payload 规则。

详细变化和测试要点见 `RELEASE-NOTES.md`、`PLATFORM-TEST-CHECKLIST.md`。
