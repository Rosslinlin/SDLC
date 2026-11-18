# Story：评分与 Jira 导出使用同一版本的 `.jirawiki` 正文

## Summary

评分工具和 Jira 导出工具支持路径版 Story JSON，并使用同一版本的 Description 与 AC 正文。

## User Story

作为准备 Jira Story 的用户，我希望评分和导出都读取 `descPath`、`acPath` 指向的正文，以便质量判断与最终写入 Jira 的内容一致。

## Business Context

Story JSON 通过根级 `descPath` 和 `acPath` 引用两个独立 `.jirawiki` 文件。评分与导出读取这两个文件中的 Description 和 AC 正文，根级 `acceptanceCriteria` 指定实际 Jira AC 字段 ID。

## Scope

路径版 JSON 的 `fields` 保留 `project`、`issuetype`、`summary` 及其他适用字段；根级保留实际 AC 字段 ID `acceptanceCriteria`、`descPath`、`acPath` 和既有附件控制。评分与导出使用相同的正文解析及版本判定结果。现有工具入参、create/update 目标及附件行为保持适用。

## Acceptance Criteria

- **AC-001**：给定新的 Story JSON，当工具读取它，则允许 `fields` 不内嵌 Description、AC；根级 `acceptanceCriteria` 仍是实际 AC 字段 ID，`descPath`、`acPath` 分别定位对应的非空 Jira Wiki 正文。
- **AC-002**：给定两个正文可读取，当评分工具处理 Story，则使用解析后的 Description 和 AC 进行内容验证、AC 识别与评分，不把字段 ID 或路径字符串当作业务正文；现有评分入参与评分规则保持适用。
- **AC-003**：给定 Story 已评分，当 JSON、所引用的任一 `.jirawiki` 正文或其他现有评分输入发生变化，则旧评分保留为历史记录，导出内容需重新确认；即使路径不变、文件内容改变，也要核对当前正文。在原流程要求评分时，取得当前版本的新评分后再导出。
- **AC-004**：给定当前版本已获内容确认并满足所属流程的评分要求，当导出工具按现有单一路径入参处理 Story，则将解析后的 Jira Wiki 正文分别写入 Jira 请求的 `fields.description` 与 `fields[acceptanceCriteria]`。正文措辞、顺序和换行保持一致；路径字段不作为 Jira 业务字段发送。原 JSON 文件、create/update 目标及既有附件操作不因组装请求而改变。
- **AC-005**：给定路径不存在、不可读、超出允许范围、正文为空，或无法确定某路径对应哪一段正文，当评分或导出预检时，则返回可定位到字段和路径的错误；导出预检失败时不发起 Jira 写入，也不回退到过期正文。

## Dependencies and agreed format

- 上游生成流程需提供可解析的 `.jirawiki` 正文及 `descPath`、`acPath`；本 Story 聚焦评分与导出工具对新格式的消费。
- `acPath` 与 `descPath` 使用两个不同的 `.jirawiki` 文件路径，相对路径以当前 session Workspace 根目录为基准。
- 新生成 Story 使用路径格式。通用导出及独立 Reviewer 对已有内联 Story 的兼容按实际后端支持处理，已评分或确认的输入如需迁移，应重新完成对应评分和确认。
- 导出工具使用 `java-base-mcp.exportJiraByDynamicFieldsV2`，业务内容导出传入 `story_json_workspace_path`。
