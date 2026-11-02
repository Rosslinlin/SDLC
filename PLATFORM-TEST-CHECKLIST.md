# v6.0 平台测试清单

以下用真实平台、评分服务、Jira、附件和数据库验证。分别在平台 GPT-5.6 Luna 与 GPT-6 Luna 上执行；结果栏留空表示尚未记录，不能把本地JSON/链接检查当作平台实测。保留实际会话、调用/响应与票号作为证据，失败只恢复获准的缺失步骤。

## 本地验证记录 — 2026-10-31

- 已通过：3个Skill的YAML/frontmatter及命名/描述规则、5个JSON文件、73个本地文档链接、11个JSON示例及7处嵌套JSON字符串解码、共享标准逐字一致、Task工具/顺序/Skill IDs/schema保持、阶段依赖及git diff格式检查。
- 工具边界检查通过：评分示例为3字段Workspace输入；A示例无绑定路径；B示例恰好5个非空字段；none更新不含附件操作；SPEC分数显示/最终元数据协议与重构前逐字相同。
- 原独立helper、user-story-reviewer、review-requirements目录未修改。已修复跨包资源读取退路，以及batchStatePath/整批覆盖证据的定位。
- 自带Python quick_validate因环境缺PyYAML未运行完成；改用Ruby Psych解析真实YAML，并检查相同frontmatter/命名/描述条件，未安装或更改环境依赖。
- 已准备12个隔离mock场景；GPT-6 Luna的独立轨迹执行被运行额度中断，未产生完成记录，**不计通过**。真实5.6/6 Luna、Jira/附件和数据库结果仍待下表记录。

活动Skill从33文件/201,394字节整理为29文件/132,666字节；Task从14,390字节整理为8,593字节。这是文件体积记录，不是运行耗时或token用量的测量。

## 环境与执行记录

| 模型 | 执行日期 | 平台/Skill版本与实际ID | 会话/测试数据 | 总体结果 | 证据位置 |
| --- | --- | --- | --- | --- | --- |
| GPT-5.6 Luna | | | | | |
| GPT-6 Luna | | | | | |

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| T01 | 三个活动包分别安装且本地资源可读；两包Q1–Q7同文，无sibling文件系统也可审查。核对实际skill IDs/exporter占位替换；兼容Task路径显示v6、schema和10工具不变、score最后，模型由平台配置。 | | |
| T02 | 新Story输入未写push/export仍走完整链；显式preparation-only停在准备完成，阶段调用不越界、不取消后续导出意图。 | | |
| T03 | 两候选按单候选生成→review→score准备；某项阻塞仍保留选择并允许另一项准备。全部选定项和原始需求整批覆盖未通过前不执行A，减少范围需用户决定。 | | |
| T04 | 生成前加载Q1–Q7；遗漏明确原要求、无来源承诺或源冲突被定位。Actor/核心结果未知只阻塞受影响项；未知Author/Branch及无关后台细节不引发虚构或额外追问。 | | |
| T05 | 四个AC的内聚流程不机械拆票；相关异常留在原价值切片。合理单场景标题出现or、负向标签所在行不同不被判业务FAIL；真实混合不可执行场景仍定位其影响。 | | |
| T06 | `*-spec.md`、11列、唯一AC标题、ID/引用、Story/测试镜像由生产precheck核对。机械修正后引用一致；不能仅凭命名风格或列布局产生业务finding。 | | |
| T07 | reviewer独立读取原来源及所有当前产物，不能凭生成器自检/旧PASS/头部过关。报告只有Q1–Q7内容结论、findings、当前bindings、全部SPEC及保留结论；无旧评分/预算/readiness兼容字段。 | | |
| T08 | 每AC/FR有能执行且验证对应行为的测试；拒绝型AC无需虚构Positive伴侣，不强制补齐无关场景，不发明接口、日志或存储。未读的额外SPEC不能被主SPEC的PASS掩盖。 | | |
| T09 | review与评分缺陷共用两批内容修复并保留旧编号记录；无进展提前停止。纯机械原因各限一次，不消耗内容批次但使变更影响的review/score/preview失效；续跑不重置计数。 | | |
| T10 | 真正缺事实时聊天展示文件/条目/当前问题和确切所需事实，弹窗只给简短唯一定位与问题。部分回答只问剩余项；充分回答后恢复受影响内容，已知AI错误不要求重述需求。 | | |
| T11 | S1使用最终Workspace Story relativePath、主SPEC specFilePath及精确Pass；不传jira_number/source。ok=true但低分、数字字符串、parseErrors或已返回AC计数不符均阻断；canonical内容仍识别失败转技术恢复。 | | |
| T12 | S1原始完整JSON及实际入参编号保存。真实总分写入每个SPEC并全文读回；主SPEC版本绑定改变时保留必要正常缓存S2及其独立完整记录。缓存不免持久化，不force刷分，不因显示重写陷入循环。 | | |
| T13 | 低分/parser阻塞的有效数字可诚实显示但不执行失败Story的metadata-only S2，也不导出。头部同步不改业务body；首次读回不符仅一次header重试，再失败等待。 | | |
| T14 | 更新前确认每票source并保存Description、独立AC结果与完整附件清单；Description可用且独立AC空、零附件均可正确处理。none保留本地SPEC但不宣称上传；replace每份SPEC单独绑定和保留义务，不隐式扩展add/delete。 | | |
| T15 | 与实际Jira读取比较Description/AC：AC仅进入确认可写的独立字段，其余Story内容按原顺序保留，包括AC之后章节；Markdown→wiki保留列表/表格/代码/链接与业务措辞，Workspace源不变。 | | |
| T16 | 首次缺配置仅查缺项并保存精简profile；跨session完整命中项目/type/meta查询均为0，不因TTL刷新。按用户/source/project/type/操作能力隔离；create metadata不被无证据当作edit权限。 | | |
| T17 | 每次导出确认实际目标/Story类型/更新Key/Epic/Parent/字段值；首次create明确Epic或no-Epic，确认过的Epic不重复查询。最终准确预览及preflight后直接A，不追加审批弹窗。 | | |
| T18 | A为实际direct参数，首次不含markdownReviewRelativePath或内部handoffType；多create用issuesJson，普通update逐票顺序执行并在首个失败/partial/unknown停止；不发requestJson或未暴露参数。 | | |
| T19 | 仅授权SPEC上传，Test Case/内部文件不上传；多个替换保持各自映射，replace确认新上传后才删除旧件。附件路径/文件名与预览一致，成功附件不重复上传。 | | |
| T20 | 每票A及所有要求附件确认成功后，B恰好五个非空字符串，使用该票实际返回Key和最终评分Story路径。逐批项独立关联；只有markdownReview明确确认才完成，不把普通success当关联成功。 | | |
| T21 | A成功+B失败/unknown保留票/附件，只恢复B；附件失败不进入B。numeric status=400即使isError=false也记失败。未知结果先只读核对，不自动重放任何已成功外部操作；profile保存失败另报。 | | |
| T22 | v5状态迁移保留来源、版本、消耗计数、完整评分记录及已知/未知Jira/附件/B结果；不把unknown转未开始，不通过重放A/B迁移。只恢复真正缺失且允许的步骤。 | | |

## 独立包回归（安装相关包时执行）

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| R01 | 当前SDLC handoff进入专用exporter；只有明确legacy选择才走旧gateway，新exporter不可用不自动退回通用路径。普通create/update/association/batch保持helper业务边界。 | | |
| R02 | 通用Test Case仍使用其专用individual/single payload与真实换行，不套Story评分门槛、AC分区或通用wiki转换，不新增本链路调用。 | | |
| R03 | user-story-reviewer按实际来源选模式：原Jira抓取即使保存为Markdown仍按主票+source；真实本地修订按当前路径。只回写不重评，Jira四字段/Workspace五字段各按原规则，漏关联不重放内容写入。 | | |
| R04 | 独立review-requirements仍为两文件、需求/任务两阶段、候选问题验证及原可理解性规则；不自动接入本Task、不评分/导出，未授权不发PR评论。 | | |

## 观测与问题记录

记录实际工具次数、每候选/阶段耗时，以及平台可提供的input tokens；检查重复读入、重复review和无意义评分调用。S2及B等必要持久化调用须独立计数，不能以减少调用数为由省略。性能与可靠性结论只根据上述真实记录给出。

| 模型/场景ID | 实际调用与耗时/用量 | 观察到的问题 | 已确认外部结果 | 缺失步骤及恢复决定 | 证据 |
| --- | --- | --- | --- | --- | --- |
| | | | | | |
