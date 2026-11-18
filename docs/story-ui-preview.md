# Story：在 Story Preview 中展示路径引用的业务正文

## Summary

Story Preview 按可读布局展示 Summary、Description 和 Acceptance Criteria。

## User Story

作为评分和导出前审阅 Story 的用户，我希望在 Preview 中直接阅读 `.jirawiki` 正文，并能切换到 Code 查看原始路径，以便确认业务内容及其对应的 Jira 字段。

## Business Context

新 JSON 用 `descPath`、`acPath` 引用正文。Preview 需要展示解析后的内容，同时保留 Code 中的真实 JSON。当前 HTML demo 展示的是目标布局，不证明生产环境已有文件解析能力。

## Scope

Preview 顶部依次展示 Jira Instance、Project Key、Issue Type；其后以普通内容区展示 Summary、Description 和 Acceptance Criteria。只有 Fields 使用表格。Fields 下方以普通“标签：值”行展示根级 `acceptanceCriteria`、`acPath`、`descPath` 和 `attachments`。Code 展示原始路径版 JSON。

## Acceptance Criteria

- **AC-001**：给定有效的路径版 Story JSON，当用户打开 Preview，则页面顶部依次显示 `jiraInstance`、`projectKey`、`issueType`，并从 `fields.summary` 展示 Summary。
- **AC-002**：给定两个路径的正文可读取，当 Preview 展示 Description 和 AC，则按原有顺序呈现可读的 Jira Wiki 效果，保留标题、列表、强调文字及 AC ID，不把 `h2.` 等格式标记直接当正文显示。
- **AC-003**：给定用户浏览 Preview，则 Summary、Description、AC 是普通内容区；只有 Fields 使用表格。Fields 展示 `project`、`issuetype`、`summary` 及其他适用字段，不重复展示 Description 和 AC 字段。顶部 Summary 与 Fields 中的 Summary 均来自同一个 `fields.summary`。
- **AC-004**：给定用户浏览 Fields 下方的信息，则 `acceptanceCriteria` 字段 ID、`acPath`、`descPath`、`attachments` 以普通“标签：值”逐行显示；`acceptanceCriteria` 的值不被当作 AC 正文。
- **AC-005**：当用户切换到 Code，则看到真实的路径版 JSON；切换视图不修改 JSON 或 `.jirawiki` 内容。若某段正文无法读取，Preview 明确标识受影响的字段，不展示旧正文或将路径冒充为正文。

## Dependency

Preview 需要取得后端 Story 所定义的路径解析结果。AC-005 的具体错误文案和呈现形式由 UI 设计确定。
