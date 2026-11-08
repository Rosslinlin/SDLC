# v6.1 JSON4Jira flow 平台测试清单 — preview

版本 `6.1.0-preview.8`，当前主分支为 `main`；2026-11-07 合入原 `codex/sdlc-json4jira-flow` 重构版本。分别在平台 GPT-5.6 Luna 与 GPT-6 Luna 验证路径接口。下列结果栏留空表示尚未记录，合并到 main 不代表平台验证通过；不能把旧 v6 本地/旧接口检查、mock 推演或普通 tool success 当作新接口与数据库实测。

## 环境与执行记录

| 模型 | 执行日期 | 平台/Skill版本与实际ID | 会话/测试数据 | 总体结果 | 证据位置 |
| --- | --- | --- | --- | --- | --- |
| GPT-5.6 Luna | | | | | |
| GPT-6 Luna | | | | | |

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| J01 | 三个包独立安装、资源可读，generation/review 的 Q1–Q7 同文；实际 skill IDs 已核对，exporter 占位替换。兼容 Task 文件显示 `SDLC-workflow-v6.1 JSON preview`，schema 与 10 工具顺序保留，最后一项为 scoreUserStory；模型由平台配置。 | | |
| J02 | 所有用户未写 push/export 也默认继续 Jira-backed 完整流程，入口不提供路线选择；缺信息只补缺项，不推荐 preparation-only。仅用户主动明确只准备/不导出时遵从；阶段调用不越界。默认流程最终仍须评分后的当前内容 HTML 预览及用户明确确认。 | | |
| J03 | 项目/type/meta 查询在生成最终 JSON 前完成；只使用 live schema 真实字段、类型、选项和必要值，不把旧 direct 参数直接猜成 JSON 字段。缓存命中复用，缺项/显式变更才查相应部分。 | | |
| J04 | 创建和更新都读真实跨 session 用户 Workspace；issue 一经确认成功便执行 populated snapshot 的 fresh-read/merge/write/read-back，附件/关联/队列失败也不跳过配置保存。失败/未知 issue 不伪造成功。saved 必须有用户作用域证据，配置/会话文件不混淆。 | | |
| J05 | 每候选只有一个最终 story.json4jira 和保留的 TEST_CASE.md、SPEC；不生成独立 STORY.md。SPEC Story 投影与 JSON 业务字段一致，测试镜像与当前表一致；内部路由/诊断不泄露到 Jira。 | | |
| J06 | 生成前加载 Q1–Q7；review 独立读取原来源、JSON、测试和所有 SPEC。实际遗漏/编造/不可验证行为被定位，or、机械命名/布局、负向标签位置不单独构成业务失败。 | | |
| J07 | 新 Story 按内聚价值拆分而非 AC 数；每 AC/FR 有可执行测试，不为拒绝型 AC 编造 Positive 伴侣，不发明接口、日志、SLAs 或无来源场景。 | | |
| J08 | review/score 共用两批内容修复；机械 cause 一次独立，变更仍使受影响 review/score/确认失效。续跑保留计数，无进展提前停止，外部操作不由本地额度授权重发。 | | |
| J09 | 真实业务缺口以具体文件/条目和所需事实询问；部分回答只追问未解决事实，充分回答后恢复局部内容；未知可选 metadata 不虚构、不造成无关阻塞。 | | |
| J10 | scoreUserStory 实际入参恰为 JSON 工作区相对路径、精确 Pass/Fail、实际 SPEC 路径；主链先独立内容 PASS 和 SPEC 状态读回，不使用 relativePath、jira_number 或旧评分工具。 | | |
| J11 | 每次实际评分的完整原始响应、未知字段及真实输入/标识保存为新的编号记录；ok=true 不等于质量通过，数值 76–100、解析/完整性证据及当前内容绑定按 live 契约验证；不自造 checksum 或强制刷分。 | | |
| J12 | SPEC 不出现 Story Score/真实分数显示；没有为显示分数再调用 S2。合法低分/解析阻塞停止，不以内容 PASS 或缓存命中绕过评分保存。 | | |
| J13 | 当前通过评分的内容生成并展示前端 HTML 预览，清楚呈现目标、业务内容与获准附件操作；用户明确确认当前内容才可调用导出。初始请求、设置确认或旧预览不替代本次内容确认。 | | |
| J14 | 预览后修改 JSON、已评分 SPEC、附件选择、目标或提交字段时，使旧确认/评分失效并重新执行所需审查/评分/HTML 确认；仅 path 相同不能证明 bytes 未变，后端 stale checksum 拒绝不得绕过。 | | |
| J15 | exportJiraByDynamicFields 实际只传 story_json_workspace_path，且等于该候选最终通过评分的路径。不传 direct fields、旧 B 五字段、issuesJson、requestJson 或内部 handoffType。 | | |
| J16 | 后端真实执行已评分内容 checksum/threshold 校验；缺评分、低分、未保存或内容不匹配明确拒绝。记录实际服务错误和已知/未知写入结果，不宣称本地检查等同后端校验。 | | |
| J17 | 正常 create 分别记录真实 issue key/URL、每项附件结果、数据库 linkage、queue 以及 async sync 结果；普通 success 或只有 ticketKey 不足以宣布全部完成。 | | |
| J18 | queue 已接受但 async sync 未完成时明确待处理；只有后端真实完成证据才报告 Jira UI 分数已同步。不臆造查询工具、任务ID或完成字段。 | | |
| J19 | 后端 linkage/queue 失败或未知时保留已创建票据与已成功附件，不执行旧五字段 B、不重建票据。只使用实际支持的核对/恢复接口；不可恢复时准确等待。 | | |
| J20 | 三候选按各自 JSON 路径逐票执行，不用 issuesJson；绑定每票评分、预览确认和响应。前票失败/partial/unknown 时按契约停止并保留其余待办，不静默丢项或猜返回Key。 | | |
| J21 | 超时/断连无法确定写入结果时记 unknown，先以实际可用只读手段核对，不自动重发未知/成功的 issue、附件或数据库步骤；status=400 等业务失败不被 envelope success 掩盖。 | | |
| J22 | 更新读取 source、目标、Description/独立AC状态和完整附件 inventory；根级 issueIdOrKey 与 baseline 一致。先读用户 profiles，按组件复用稳定配置，必要 baseline 读取不被缓存替代；相关 SPEC 默认同步，不因未说 update spec 跳过，真实能力缺口准确 WAITING_TOOL。 | | |
| J23 | 用户明确不改远端附件时省略 attachmentsJson、保留 attachments=[]，本地 SPEC/测试仍更新并经 specFilePath 评分；有原件用已确认 attachmentsJson.replace，无原件用 add。readJiraAttachments 取得真实旧 ID，无需另问技术 ID 或重复证明格式能力。 | | |
| J24 | 旧 v6 会话迁移保留原始状态、编号证据、修复计数及所有已知/未知远端结果；不把旧 STORY.md 评分当新 JSON 评分，不通过调用重放完成迁移，也不补旧 S2/B。 | | |
| J25 | 按实际 metadata 取得 AC 字段 ID，根级 `acceptanceCriteria` 保存该 ID 字符串，不再使用 customFields 包装；正文从 `fields[acceptanceCriteria]` 读取且为 Jira wiki 字符串。SPEC/测试/reviewer/评分 AC 识别指向相同正文，截图示例值不被硬编码。 | | |
| J26 | 输入旧扁平 summary/description/AC 正文或数组/bddScenarios，或旧 customFields.acceptanceCriteria 包装时，在组装/自检修正为当前结构：根级 acceptanceCriteria 仅映射字段 ID，正文在 fields。不得混用两套映射或旧评分继续导出；迁移后按当前包重新评分、确认，不重放已有远端写入。 | | |
| J27 | `fields.description` 被误写为数组/对象时作为结构问题处理，修为保留原业务含义的 Jira wiki 字符串；不因格式修正编造新业务要求或另加 Q1–Q7 门槛。 | | |
| J28 | 根级 `acceptanceCriteria` 的字段 ID 不被计作 AC 正文/AC 数量；读取对应 fields 值，缺少实际正文不能被映射字符串伪装成内容通过。 | | |
| J29 | AC 映射为空/缺失、指向不存在字段，或字段值为空/非字符串时明确结构阻塞并回到生成；不猜 ID、不从旧顶层数组静默兜底、不宣称通过评分。 | | |
| J30 | 根级 projectKey 与 `fields.project.key`、类型名/真实 `fields.issuetype.id` 一致；创建和更新 JSON 均不含 fields.reporter，不因旧模板/profile 又加入它；更新省略 reporter 不清空远端原值。冲突在评分前解决，不硬编码类型 ID 7 或照抄示例员工号/项目。 | | |
| J31 | 首次无适用配置：先读真实用户 Workspace/可信身份，按缺失 source → project → 可跳过附加字段引导；仅展示实际支持的 Epic Link/custom Story Type 等。类型/schema/AC ID 由工具查，staffId 仅必要时问；保持 SDLC Story 与默认 Jira 意图。 | | |
| J32 | 已有适用配置：生成业务文件前展示项目/Story 字段，明确确认沿用/修改/切项目或仅本次覆盖；未变当前 run 批次/续跑不重复问。完整 facts 不重复 discovery，最终评分后仍要当前文件确认。 | | |
| J33 | 用户主动明确只准备/不导出：记录该方向，不执行导出；不把此方向变成默认入口选项。仍只用已知真实值，缺必要元数据则交付已有事实和待补项，不伪造完整可评分 JSON 或实际评分成功。 | | |
| J34 | 模板与实际 Story JSON 不含根级 staffId 或 fields.reporter；评分仍恰三参数、导出仍仅路径。身份配置和查询工具按实际 schema 保留所需 staffId，删除提交中的 reporter 不代表清空 Jira 现有值。 | | |
| J35 | 生成时即将 description/映射 AC 及其他获准富文本写成 Jira wiki，标题 h1./h2.、AC `* *AC-001*: Given ...`；代码、嵌套列表、链接及表格内容无损，已有 wiki 不重复转换。summary、路由/选项、普通文本及未改动更新字段不转换。评分后导出文件不被再次渲染。 | | |
| J36 | SPEC 的 Markdown 投影与 JSON 的 Jira wiki 语义一致，AC ID/数量/措辞/顺序保留，引用不被误算为声明；reviewer 和评分读取同一映射正文。旧 JSON 去除 staffId 或转换格式后，旧评分/确认失效；重新评分确认不重放已成功或未知的远端写入。 | | |
| J37 | 创建模板及实际创建 JSON 使用根级 acceptanceCriteria 字段 ID，无 customFields 包装、fields.reporter 或 issueIdOrKey；实际 AC 正文在 fields[acceptanceCriteria]。字段可写性与类型在评分前核对，不从旧缓存补回 reporter。 | | |
| J38 | 更新模板及实际更新 JSON 在 issueType 后加入准确大小写的 issueIdOrKey，值与可信 source/target/baseline 实际票号一致；按获准增量保留原有义务，通过同一 JSON 路径评分/确认/导出，实际返回 key 与目标一致。普通更新不因目标编码未知而阻塞，也不新增票号工具参数。 | | |
| J39 | 更新缺目标、目标与 baseline 冲突，或误用 issueldOrKey（小写 l）时在提交前阻止并修正/澄清；不得删除目标字段、填空字符串/示例 key 或切换创建继续执行。创建不因证据引用票号而加入更新目标。 | | |
| J40 | 旧包装迁移为根级 AC 字段 ID、去除 reporter 或补入真实更新目标后，保留 AC 业务正文和获准业务值；旧评分/确认失效。附件计划按最新同步意图解析，明确 none 仅保持远端、本地仍同步；replace 据真实能力，不伪造控制或重放成功/未知写入。 | | |
| J41 | update 只有同用户/source/project/type 的完整 create profile 时，复用稳定项目/type/AC ID 等组件，不因 capability 标签差异重跑三个 query；create/edit 权限、requiredness 和默认值不混用。已完成 baseline/附件读取可复用，完整事实下字段 discovery 为零次。 | | |
| J42 | profile 仅缺 AC ID/某字段 descriptor，或收到真实 schema 错误时，只定向补该组件；已有项目/type/baseline 不重查，用户显式刷新/目标变化按实际影响处理，不按 TTL 强刷，不用 createMeta 冒充 edit 权限。 | | |
| J43 | 用户只要求改 Story 标题/描述/AC/业务规则，没有说 update spec：自动同步本地所有相关 SPEC、受影响测试和 Markdown 投影，保留旧义务，读回并完成受影响审查/Header 同步后才评分；不能以旧 SPEC 继续导出。 | | |
| J44 | Story 业务变化且有相关 Jira SPEC：读原件确认身份，将 readJiraAttachments 实际旧 ID 写入 attachmentsJson.replace[].attachmentId，新文件 relativePath/fileName 与受审查输出一致；不以只新增代替替换，不再因旧文档“编码未知”等待。原件歧义只问具体选择。 | | |
| J45 | 用户明确只改 Jira Story、不替换远端附件：省略 attachmentsJson、保留 attachments=[]；本地 SPEC/受影响测试仍同步并评分，结果说明远端附件有意保留。 | | |
| J46 | 完整清单确认无相关或待确认 SPEC：生成本地 SPEC，以 attachmentsJson.add 的 relativePath/fileName 首次交付至原票，保留所有非 SPEC；不编造 attachmentId 或创建新票。 | | |
| J47 | 仅路由/schema 字段变化时检查 SPEC 与 Story 的业务一致性；已一致的正文不为制造更新而改动，实际评分输入变动仍按 checksum/currentness 规则处理，审核编号循环不因此扩大。 | | |
| J48 | 混合附件包含本票 SPEC、FSD、测试报告、截图及其他票 SPEC：从原文/实际 ID/路径/source+ticket 正向识别，只映射本次受影响 SPEC；读取过的业务证据不自动成为删除目标，其余附件保留。 | | |
| J49 | 真实旧 SPEC 名称不规范，普通资料文件名含 spec，或同名不同用途：不按名称/扩展名/MIME/位置/时间推断；以完整原文及本票关联判断，歧义时保留并只问具体选择。 | | |
| J50 | 可能相关原件不可读、ID/正文绑定缺失或清单不全：保留旧件并准确等待补齐，不当作无 SPEC 自动 add，也不猜 ID 或仅凭父角色标签判定 preservation PASS。 | | |
| J51 | 多份不同相关 SPEC 逐原 ID 映射当前输出并保留各自义务；未选 SPEC 不动，多对一须明确授权。完整清单仅非 SPEC 或零附件时才走首次 add，明确 none 仍保留所有远端附件。 | | |
| J52 | 正常 add/replace 使用用户已实测确认的根级 attachmentsJson 对象：add 条目为 relativePath/fileName，replace 多实际 attachmentId；不再设未知 schema/额外能力证明 WAITING。若真实服务拒绝，按实际错误处理，不能静默改为只新增或 none。 | | |
| J53 | 支持的替换操作按每对映射先确认新上传再删选定旧 ID；上传失败/未知绝不删旧，不按同名批量删，不删除新上传返回的 ID 或非 SPEC。最终确认展示旧件、新件、顺序与保留项。 | | |
| J54 | 新上传已成功而旧 ID 删除失败/未知：记 partial，保留新旧实际结果，先核对后仅受支持地恢复缺失删除；不重传已成功上传、不重放 Story 更新，不报替换完成。 | | |
| J55 | 替换完成证据覆盖新上传和每个选定旧 ID 删除（或有文档保证覆盖精确映射的原子结果）；上传列表非空、普通 success、同名新文件出现均不单独证明旧 SPEC 已替换，已知外部变化需重新协调映射。 | | |


| J56 | readJiraAttachments 返回清单与可读原文后，直接绑定实际旧 ID/source/ticket；从同票多附件中确认正确 SPEC，将该 ID 而非文件名、新上传 ID、另一票 ID 或截图示例值写入 replace。 | | |
| J57 | add/replace 的 attachmentsJson 为原生 JSON 对象而非字符串，条目字段和类型完整；同时出现非空旧 attachments 路径数组时先修正并重评分，防止双通道重复上传。 | | |
| J58 | 新建模板默认 add，更新模板默认 replace；确无原 SPEC 时转 add，显式 none 则省略对象并保留 attachments=[]。score/export 均沿用同一修订文件；附件对象不作为工具额外参数。 | | |
| J59 | 旧路径数组迁移为已确认操作对象时保留业务正文与实际原 ID 映射，JSON 字节变化使旧评分/确认失效；正常 replace 不要求重查字段或重复证明后端能力。 | | |
| J60 | 用户确认支持 add/replace 不等于每次调用成功；真实后端错误、附件部分成功、未知删除结果仍准确保存，不发独立 delete、不重传成功上传或重放 Story。 | | |

### 2026-11-06 新增：SPEC 格式稳定性

以下为待执行回归，结果留空，不代表平台已测通过。

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| J61 | 有证据支持的用户动作/状态转换，包括简单展示流程，User Flow 均使用一个非空、闭合并标记 mermaid 的代码块，简单流程画最小图而非纯叙述；不添加无来源后端或错误分支。 | | |
| J62 | 确实不适用流程图时写 Not applicable 和事实理由；缺少会影响业务流程的事实时精确等待，不能用 N/A 或虚构分支掩盖。 | | |
| J63 | 注入空图、错误/缺少 mermaid 标记或未闭合围栏，在既有一次生产自检中利用当前已读文件发现并局部修正；不新增角色、工具、独立报告、评分调用或修复循环。 | | |
| J64 | SPEC 的 Jira Story 投影从三级标题开始，不嵌主标题或同级二级标题；仅下调层级，原 JSON 业务小节、顺序、AC ID、文字、代码和链接不丢失，业务代码中的字面量不被误改。 | | |
| J65 | 同次自检发现未替换模板占位、11 列表格管道/换行问题及独立表/嵌表不一致，保留真实业务字面量；只修受影响格式，不无故全量业务重审或增加 Q 项。 | | |
| J66 | 真实业务流程改变仍独立复核受影响内容；评分前格式修正不额外发起评分，评分后任何 SPEC 改动仍执行既有当前包重评分/确认，不以机械修正绕过当前性。 | | |

## preview.8 持久用户配置与引导回归（待平台执行）

必须记录实际发布 Skill 版本/ID、真实用户读写工具 schema 和作用域证据。不能用仓库修改时间、写入确认或同名会话文件替代跨 session 验证。

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| J67 | 删除用户 profile 文件及父目录后首次创建成功：通过真实用户 writer 创建有意义的路由/type/schema/default 条目并读回，非空模板；打开新 session 读取同条目，展示供沿用确认，不重新收集已有基础项。 | | |
| J68 | 更新成功：fresh-read/merge/write/read-back 同样必达；create 稳定 facts 可复用，不套 create defaults，不以缺 update 条目重跑全部发现。保留其他项目/类型/capability。 | | |
| J69 | issue 已成功但附件/关联/队列失败：先保存独立配置再返回部分失败；lastSuccess 只证明 issue，逐项效果不伪造，远端成功内容不重放。 | | |
| J70 | 通用 helper 收到完整获准上游 JSON、没有完整 metadata snapshot：仍读用户 profile，成功后保存真实路由/type；缺 schema 标 complete=false，不猜权限/选项，不降级已有更完整 schema，不修改原文件/注入旧 defaults。 | | |
| J71 | 同一用户两项目、Story/Epic/Bug 和 create/update 共存：精确选择、更新目标条目，保留其余项；旧 storyType 兼容不重复，新 Story issueType/alias 一致，冲突不猜。SDLC 只选 Story。 | | |
| J72 | 新用户第三问只显示真实支持附加字段，跳过不阻塞、不清旧默认；单次字段不成为默认，明确记住/修改/清除默认只影响选定项目/类型/操作。custom Story Type 不切换 SDLC issue type。 | | |
| J73 | 已有配置先展示并获本次沿用/修改/切项目决定，再生成业务文件；相同当前 run 批次/续跑不重问；最终友好文件预览与明确导出确认仍独立存在，沉默不算确认。 | | |
| J74 | user write 返回 success 但 user read-back 拒绝/缺失/不符：profilePersistence 不为 saved，明确原因/待办；不可改存 conversation 冒充，profile-only 恢复不触发 Jira/评分/附件/正常发现。 | | |
| J75 | 用户存储能力缺失、权限拒绝、JSON 损坏与 not-found 精确区分：不清空 uncertain profile；not-found 可初始化，有能力缺口明确 unavailable，不索取 token/数据库 ID 或杜撰工具。 | | |
| J76 | 通用多项 manifest 部分成功：只为确认成功子项记录实际 lastSuccess/配置，不把 top-level success 分配给未知子项，不重发整批或成功子项。Test Case 特定内容/模式结构保持原样。 | | |
| J77 | 保存时其他项目条目已改变：fresh-read 后合并保留新值；有真实 conditional-write/revision 能力时处理冲突，无能力不声称并发原子性。瞬时 profile-only 重试最多一次。 | | |
| J78 | 平台实际载入 preview.8 generation/export 与 generic 3.0.0-json-preview.4，Task exporter 占位替换成真实返回 ID；user storage 能力实际挂载；重开 session 实读，不能把本机旧包或仓库源码当已部署。 | | |

| 候选/操作 | 实际用户读写工具与作用域证据 | 实际profile路径/版本 | populated匹配条目与其他条目保留 | profilePersistence状态/错误 | 新session读回证据 |
| --- | --- | --- | --- | --- | --- |
| | | | | | |

## 后端结果证据

后端字段名与状态语义以实际 live schema/响应为准；下表是观测类别，不是虚构的响应字段协议。

| 候选/实际JSON路径 | issue结果与票号 | 各附件结果 | 数据库linkage证据 | queue证据 | async sync / Jira UI分数证据 | 未确认项与下一步 |
| --- | --- | --- | --- | --- | --- | --- |
| | | | | | | |

## 独立包边界回归

jira-createissue-helper 独立包已同步本次持久配置生命周期，仍不加入 SDLC Task；user-story-reviewer 与 review-requirements 未改。下表保留业务格式/隔离回归，不把其他接口场景当作新 JSON 主链的完成证据。

| ID | 场景及通过条件 | 5.6 Luna结果/证据 | 6 Luna结果/证据 |
| --- | --- | --- | --- |
| R01 | 新 Task 只加载三个活动 Skill，不因新接口不可用自动转入 helper/legacy 或绕过 HTML 内容确认。普通 Jira/Test Case 等显式独立请求仍使用各自包。 | | |
| R02 | jira-createissue-helper 的原业务字段/确认/Test Case 专用格式与换行规则未被新路径接口改写。 | | |
| R03 | user-story-reviewer 原评分来源/回写规则保持其包原样；新 JSON SDLC 不读取其旧四/五字段规则来补数据库操作。 | | |
| R04 | review-requirements 仍为独立需求/任务审查，不自动加入本 Task，不评分/导出，未授权不发 PR 评论。 | | |
| R05 | 通用 helper preview.3 的创建/仅改单字段的更新/完整上游 JSON/Test Case single 与 multiple 每个 child 都含真实非空 fields.project.key 和 fields.issuetype.id，与 root/target 一致；缺任一、null/空值/错误类型或绑定冲突均在准备或读回时阻止提交。其他业务字段可按范围省略；不在已确认/已评分后静默补字段，不改变 manifest 仅路径结构或重放写入。 | | |
| R06 | 通用 helper preview.4：普通非 Story 批次、混合计划每个非 Story 分组（含单项）、Test Case multiple 的实际 manifest 与全部 child 均为 .json4jira，最终调用路径指向存在且已确认的同名文件。旧 .json 返回准备修正路径/引用并重新确认；不只替换调用后缀、不重放成功/未知批次，不改模板源/profiles 文件名或路径数组协议。 | | |

## 当前分支验证记录

### 2026-11-07 preview.8 本地检查与独立入口/保存推演

本地完成：4 个 Skill 官方 quick_validate 通过（使用现有 Anaconda Python 的 PyYAML，无新增依赖），15 个 JSON 解析、250 处本地 Markdown 链接及 git diff --check 通过。核对 Task schema、10 个工具及顺序、Skill ID 绑定/阶段顺序、评分/导出入参、contractVersion=7、原创建/更新业务 JSON 模板和同文 Q1–Q7 规则未变；exporter 实际平台 ID 仍须发布时填写。

两名独立子代理仅读取当前 Skill 和隔离 mock 原始事实，保存四条具体轨迹及配置结果，父代理读回并核对：通用完整已确认 JSON 只提议一次路径导出，附件失败仍保存配置，保留其他项目和已有更完整 schema/default；更新已成功后读回被拒绝，标 failed 并只保留配置恢复，无 Jira/评分/发现重放；SDLC 已有 Story/Bug 多项目配置停在本次沿用/修改/切项目确认，未将 Bug 默认混入；首次已给 source/project 后仅呈现真实附加字段问题，消费 skip 后本次 no-Epic、不自动选择唯一 subtype 或保存永久默认。四例均未调用真实平台、Jira、用户存储或数据库，不代表跨 session 集成通过；J67–J78 的平台结果继续留空。


尚未在此表记录 v6.1 新接口的真实平台结果。填写时保存实际输入文件、评分请求/完整回应、HTML预览、明确确认、导出调用/完整回应及后续状态证据；区分本地检查、mock 推演和真实服务调用。

| 日期/模型/场景ID | 验证类型 | 实际调用、耗时或用量 | 结果与缺失证据 | 证据位置 |
| --- | --- | --- | --- | --- |
| | | | | |

### 2026-11-03 preview.6 本地检查与配置复用/SPEC 同步隔离验证

本地检查通过：7 个 JSON、105 条文件链接、9 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter（Ruby Psych）、共享质量副本同文、state 7 和 git diff --check。Task schema/skill IDs/10 个工具顺序、评分三参数/导出单路径及 Q1–Q7 业务判定表与 preview.5 一致；本地同步指令位于 review/score 之前，更新模板默认含 SPEC 路径。三个独立包与 origin/main 无差异，原 SPEC Header 与控制状态模板未改。官方 quick_validate 因缺 PyYAML 未完成，不将替代检查记作官方通过。

隔离样例使用规范化 mock 用户 profile/baseline/附件清单。子代理保存三套真实 JSON、SPEC、测试表、manifest 和决策记录，父代理完成最终读回及独立解析：三例均复用 capability=create 的稳定配置，discoveryQueries 为空，未套用 create 默认 priority；未提 SPEC 的 Story 改动也同步了投影、Summary/flow、FR/SC/实体及新 TC-003，保留原 AC-001/002 与测试，嵌入测试表与独立文件一致。有相关 SPEC 时计划 replace 并保留原 ID/无关附件；明确远端保留时 attachments=[] 但本地仍更新；无相关 SPEC 时计划首次 add。模拟替换编码未提供的样例准确保留 WAITING_TOOL，没有编造替换控制或服务成功。

另一个仅缺 AC 映射的配置样例只计划一次 queryJiraCreateMetaFields，复用已知项目/类型且未猜 AC ID，没有执行真实查询。以上没有运行独立内容 review、评分、用户确认、Jira 写入/附件替换或数据库/队列服务，也不代表 Luna 模型平台实测；J41–J47 的真实平台结果继续留空。

### 历史记录：preview.5 — 2026-11-03 本地检查与创建/更新隔离验证

以下是 preview.5 当时的检查，不验证 preview.6 的跨模式缓存复用、自动本地 SPEC 同步和默认远端同步计划；J41–J47 等真实平台结果尚未记录。

本地检查通过：7 个 JSON、100 条文件链接、9 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter（Ruby Psych）、同文共享质量副本、state 7 和 git diff --check。Task schema/skill IDs/10 个工具顺序、评分三参数/导出单路径及 Q1–Q7 业务判定表与 preview.4 一致。解码创建/更新模板确认根级 acceptanceCriteria 为字段 ID、正文在对应 fields 值中，无 customFields/reporter/root staffId；创建省略目标，更新使用准确 issueIdOrKey。三个独立包与 origin/main 无差异，SPEC Header/控制状态模板未改。官方 quick_validate 仍因缺 PyYAML 未运行完成，不将替代检查记为官方通过。

独立子代理使用当前资源与规范化 mock metadata/profile/baseline，仅完成两组 assembly/Markdown projection。父代理实际解码核对：创建没有把来源票当更新目标；更新使用指定目标，保留原标题及 AC-001/002，仅新增已要求的提交日期 AC-003。旧 profile/descriptor 中 reporter 未带入，动态 AC 字段、查询身份边界、labels、链接/行内代码/日期、Markdown 语义及 none 的空附件列表符合给定事实。没有完整 SPEC/测试表/生产全量 precheck、review、评分、用户确认或真实 Jira/数据库调用，也没有目标 Luna 模型平台实测；J37–J40 等真实结果栏继续留空。

### 历史记录：preview.4 — 2026-11-03 本地检查与格式隔离验证

以下是 preview.4 当时的检查，不验证 preview.5 的根级 AC 字段映射、reporter 移除及更新目标编码；相关真实平台结果尚未记录。

本地检查通过：6 个 JSON、95 条文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter（Ruby Psych）、共享质量副本一致、state 7 和 git diff --check。Task schema/skill IDs/10 个工具、评分三参数/导出单路径及 Q1–Q7 业务判定表与 preview.3 一致。解码模板确认无根级 staffId，description/映射 AC 为 Jira wiki 且含真实换行。三个独立包与 origin/main 无差异。官方 quick_validate 因缺 PyYAML 未运行完成，未将替代检查记作官方校验通过。

独立子代理使用当前生成资源和规范化 mock metadata/profile，仅完成隔离的 assembly/Markdown projection。父代理实际解码产物，确认动态 AC 字段、两个 AC 的内容与顺序、获准 reporter/labels/选项对象、链接目标、版本/日期、表格和精确代码正文均保留，代码中的 AC-999 未被计作验收项。子代理读回后修正过一次链接闭合符，最终产物核对通过。附件仅为后续计划路径，没有完整 SPEC、测试表、review、评分、前端确认或任何真实 Jira/数据库调用；该离线验证不代表 GPT-5.6 Luna / GPT-6 Luna 平台实测。J34–J36 等真实结果栏继续留空。

### 历史记录：preview.3 — 2026-11-03 本地检查与入口隔离验证

以下是 preview.3 当时的检查，不验证 preview.4 的根级 staffId 移除与 Jira wiki 文本格式；J34–J36 的真实平台结果尚未记录。

本地检查通过：6 个 JSON、87 条文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter（Ruby Psych）、共享质量副本一致、state 7 和 git diff --check。Task schema/skill IDs/工具列表、评分三参数/导出单路径、JSON 模板及 Q1–Q7 内容保持 preview.2 原样；质量标准版本仍为 6.1.0-preview.2。

GPT-6 Luna 使用最终入口资源，对三个给定 mock 场景产出具体问题与状态。首次无配置：只合并询问来源/staffId/项目名称或已知 key，jiraExportRequested=true；完整已确认 profile：不提问、不重复 query，可继续 assemble；用户主动要求不导出：jiraExportRequested=false，仍补问最终 JSON 所需的缺失配置。根代理另行解析轨迹核对问题、参数范围与状态。三个场景均未提出流程模式选择，也未生成/评分/导出文档或执行真实服务调用；这是入口阶段的离线推演，不能作为 J31–J33 的平台实测。耗时/token 与 GPT-5.6 Luna 未测，平台结果栏留空。

### 历史记录：preview.2 — 2026-11-03 本地检查与 assemble 隔离验证

以下为 preview.2 当时的检查，不验证 preview.3 的默认入口与缺项补齐行为；新增 J31–J33 的真实平台结果尚未记录。

当时本地检查通过：6 个 JSON、85 条文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter、共享标准副本一致、state 7 和 git diff --check；Task schema/skill IDs/工具列表及 Q1–Q7 业务判定表与 preview.1 一致。模板经 JSON 解码检查字段对象、动态 AC 映射、字符串类型、身份/项目关系及 Markdown 换行；旧顶层字段已移除。官方 quick_validate 仍因缺 PyYAML 未完成，frontmatter 使用 Ruby Psych 解析检查。

GPT-6 Luna 当时读取 preview.2 generation 资源，以与截图不同的模拟项目、Story 类型 ID 和 AC 字段 ID，完成一次仅 assemble 阶段的 JSON 生成。根代理独立解码实际产物，确认两个 AC 从真实模拟映射字段读取、描述与 AC 均为有实际换行的 Markdown 字符串、未照抄截图常量；附件仅保留获准的后续 SPEC 路径，未声称文件存在。没有执行完整生产 precheck、review、评分、预览确认或 Jira/数据库调用，不代表 J25–J30 的真实平台测试通过。平台结果栏继续留空；模型耗时/token 未测。

### 历史记录：preview.1 — 2026-11-02 本地检查与 GPT-6 Luna 隔离推演

以下是 preview.1 当时的检查，不验证 preview.2 的 fields/动态 AC 映射结构。新结构的真实平台结果尚未记录。

当时开发检查通过：6 个 JSON 文件解析、83 条本地文件链接、8 个 fenced JSON 示例、3 个 Skill 的 YAML/frontmatter/命名、两个 Q1–Q7 副本逐字一致、state contract 7、Task schema/skill IDs/10 工具顺序（仅评分 ID 更新）、评分三参数与三个导出单参数示例、独立三目录无修改、git diff --check。官方 quick_validate 因环境缺少 PyYAML 未完成；frontmatter 改由 Ruby Psych 解析并检查同类约束，未安装依赖。

GPT-6 Luna 当时读取 preview.1 包文本，使用给定 mock 文件状态/工具结果生成事件和具体调用提议；未调用真实评分、Jira、数据库或预览服务。根代理另行解析完整轨迹，核对六个场景的调用数量及参数键值。耗时、token、GPT-5.6 Luna 与真实后端行为未测；以上平台结果栏保持空白。

| 隔离场景 | 实际推演结果 |
| --- | --- |
| 当前评分、内容确认及后端 mock 分项成功 | 仅提议一次单路径导出；报告异步分数同步待处理，无 B。 |
| JSON 从已评分/确认的 rev3 改为 rev4 | 拦截旧评分/确认；没有导出提议。 |
| 已发出导出但超时、无法核对结果 | 保留 unknown/WAITING_TOOL；没有重放提议。 |
| 评分返回 SKIPPED、无数值 | 不认定为通过；没有导出提议。 |
| 已有票 none 更新但缺目标 schema/score-only SPEC 支持 | WAITING_TOOL；没有猜字段、新建或导出提议。 |
| 当前审查/头部同步完成，尚未评分且未确认导出内容 | 提议 scoreUserStory 恰三参数，保存完整 mock 响应，不改 JSON/SPEC 分数；等待内容确认，没有导出提议。 |

这些结果只验证给定 mock 下的流程选择和参数形态，不能证明评分落库、附件上传、checksum、数据库关联或队列执行已通过集成测试。

## 历史记录：v6.0 本地验证 — 2026-10-31

以下只针对旧 Markdown 评分 / S1–S2–A–B 接口，不能作为 v6.1 新路径接口的验证结果：

- 当时通过 frontmatter/命名检查、5 个 JSON、73 个本地链接、11 个 JSON 示例与 7 处嵌套 JSON 字符串解码、共享标准逐字一致及旧 Task/schema/工具顺序检查。
- 当时检查旧三字段 Workspace Markdown 评分、旧 A direct payload、五字段 B、none 无附件与 SPEC 分数显示；这些旧调用形态不再用于新 JSON 主链。
- 独立三个包当时未改。曾准备 12 个隔离 mock 场景；GPT-6 Luna 轨迹执行被额度中断，未产生完成记录，不计通过。
- quick_validate 曾因缺 PyYAML 未运行完成，改用 Ruby Psych 验证 frontmatter；未安装环境依赖。
- 当时文件体积从 33 文件/201,394 字节整理为 29 文件/132,666 字节，Task 从 14,390 字节整理为 8,593 字节。仅为历史体积记录，不是新分支运行耗时/token 测量。
