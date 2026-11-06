# 后端基础：简体中文审阅版

> 对应 [英文 README](../../README.md) 及 PR #44 英文基准提交 `9ec3da125578cfc49f008a83cafaa197802939a6`。本目录仅供人工阅读和验收，不参与运行时加载、检索或评估，不声明支持中文激活；英文源码为唯一运行时真源，有歧义时以英文为准。

`backend-fundamentals@0.1.0` 是尚未发布的源码 Pack，包含 30 条供实现后端服务的 Agent 使用的精简英文 Practice。每条负责一个决策，单独检索出来也可使用。

## 范围与阅读导航

本 Pack 用于常见后端操作的实现，与 `agentic-coding` 互补：后者负责工程判断和证据，本 Pack 提供具体的后端契约与完整性决策。每条 Practice 都采用有顺序的指导步骤，并明确停止条件。

中文 Practice 与英文保持相同相对路径，不包含运行时 YAML frontmatter。英文元数据以普通 Markdown 表格呈现：适用条件和反模式描述译为中文，ID、枚举和技术标识保留原文。本文按类别排列，方便验收。

[`../manifest.yaml`](../manifest.yaml) 由 `lore i18n sync` 生成，用于记录中文稿对应的英文内容摘要，不手工填写摘要，也不改变运行时语言。英文修改后，应先复核译文，再执行同步，不能仅通过刷新摘要来宣称翻译已更新。

| 分类 | Practice | 决策 |
| --- | --- | --- |
| API | [保持安全读取语义](practices/api/preserve-safe-read-semantics.md) | GET/HEAD 不承载请求要求执行的业务变更。 |
| API | [限制集合结果](practices/api/bound-collection-results.md) | 在数据访问路径上限制浏览结果规模。 |
| API | [表达 HTTP 失败](practices/api/express-http-failures.md) | 保留有意义的失败语义，不泄露内部细节。 |
| API | [隔离个性化缓存响应](practices/api/isolate-personalized-cache-responses.md) | 避免共享 HTTP 缓存扩大响应的预期受众。 |
| API | [只绑定声明可写的字段](practices/api/bind-only-declared-write-fields.md) | 限定某操作可以赋值的请求属性。 |
| API | [只允许预期的浏览器来源](practices/api/allow-only-intended-browser-origins.md) | 授予跨源响应读取权限，不将其与服务端授权混淆。 |
| 安全 | [保护入口访问](practices/security/enforce-protected-entry-access.md) | 在调用方无法绕过的边界执行访问控制。 |
| 安全 | [授权目标资源](practices/security/authorize-target-resource.md) | 针对实际动作、资源和租户判断权限。 |
| 安全 | [存储密码哈希](practices/security/hash-passwords-for-storage.md) | 保存专用的密码验证材料，而非可恢复的密码。 |
| 安全 | [日志排除秘密](practices/security/exclude-secrets-from-logs.md) | 在输出日志前选择安全的诊断上下文。 |
| 安全 | [保护 Cookie 认证写入](practices/security/protect-cookie-authenticated-writes.md) | 对浏览器自动附带凭据的写入应用 CSRF 防护。 |
| 安全 | [执行有作用域的限流](practices/security/enforce-scoped-rate-limits.md) | 在真实部署范围内限制预期调用方或操作。 |
| 安全 | [生成上传存储键](practices/security/generate-upload-storage-keys.md) | 不让客户端文件名控制存储路径或替换既有对象。 |
| 安全 | [验证 Webhook 消息签名](practices/security/verify-webhook-message-signatures.md) | 在信任事件或产生效果前验证提供方签名消息。 |
| 安全 | [约束服务端抓取目标](practices/security/constrain-server-fetch-destinations.md) | 在解析和重定向过程中约束实际出站目的地。 |
| 安全 | [只接纳支持的上传内容](practices/security/admit-only-supported-upload-content.md) | 在处理或提供文件前检查并限制真实内容。 |
| 数据 | [数据库执行唯一性](practices/data/enforce-uniqueness-in-database.md) | 防止并发写入破坏唯一性。 |
| 数据 | [组合原子数据库写入](practices/data/group-atomic-database-writes.md) | 让一个本地数据库工作单元一起提交或回滚。 |
| 数据 | [用查询计划论证索引](practices/data/justify-indexes-with-query-plans.md) | 根据有代表性的查询证据与写入成本选择索引。 |
| 数据 | [归还已获取连接](practices/data/return-acquired-connections.md) | 在每条退出路径归还或丢弃已获取的客户端连接。 |
| 数据 | [规划部署连接预算](practices/data/budget-deployment-connections.md) | 将连接池总体需求控制在部署分配额度内。 |
| 数据 | [调用方迁移后退役旧结构](practices/data/retire-old-schema-after-callers.md) | 支持的调用方与迁移写入脱离旧结构后才移除它。 |
| 数据 | [参数化查询值](practices/data/parameterize-query-values.md) | 将数据值绑定与受控 SQL 结构分开。 |
| 数据 | [批量读取必需关联](practices/data/batch-required-relations.md) | 避免逐行关联查询，不引入无界或未授权的数据加载。 |
| 数据 | [事件去重与效果共同提交](practices/data/commit-event-deduplication-with-effects.md) | 将重复判定和本地数据库效果持久化为同一个原子单元。 |
| 数据 | [保护依赖状态的写入](practices/data/guard-state-dependent-writes.md) | 并发竞争时在写入处执行当前状态或版本前置条件。 |
| 数据 | [执行关联行完整性](practices/data/enforce-related-row-integrity.md) | 让所有写入方遵守父子身份与声明的删除规则。 |
| 数据 | [精确表示金额](practices/data/represent-money-exactly.md) | 跨边界保持金额、币种、单位和舍入的精确含义。 |
| 生命周期 | [关闭资源前排空工作](practices/lifecycle/drain-work-before-closing-resources.md) | 停止接收，并在关停预算内处理在途工作，再释放依赖。 |
| 生命周期 | [接收流量前检查就绪](practices/lifecycle/check-readiness-before-admitting-traffic.md) | 声明可用的服务能力，而不是仅声明进程存在。 |

## 边界

这不是后端课程、框架起步模板、架构强制规范或完整安全标准。不规定必须使用微服务、依赖注入、GraphQL、分布式事务、OAuth/OIDC 集成、消息队列、Kubernetes 或某家云服务商。PostgreSQL 专属示例及语义不代表对所有数据库的承诺。

输入校验的责任归属和重试／回退设计仍由相应的 `agentic-coding` Practice 负责。资源授权不同于建立可信调用方身份。数据库唯一性与事务原子性解决不同故障，均不能替代任意读—改—写不变量所需的并发控制。

## 评估与来源

阅读 [来源及归纳边界](SOURCES.md)、[决策选择和相邻边界](CANDIDATES.md)，再使用 [中文验收清单](ACCEPTANCE.md) 审阅 30 条行为预期和 13 个跨 Practice 场景。

仅供评估的英文 [catalog](../../../../fixtures/backend-fundamentals/practice-catalog.yaml)、[queries](../../../../fixtures/backend-fundamentals/queries.yaml) 和 [workflows](../../../../fixtures/backend-fundamentals/workflows.yaml) 覆盖全部 30 个决策。150 条冻结的 query 预期是评估假设，不是运行时 Pack 输入，也不是检索质量证明。英文基准的运行时内容和 fixture 均为英文；本目录补充的是非运行时人工审阅材料，不是中文检索支持。150 条 query 和详细来源 YAML 不另作中文副本。

[源码检查 workflow](../../../../.github/workflows/backend-fundamentals.yml) 校验 Pack 格式、fixture 身份与覆盖，随后使用仓库未改动的 `scripts/eval-project-layer` 辅助脚本及公开 CLI 读取每条候选 Practice，执行一条 keyword 激活查询。不增加或修改评估器，不执行完整检索基准、不调用模型，也不证明下游有效性。更大的本地写作实验及历史 baseline 不属于本次内容 PR。

## 手动检查英文运行时内容

从仓库根目录执行；需要 Python 3.10+、PyYAML，以及支持项目层的 `lore`（`0.1.0-alpha.3` 或更新版本）：

```sh
lore validate packs/backend-fundamentals --json
python scripts/eval-project-layer --pack backend-fundamentals --dir tmp/backend-source-project --lore <lore>
lore context status --project-root tmp/backend-source-project --store-root tmp/backend-source-store --json
lore query "A link preview fetches our GET confirmation URL and accidentally submits the purchase. Where should confirmation happen?" --mode keyword --project-root tmp/backend-source-project --store-root tmp/backend-source-store --json
lore get backend-fundamentals.api.preserve-safe-read-semantics --project-root tmp/backend-source-project --store-root tmp/backend-source-store --json
```

将 `<lore>` 替换为可执行文件路径。辅助脚本会创建可丢弃的本地评估状态，不要指向已有的人工编写项目。这些命令仅检查本地结构和激活，不声称存在 Registry 条目、发布标签、可安装性、语义检索结果或 Agent 效果。
