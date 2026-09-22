# CEAIA SDLC v4.2

本目录是 v4 独立版本，不覆盖 v3。

活动 Skill：

- `ceaia-sdlc-story-spec-generation`
- `ceaia-spec-review`
- `jira-createissue-helper`

推荐安装顺序：

1. 分别上传/更新三个 Skill。
2. 记录平台返回的实际 skill ID。
3. 在 `SDLC-workflow-v4.task.json` 中，将 `REPLACE_WITH_PLATFORM_JIRA_CREATEISSUE_HELPER_SKILL_ID` 替换为 helper 的实际 ID。
4. 导入 task，确认 tools 列表中的评分工具位于最后。
5. 按 `PLATFORM-TEST-CHECKLIST.md` 先跑一个新 Story 和一个现有 Jira 更新场景。

SDLC 默认执行完整 generation → score → review → Jira create/update 链路，即使初始输入没有写 push/export。`jira-user-info.md` 仅供 SDLC 使用：首次创建主动收集 Epic Link/明确无 Epic，后续运行先复验并确认配置。最终 `jira-preview.md`、payload read-back 和附件 preflight 通过后直接调用 `exportJiraByDynamicFields`，不依赖额外审批工具。

普通非 Test Jira create/update/batch 会把 Markdown rich text 转换为 Jira wiki transport syntax；Test Case 路由完全保留其原有专用格式、换行与 payload 规则。

详细变化见 `RELEASE-NOTES.md`。
