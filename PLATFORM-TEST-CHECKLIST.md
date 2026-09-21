# v3 平台测试与替换清单

先在测试空间小批量验收，暂不直接替换生产。“通过”需要真实运行结果，不是本地静态检查结果。

## 导入前

1. 备份平台三个 Skill 和 task。本包未修改本机已安装版、原始目录或 v2。
2. 更新三个 CEAIA Skill 及其全部资源。helper 原样附带，不替换 review，无须重新发布。
3. 使用 SDLC-workflow-v3.task.json。三个 Skill ID 沿用截图；新建条目或换环境需换实际 ID。评分工具位于 tools 数组最后。
4. 确认资源读取、独立只读评审、文件持久化、ask_user_question 和 request_user_approval 可用。不需要运行脚本或计算哈希；只使用平台已有工具读取、写入文本及执行业务调用。
5. 工具截图对可选参数类型有冲突：默认只传 relativePath，省略 force_review/metadata。用户明确要求新评审时，按真实 schema 提交 force_review。
6. 核对平台自定义报告/状态解析器对 contractVersion 3 的支持；纯文本修订已把哈希字段替换为 readback-and-revision。task schema_version 仍为 enriched-agent.task/v2。

## 场景与预期

| 场景 | 应观察到的结果 |
|---|---|
| 无代码执行环境 | 全流程不得请求终端、Python 或任何脚本执行；无哈希工具不会单独阻断 |
| 单个正常新 Story | Story→真实评分→测试→SPEC→独立评审；评分通过前不新建下游占位 |
| AC 识别 | 精确 ## Acceptance Criteria 与 AC-001；无缺 AC 警告，计数与正文相符 |
| 原示例低分 | ok=true、finalScore=36 不能放行；修标题不保证业务缺项自动消失 |
| 高分但解析异常 | 缺 AC 警告、parseErrors 或数量冲突仍阻断；定位解析问题 |
| 新旧诊断结构 | sectionScores 或 scoreBreakdown/dimensions 可读取；原始响应保留，不编造缺失字段 |
| 仅 SPEC/测试变化 | 不重写不重评未变 Story；同步嵌入并重审 |
| Story 确实变化 | 旧分失效，重评分后同步下游并重审 |
| 缺证据与三轮预算 | 业务缺口立即问；三轮或连续两轮无进展后停止自动编辑，不暗中第四轮修复 |
| 十个 Story 中一个阻塞 | 路径预算隔离，其余独立候选可继续；未明确移除阻塞项前不判定全批可推 |
| 已有票独立 AC 为空 | Description 非空时继续；source 逐票明确、附件盘点完整 |
| 两个不同附件替换 | 各自保留原义、各自评审、绑定真实 ID，不强制合并 |
| 显式 SPEC.md 文件名 | 经确认的安全名称可用，否则默认 kebab-case |
| 显式 Epic + Stories | 仅明确请求才包含 Epic，每个 Story 绑定自己的 reviewed SPEC |
| 审批后文件/字段变化 | 原审批失效，重新预览与审批 |
| 部分成功或写入超时 | 区分成功/失败/未尝试/未知，不盲重发成功或未知创建 |
| 中断恢复和缓存 | 回读文件和最新状态；确认未变才复用，无法确认则重走受影响门禁；不因无哈希而循环评分，不新增历史目录 |
| 范围隔离 | helper 与 push 独立 Test Case 资源保持原样，本 task 不调用这些分支 |

## 执行与记录

先测单 Story，再测局部修复、已有票更新，最后测十个 Story。Jira 写入只在专用测试项目且获得明确审批后执行。

记录候选 ID、证据版本、真实工具调用次数、各门禁状态、失败位置和恢复结果；不能把未实际调用记录为成功。验收完成后再决定是否替换生产。
