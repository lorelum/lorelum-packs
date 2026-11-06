# 来源与独立归纳边界

> 对应 [英文原文](../../SOURCES.md)。仅供人工审阅，不参与运行时加载、检索或评估；英文为准。详细来源 YAML 保留英文，不在此复制翻译。

固定 Skill 输入的研究快照日期为 2026-11-01。30 条 Practice 使用下列来源组；每份来源映射记录自身研究日期和来源。[sources.yaml](../../sources.yaml) 覆盖安全读取、有界集合、HTTP 失败、受保护入口、资源授权、密码验证材料、数据库唯一性、本地事务、查询计划证据、连接清理以及部署连接预算。这些文件记录来源，不是运行时指令，也不是许可授权。

## 固定的 Skill 输入

| 领域 | 来源 | 固定版本 | 用途 |
| --- | --- | --- | --- |
| API | [wshobson API Design Principles](https://github.com/wshobson/agents/blob/0f73766f80b0b5eb6aa9faaa2fdf90df85a6793f/plugins/backend-development/skills/api-design-principles/SKILL.md) 及其 `references/details.md` | `0f73766f80b0b5eb6aa9faaa2fdf90df85a6793f` | 安全读取、有界集合、对外 HTTP 失败。 |
| 安全 | [wshobson Auth Implementation Patterns](https://github.com/wshobson/agents/blob/be57c0b2e3c05c528ca6132b87410b385718775f/plugins/developer-essentials/skills/auth-implementation-patterns/SKILL.md) 及其 `references/details.md` | `be57c0b2e3c05c528ca6132b87410b385718775f` | 受保护入口、目标资源权限、密码验证材料。 |
| 数据 | [Supabase Postgres Best Practices](https://github.com/supabase/agent-skills/blob/c9be0e931b7930f7d02126d04774d904c381e7d7/skills/supabase-postgres-best-practices/SKILL.md) 及七条关联规则 | `c9be0e931b7930f7d02126d04774d904c381e7d7` | 唯一性、短本地事务、查询证据、连接池及容量。 |

wshobson 版本来自特定路径的提交订阅快照，不表示仓库整体的最新版本。Supabase 输入在 Skill 元数据中声明 MIT。未复刻来源正文或代码：Pack 的指导与示例为独立编写。本文不声称 Pack 的许可会重新许可上游材料。

## 30 条 Practice 的来源组

- [生命周期与敏感诊断](../../references/lifecycle-sources.yaml)：日志排除秘密、关闭资源前排空工作、调用方迁移后才退役已部署数据库结构。
- [查询和请求安全](../../references/query-safety-sources.yaml)：查询值参数化、必需关联批量读取、Cookie 认证写入保护、有作用域的限流。
- [事件和文件边界](../../references/event-files-sources.yaml)：应用掌控上传存储键、Webhook 消息签名验证、事件去重与本地效果的事务性提交。
- [完整性与精确值](../../references/integrity-and-values-sources.yaml)：PostgreSQL 并发、关联约束、精确数值表示，以及明确的传输／币种边界。
- [HTTP 请求与响应边界](../../references/http-boundary-sources.yaml)：共享缓存语义、可写字段投影、浏览器来源响应访问。
- [运行时接纳](../../references/runtime-admission-sources.yaml)：出站目的地约束、文件内容接纳，以及就绪与存活、关停之间的区别。

Practice 指导基于所引研究输入独立编写，而非复制 Skill 正文。每份映射记录支持的主张、限制和作者归纳；没有版本的文档 URL 不是不可变的研究版本锚点。

## 主要技术依据

- [RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html)：安全与幂等方法的区别、GET/HEAD、错误状态和必需响应头。
- [RFC 9457](https://www.rfc-editor.org/rfc/rfc9457.html)：安全的错误披露；Problem Details 是可选格式，而不是强制替换协议。
- [OWASP Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)：执行位置、默认拒绝、对象／动作／关系策略。
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) 和 [argon2-cffi 文档](https://argon2-cffi.readthedocs.io/en/stable/howto.html)：专用加盐验证材料、成本选择、verify 和 rehash API。
- PostgreSQL 18：[约束](https://www.postgresql.org/docs/18/ddl-constraints.html)、[唯一性检查](https://www.postgresql.org/docs/18/index-unique-checks.html)、[事务](https://www.postgresql.org/docs/18/tutorial-transactions.html)、[隔离](https://www.postgresql.org/docs/18/transaction-iso.html)、[执行计划](https://www.postgresql.org/docs/18/using-explain.html)、[索引](https://www.postgresql.org/docs/18/sql-createindex.html)、[结果限制](https://www.postgresql.org/docs/18/queries-limit.html) 和 [连接配置](https://www.postgresql.org/docs/18/runtime-config-connection.html)。
- node-postgres：[连接池 API](https://node-postgres.com/apis/pool)、[连接池使用](https://node-postgres.com/features/pooling) 和 [连接池容量](https://node-postgres.com/guides/pool-sizing)：客户端连接绑定、清理、排队和总体预算。必须核查实际部署驱动的专属行为，不能假定所有驱动相同。

## 独立边界

本 Pack 不从用于发现候选主题的材料中继承无差别加索引、通用提速倍数、固定连接池公式、统一令牌有效期、仅限所有者的授权规则或强制框架。本地原子性不承诺远程效果的原子性，也不承诺任意并发更新的隔离。密码成本和连接数量是部署决策，不是照搬的常量。主要网页文档于对应来源映射记录的研究日期阅读；未版本化的 OWASP 和库文档页面之后可能改变。

示例是构造的说明，不是已观察事件或后端基准。`fixtures/backend-fundamentals/` 下仅用于评估的 catalog、queries 和 workflows 覆盖当前 30 个决策。源码检查 workflow 检查格式有效性、本地激活、全文读回和一条 keyword query；不是完整检索基准或 Agent 效果实验。结构有效性、检索选择和下游行为仍是彼此独立的主张。
