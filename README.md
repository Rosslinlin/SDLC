# CEAIA SDLC v5

仓库根目录现为 v5。此前的 v4.2 内容保留在 Git 历史中。

活动 Skill：

- `ceaia-sdlc-story-spec-generation`
- `ceaia-spec-review`
- `jira-createissue-helper`

独立评分 Skill：

- `user-story-reviewer`：供非 SDLC 的 Story 生成后或单独评分时使用。每条 Story 在 `outputs/user-story-reviews/` 下独立建一层文件夹：已知 Ticket 时用 `aiwpb-123/AIWPB-123-score-input.md` / `aiwpb-123/AIWPB-123-score-result.md`，未知时用 `story-001/` 文件夹和 `story-001` 文件名前缀。再次评估仍放在同一文件夹，文件名前缀后加 `-v2` 等版本号，不再增加目录层级。先写输入，评分工具返回后才生成结果。每个字段都用 `##` 标题，每条完整 AC 占一个列表项，只转换排版。入口 Skill 按阶段加载三个 references 文件。上传时须同时配置评分工具、Ticket 绑定工具及 conversation/user workspace 读写能力。有明确现有 Ticket 时，复用 `jira-user-info.md` 的 Staff ID/Jira source，首次缺信息才收集；资料齐全直接关联，不再弹出确认，失败后再处理。成功后用 SDLC 相同的文件结构保存身份信息，保留既有 SDLC 目标配置和导出历史。它不参与下方三 Skill 安装顺序，也不替代 SDLC 的 76 分放行规则。

推荐安装顺序：

1. 分别上传/更新三个 Skill。
2. 记录平台返回的实际 skill ID。
3. 在 `SDLC-workflow-v5.task.json` 中，将 `REPLACE_WITH_PLATFORM_JIRA_CREATEISSUE_HELPER_SKILL_ID` 替换为 helper 的实际 ID。
4. 导入 task，确认 tools 列表中的评分工具位于最后。
5. 按 `PLATFORM-TEST-CHECKLIST.md` 先跑一个新 Story 和一个现有 Jira 更新场景。

SDLC 默认执行完整 generation → score → review → Jira create/update 链路，即使初始输入没有写 push/export。在 `jira-createissue-helper` 内，`jira-user-info.md` 仍仅供其 SDLC 路由使用，普通导出/Test Case 路由不使用；独立 `user-story-reviewer` 按自身规则复用该文件。SDLC 首次创建主动收集 Epic Link/明确无 Epic，后续运行先复验并确认配置。最终 `jira-preview.md`、payload read-back 和附件 preflight 通过后直接调用 `exportJiraByDynamicFields`，不依赖额外审批工具。v5 在每个 Jira Story 及要求的附件操作成功、返回 Ticket Key 后，再以五个确定字段单独调用同一工具，将 Ticket 与该 Story 的 Markdown 评分记录关联；两次调用分别记录结果。

普通非 Test Jira create/update/batch 会把 Markdown rich text 转换为 Jira wiki transport syntax；Test Case 路由完全保留其原有专用格式、换行与 payload 规则。

详细变化和测试要点见 `RELEASE-NOTES.md`、`PLATFORM-TEST-CHECKLIST.md`。
