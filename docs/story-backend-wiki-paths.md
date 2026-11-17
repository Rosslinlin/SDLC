# Story：评分与 Jira 导出使用同一版本的 `.jirawiki` 正文

## Summary

评分工具和 Jira 导出工具支持路径版 Story JSON，并使用同一版本的 Description 与 AC 正文。

## User Story

作为准备 Jira Story 的用户，我希望评分和导出都读取 `descPath`、`acPath` 指向的正文，以便质量判断与最终写入 Jira 的内容一致。

## Business Context

当前仓库契约要求在 `fields.description` 和 AC 字段中内嵌 Jira Wiki 字符串。新方案改为在 JSON 中保存路径，因此评分与导出都需要识别新格式。

## Scope

路径版 JSON 的 `fields` 保留 `project`、`issuetype`、`summary` 及其他适用字段；根级保留实际 AC 字段 ID `acceptanceCriteria`、`descPath`、`acPath` 和既有附件控制。评分与导出使用相同的正文解析及版本判定结果。现有工具入参、create/update 目标及附件行为保持适用。

## Acceptance Criteria

- **AC-001**：给定新的 Story JSON，当工具读取它，则允许 `fields` 不内嵌 Description、AC；根级 `acceptanceCriteria` 仍是实际 AC 字段 ID，`descPath`、`acPath` 分别定位对应的非空 Jira Wiki 正文。
- **AC-002**：给定两个正文可读取，当评分工具处理 Story，则使用解析后的 Description 和 AC 进行内容验证、AC 识别与评分，不把字段 ID 或路径字符串当作业务正文；现有评分入参与评分规则保持适用。
- **AC-003**：给定 Story 已评分，当 JSON、所引用的任一 `.jirawiki` 正文或其他现有评分输入发生变化，则旧评分与内容确认不能用于导出；即使路径不变、文件内容改变，也需重新评分和确认。
- **AC-004**：给定当前版本已通过评分并获确认，当导出工具按现有单一路径入参处理 Story，则将解析后的 Jira Wiki 正文分别写入 Jira 请求的 `fields.description` 与 `fields[acceptanceCriteria]`。正文措辞、顺序和换行保持一致；路径字段不作为 Jira 业务字段发送。原 JSON 文件、create/update 目标及既有附件操作不因组装请求而改变。
- **AC-005**：给定路径不存在、不可读、超出允许范围、正文为空，或无法确定某路径对应哪一段正文，当评分或导出预检时，则返回可定位到字段和路径的错误；导出预检失败时不发起 Jira 写入，也不回退到过期正文。

## Dependencies and open decisions

- 上游生成流程需提供可解析的 `.jirawiki` 正文及 `descPath`、`acPath`；本 Story 聚焦评分与导出工具对新格式的消费。
- 截图中的 `acPath` 与 `descPath` 指向同一文件。实施前需确定使用两个文件，还是约定同一文件中的分段规则。
- 实施前需确定相对路径的解析基准，以及旧内联 JSON 是否仍需兼容。这些规则不由截图本身证明。
