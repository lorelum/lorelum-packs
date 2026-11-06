# 验收假设中文审阅伴随稿

仅供人工审阅；不参与运行时加载、检索或评估；英文为准。以下内容均为验收假设，非实测结果，不表示已执行后端测试，也不声明中文检索能力；不参与 runtime/query。

英文 fixture：

- [practice-catalog.yaml](../../../../fixtures/backend-fundamentals/practice-catalog.yaml)
- [workflows.yaml](../../../../fixtures/backend-fundamentals/workflows.yaml)

## Practice 决策与对照假设

| 字段 | 内容 |
| --- | --- |
| `fixture_set` | `backend-fundamentals-practice-catalog` |
| `evaluation_only` | `true` |
| `description` | 30 篇英文 Practice 的原子决策与对照假设。次近邻用于区分当前的对照边界。并非实测行为。 |

以下按英文 `entries` 顺序保留完整 `id`、`nearest_neighbor` 和已有的 `secondary_neighbors`，翻译 `expected_behavior` 与 `forbidden_behavior`。触发示例字段 `positive_trigger`、`paraphrase`、`near_miss` 不在此重复翻译，请参见英文 fixture。

### 1. `backend-fundamentals.api.preserve-safe-read-semantics`

- `nearest_neighbor`：`backend-fundamentals.api.express-http-failures`
- `secondary_neighbors`：
  - `backend-fundamentals.security.protect-cookie-authenticated-writes`
- `expected_behavior`（预期行为）：不在安全方法中执行所请求的变更，保留附带的诊断操作，并迁移动作及其调用方，不留下会执行变更的读取别名。
- `forbidden_behavior`（禁止行为）：把幂等视为安全，把日志记录当作被禁止的写入而关闭，或保留会执行变更的 GET 别名。

### 2. `backend-fundamentals.api.bound-collection-results`

- `nearest_neighbor`：`backend-fundamentals.data.batch-required-relations`
- `secondary_neighbors`：
  - `backend-fundamentals.api.express-http-failures`
- `expected_behavior`（预期行为）：在物化前限制获取范围，并保留适当的排序与续取契约，包括可独立增长的子项。
- `forbidden_behavior`（禁止行为）：无限量加载后才切片，要求所有情况都使用游标，或仅凭游标就承诺快照语义。

### 3. `backend-fundamentals.api.express-http-failures`

- `nearest_neighbor`：`backend-fundamentals.api.preserve-safe-read-semantics`
- `secondary_neighbors`：
  - `backend-fundamentals.security.exclude-secrets-from-logs`
- `expected_behavior`（预期行为）：通过现有的状态码、响应头与安全的表示形式表达已知和意外失败；仅保留经过审查、不含秘密的诊断信息。
- `forbidden_behavior`（禁止行为）：把失败抹平成空的成功结果，向响应或日志泄露内部信息，另创响应封装，或在序列化器中添加重试。

### 4. `backend-fundamentals.security.enforce-protected-entry-access`

- `nearest_neighbor`：`backend-fundamentals.security.authorize-target-resource`
- `secondary_neighbors`：
  - `backend-fundamentals.security.verify-webhook-message-signatures`
- `expected_behavior`（预期行为）：在所有真实入口路径上，使用可信身份，在受保护的效果发生之前执行已声明的入口权限检查；复用现有且不可绕过的责任组件。
- `forbidden_behavior`（禁止行为）：依赖隐藏的 UI、伪造的请求头或网络位置，或在每个私有辅助函数中重新实现身份认证。

### 5. `backend-fundamentals.security.authorize-target-resource`

- `nearest_neighbor`：`backend-fundamentals.security.enforce-protected-entry-access`
- `secondary_neighbors`：
  - `backend-fundamentals.security.protect-cookie-authenticated-writes`
- `expected_behavior`（预期行为）：将实际主体或公开访问上下文、动作、资源与租户可见性规则应用于选择和变更，同时保留获准的共享。
- `forbidden_behavior`（禁止行为）：把登录或不透明 ID 等同于权限，强迫所有公开读取进行身份认证，或用仅限所有者的检查替代共享策略。

### 6. `backend-fundamentals.security.hash-passwords-for-storage`

- `nearest_neighbor`：`backend-fundamentals.security.exclude-secrets-from-logs`
- `secondary_neighbors`：
  - `backend-fundamentals.security.authorize-target-resource`
- `expected_behavior`（预期行为）：在所有本地密码路径中使用受支持的专用加盐密码验证值与库提供的验证功能，采用有依据的成本参数；避免复制由 IdP 管理的密码。
- `forbidden_behavior`（禁止行为）：存储明文或可逆的登录秘密，使用快速摘要或固定盐，比较重新生成的加盐字符串，或照抄没有解释的成本常量。

### 7. `backend-fundamentals.data.enforce-uniqueness-in-database`

- `nearest_neighbor`：`backend-fundamentals.data.group-atomic-database-writes`
- `secondary_neighbors`：
  - `backend-fundamentals.data.commit-event-deduplication-with-effects`
- `expected_behavior`（预期行为）：在数据库中对并发写入方强制执行约定的键规则，明确处理已有违规数据，并在有效的事务状态下处理确切的冲突。
- `forbidden_behavior`（禁止行为）：依赖写入前的预查询，任意删除重复数据，认为允许空值的 UNIQUE 隐含必填要求，或在已中止的事务中继续执行 SQL。

### 8. `backend-fundamentals.data.group-atomic-database-writes`

- `nearest_neighbor`：`backend-fundamentals.data.return-acquired-connections`
- `secondary_neighbors`：
  - `backend-fundamentals.data.enforce-uniqueness-in-database`
- `expected_behavior`（预期行为）：在一个绑定到事务的客户端上一起提交必需的本地写入，或将它们全部回滚；区分并发问题与外部效果。
- `forbidden_behavior`（禁止行为）：通过互不相关的池客户端分发事务语句，吞掉必需写入的失败，或声称 SQL 回滚能撤销远程效果。

### 9. `backend-fundamentals.data.justify-indexes-with-query-plans`

- `nearest_neighbor`：`backend-fundamentals.data.enforce-uniqueness-in-database`
- `secondary_neighbors`：
  - `backend-fundamentals.data.batch-required-relations`
- `expected_behavior`（预期行为）：依据有代表性的执行计划证据与成本评估一个具体索引，或在得出不应加索引的结论并找到实际瓶颈后停止。
- `forbidden_behavior`（禁止行为）：强求某个扫描标签，按清单机械加索引，不安全地执行会产生变更的 ANALYZE，或在瓶颈位于别处时凭空提出索引。

### 10. `backend-fundamentals.data.return-acquired-connections`

- `nearest_neighbor`：`backend-fundamentals.data.budget-deployment-connections`
- `secondary_neighbors`：
  - `backend-fundamentals.lifecycle.drain-work-before-closing-resources`
- `expected_behavior`（预期行为）：在所有路径上归还或丢弃每个已获取的客户端，在复用前恢复事务状态，并让所管理的连接池存活到其使用方结束之后。
- `forbidden_behavior`（禁止行为）：仅在成功时释放连接，循环使用已中止或已损坏的状态，每个请求都关闭共享连接池，或把放弃等待混同于取消操作。

### 11. `backend-fundamentals.data.budget-deployment-connections`

- `nearest_neighbor`：`backend-fundamentals.data.return-acquired-connections`
- `secondary_neighbors`：
  - `backend-fundamentals.data.group-atomic-database-writes`
- `expected_behavior`（预期行为）：计算进程与服务的峰值需求预算，包括发布过程和代理拓扑；让连接池上限与等待保持在有依据的容量策略之内。
- `forbidden_behavior`（禁止行为）：使用通用的连接池大小，忽略副本带来的倍增或为其他使用方预留的容量，或把 max_connections 当作健康的查询并发量。

### 12. `backend-fundamentals.security.exclude-secrets-from-logs`

- `nearest_neighbor`：`backend-fundamentals.api.express-http-failures`
- `secondary_neighbors`：
  - `backend-fundamentals.security.hash-passwords-for-storage`
- `expected_behavior`（预期行为）：仅输出必要、结构化且安全的上下文，并审查自动序列化与异常序列化；保留有用事件而不包含秘密值。
- `forbidden_behavior`（禁止行为）：转储任意对象或异常字符串，认为内部日志或调试日志可以例外，或不选择字段而直接抑制全部安全事件。

### 13. `backend-fundamentals.lifecycle.drain-work-before-closing-resources`

- `nearest_neighbor`：`backend-fundamentals.data.return-acquired-connections`
- `secondary_neighbors`：
  - `backend-fundamentals.data.budget-deployment-connections`
- `expected_behavior`（预期行为）：停止接收新工作，在时限内处理完所负责的工作，并在依赖的使用方结束后释放依赖；定义到达截止时间时的行为，但不声称保证完成。
- `forbidden_behavior`（禁止行为）：先于使用方关闭依赖，把套接字关闭或休眠等同于排空完成，或给原本没有队列的进程添加队列。

### 14. `backend-fundamentals.data.retire-old-schema-after-callers`

- `nearest_neighbor`：`backend-fundamentals.data.enforce-uniqueness-in-database`
- `secondary_neighbors`：
  - `backend-fundamentals.data.group-atomic-database-writes`
- `expected_behavior`（预期行为）：以已部署的读取方、写入方、在线数据迁移和受支持的回滚版本为门槛，决定是否进行破坏性 schema 变更；切换后移除临时路径。
- `forbidden_behavior`（禁止行为）：仅依赖当前源码搜索或空闲时的迁移，丢失并发写入，认为逆转 DDL 就能恢复数据，或在停机切换时强制要求双写。

### 15. `backend-fundamentals.data.parameterize-query-values`

- `nearest_neighbor`：`backend-fundamentals.security.authorize-target-resource`
- `secondary_neighbors`：
  - `backend-fundamentals.data.justify-indexes-with-query-plans`
- `expected_behavior`（预期行为）：执行时单独绑定数据值，并将结构性选择映射为固定的获准片段，同时保留访问过滤条件。
- `forbidden_behavior`（禁止行为）：将经过校验或已存储的输入直接插值或拼接进 SQL 语句文本，把引号转义混同于授权，或以手动转义作为主要的 SQL 防御措施。

### 16. `backend-fundamentals.data.batch-required-relations`

- `nearest_neighbor`：`backend-fundamentals.api.bound-collection-results`
- `secondary_neighbors`：
  - `backend-fundamentals.data.justify-indexes-with-query-plans`
- `expected_behavior`（预期行为）：以合适的批次获取有界结果所需的关联数据，保留被替换查询中的每一个子项谓词以及可观察结果。
- `forbidden_behavior`（禁止行为）：为了命中缓存而丢弃隐私过滤条件，预加载整个对象图，强制使用一条 SQL 语句，或改变返回的关联行。

### 17. `backend-fundamentals.security.protect-cookie-authenticated-writes`

- `nearest_neighbor`：`backend-fundamentals.security.verify-webhook-message-signatures`
- `secondary_neighbors`：
  - `backend-fundamentals.security.authorize-target-resource`
- `expected_behavior`（预期行为）：对于使用自动附带凭证的写入及其合法客户端流程，使用持续维护的平台 CSRF 防御；仅对机器调用设置范围狭窄且有依据的豁免。
- `forbidden_behavior`（禁止行为）：把 POST、JSON、CORS 或 Cookie 标志当作普遍有效的证明，仅凭令牌存在就接受请求，或将浏览器令牌应用于仅使用 bearer 凭证的机器路由。

### 18. `backend-fundamentals.security.enforce-scoped-rate-limits`

- `nearest_neighbor`：`backend-fundamentals.data.budget-deployment-connections`
- `secondary_neighbors`：
  - `backend-fundamentals.api.express-http-failures`
- `expected_behavior`（预期行为）：选择针对具体操作的身份或目标预算、可信的来源键以及共享作用域，然后执行准入控制和明确的故障策略。
- `forbidden_behavior`（禁止行为）：信任任意请求头，让多个工作进程的计数器默默倍增，仅凭提交的账户名就认为该账户已通过身份认证，或把存储故障当作配额判定的证明。

### 19. `backend-fundamentals.security.generate-upload-storage-keys`

- `nearest_neighbor`：`backend-fundamentals.security.authorize-target-resource`
- `secondary_neighbors`：
  - `backend-fundamentals.security.exclude-secrets-from-logs`
- `expected_behavior`（预期行为）：根据服务器管理的配置与记录选择目标位置，将显示名称单独处理，并在上传交接过程中保留经过授权的映射。
- `forbidden_behavior`（禁止行为）：信任提交的文件名或键，允许替换无关对象，或把随机键误当作权限证明或完整的内容安全保障。

### 20. `backend-fundamentals.security.verify-webhook-message-signatures`

- `nearest_neighbor`：`backend-fundamentals.data.commit-event-deduplication-with-effects`
- `secondary_neighbors`：
  - `backend-fundamentals.security.protect-cookie-authenticated-writes`
- `expected_behavior`（预期行为）：在信任字段或产生效果之前，使用所配置提供方要求的原始表示形式与受支持的验证器验证其消息。
- `forbidden_behavior`（禁止行为）：仅因请求头存在就接受请求，验证被改动的字节，照搬其他协议，或用签名有效证明本次投递是新的。

### 21. `backend-fundamentals.data.commit-event-deduplication-with-effects`

- `nearest_neighbor`：`backend-fundamentals.data.enforce-uniqueness-in-database`
- `secondary_neighbors`：
  - `backend-fundamentals.security.verify-webhook-message-signatures`
- `expected_behavior`（预期行为）：以正确的作用域认领持久化事件键，并将其与本地效果一起提交；失败时两者一起回滚，保留的证据用于防护已接受事件的重放。
- `forbidden_behavior`（禁止行为）：单独提交标记，把锁定不存在的行当作去重证明，按载荷合并不同事件，或声称远程效果恰好发生一次。

### 22. `backend-fundamentals.data.guard-state-dependent-writes`

- `nearest_neighbor`：`backend-fundamentals.data.group-atomic-database-writes`
- `expected_behavior`（预期行为）：在变更发生时，通过合适的条件语句、锁或隔离策略强制检查当前数量、状态或预期版本；检查结果并返回约定的冲突，而不是盲目覆盖或无条件重试。
- `forbidden_behavior`（禁止行为）：认为普通的事务分组能保护先前的观察结果，忽略受影响行数为零，锁定不存在的行作为预留，冲突后丢掉保护条件，或在重新开始时复用过期的替换值。

### 23. `backend-fundamentals.data.enforce-related-row-integrity`

- `nearest_neighbor`：`backend-fundamentals.data.enforce-uniqueness-in-database`
- `expected_behavior`（预期行为）：通过数据库外键对所有写入方强制执行完整的父项身份关系，选择必填或可选的空值行为以及经过明确选择的删除动作，并安全处理已有的孤儿数据或租户不匹配。
- `forbidden_behavior`（禁止行为）：依赖事前的父项查询，把互不关联的外键当作同租户关系，对所有删除都使用级联，意外将必填租户键置空，或声称有效引用就能为调用方授权。

### 24. `backend-fundamentals.data.represent-money-exactly`

- `nearest_neighbor`：`backend-fundamentals.data.group-atomic-database-writes`
- `expected_behavior`（预期行为）：明确币种和计价单位，解析为精确十进制数或经过检查的整数单位，仅在约定的业务边界应用舍入，并在驱动程序与 JSON 消费方之间保留精度。
- `forbidden_behavior`（禁止行为）：使用二进制浮点数表示权威金额，假定币种一律使用两位小数，默默接受溢出或多余精度，让数据库隐式转换决定策略，或在没有精确传输契约时序列化超出消费方精度的数值。

### 25. `backend-fundamentals.api.isolate-personalized-cache-responses`

- `nearest_neighbor`：`backend-fundamentals.security.authorize-target-resource`
- `expected_behavior`（预期行为）：明确选择响应适用 public、private 还是 no-store；保留真正公开内容的缓存；使 Vary 和部署中的缓存键与表示形式的输入一致；若有意在共享缓存中复用受保护响应，则要求可信分区以及命中之前的访问控制。
- `forbidden_behavior`（禁止行为）：把 no-cache 当作 no-store，认为 Cookie 能阻止存储，依赖源站授权来保护绕过源站的缓存命中，或在仅以 URL 为键的情况下允许公开共享复用个性化响应体。

### 26. `backend-fundamentals.api.bind-only-declared-write-fields`

- `nearest_neighbor`：`backend-fundamentals.security.authorize-target-resource`
- `expected_behavior`（预期行为）：绑定前，按操作和能力投影出可写属性；在嵌套和补丁路径中贯彻契约；从可信状态推导服务器管理的属性；并保留明确授权的管理命令或委托命令。
- `forbidden_behavior`（禁止行为）：把原始请求展开到领域模型，仅依赖拒绝列表或值类型，把编辑目标的权限当作任意属性赋值权限，或意外禁止有意允许的特权字段变更。

### 27. `backend-fundamentals.api.allow-only-intended-browser-origins`

- `nearest_neighbor`：`backend-fundamentals.security.protect-cookie-authenticated-writes`
- `expected_behavior`（预期行为）：选择精确的预期源和凭证模式，使预检与实际响应头一致，保留基于 Origin 的响应区分，并对有意公开且不带凭证的资源保留匿名通配符访问。
- `forbidden_behavior`（禁止行为）：携带凭证时反射任意源，使用通配符允许携带凭证的访问，仅修复预检，或把 CORS 描述为服务器授权、非浏览器准入控制或 CSRF 防护。

### 28. `backend-fundamentals.security.constrain-server-fetch-destinations`

- `nearest_neighbor`：`backend-fundamentals.security.enforce-protected-entry-access`
- `expected_behavior`（预期行为）：在实际连接处约束解析后的 scheme、主机、端口和解析出的地址，对所需的重定向重新检查，并仅保留已声明的内部集成例外。
- `forbidden_behavior`（禁止行为）：认为调用方登录、CORS、HTTPS 语法、主机子串匹配或预先进行的 DNS 查询足以强制执行目标限制；默默允许未经检查的重定向或代理解析。

### 29. `backend-fundamentals.security.admit-only-supported-upload-content`

- `nearest_neighbor`：`backend-fundamentals.security.generate-upload-storage-keys`
- `expected_behavior`（预期行为）：验证实际内容属于受支持类型，且接收和处理预算得到强制执行；让未检查或未通过检查的对象保持不可用，并通过适当的提供策略发布那个确切获准的对象。
- `forbidden_behavior`（禁止行为）：把客户端标签或随机名称当作内容证明，无界缓冲输入，仅按压缩后大小判断归档的成本，发布未通过检查的原始对象，或要求每一种合法产物都必须使用某个特定扫描器或私有下载。

### 30. `backend-fundamentals.lifecycle.check-readiness-before-admitting-traffic`

- `nearest_neighbor`：`backend-fundamentals.lifecycle.drain-work-before-closing-resources`
- `expected_behavior`（预期行为）：只宣告已完成的必需服务能力；使用有界、无副作用的就绪状态观察；区分重启／存活检查与路由准入；并保留可选功能的契约。
- `forbidden_behavior`（禁止行为）：把端口已打开或固定延迟等同于初始化完成，在探针中执行业务写入，每逢依赖故障就重启健康进程，或把排空所有已接收工作作为启动前提。

## 跨 Practice 工作流假设

| 字段 | 内容 |
| --- | --- |
| `fixture_set` | `backend-fundamentals-workflows` |
| `evaluation_only` | `true` |
| `description` | 面向 30 项决策原文的跨 Practice 契约；仅为假设，并非已执行的后端测试。 |

以下完整保留英文 `workflows` 中每个场景已有的字段及列表顺序；未在英文场景中出现的字段不作补充。

### 1. `protected-invoice-update`

- `id`：`protected-invoice-update`
- `scenario`（场景）：一个隐藏的管理按钮调用某个端点，该端点接受调用方提供的发票 ID；普通已认证用户可以直接调用它并更改另一租户的发票。
- `authoritative_facts`（权威事实）：
  - 不经过 UI 也能访问该端点，且没有强制执行的上游访问控制。
  - 仅有组织成员身份，并不意味着有权编辑另一租户的发票。
  - 公开产品浏览有意保持无需身份认证。
- `distractors`（干扰项）：
  - 隐藏按钮就足够了，因为 URL 没有写进文档。
  - 添加登录检查会自动授权访问每个发票 ID。
- `expected_practices`：
  - `backend-fundamentals.security.enforce-protected-entry-access`
  - `backend-fundamentals.security.authorize-target-resource`
  - `backend-fundamentals.api.express-http-failures`
- `correctness_assertions`（正确性判据）：
  - 在受保护路由上强制建立可信的调用方上下文，并依据实际发票与租户策略评估所请求的动作。
  - 拒绝未经授权的变更，不改变发票状态，并按既定 HTTP 契约表达拒绝，不暴露内部信息。
  - 让有意公开的浏览保持公开；不要在每个私有辅助函数中创建身份认证系统。
- `forbidden_behavior`（禁止行为）：
  - 将客户端提供的角色或租户请求头视为权威信息。
  - 应用仅限所有者的规则，移除合法的组织共享。

### 2. `concurrent-order-creation`

- `id`：`concurrent-order-creation`
- `scenario`（场景）：两个工作进程接受相同的外部购买引用，各自插入订单头与明细；明细插入可能中途失败。
- `authoritative_facts`（权威事实）：
  - 一个购买引用在其租户内最多只能标识一个已提交的订单。
  - 订单头和明细记录位于同一个关系数据库中，必须一起变为可见。
  - 外部支付提供方不能通过本地 SQL 事务回滚。
- `distractors`（干扰项）：
  - 写入前检查订单是否存在就能防止竞争。
  - 一个事务会自动使外部支付与 SQL 具有原子性。
- `expected_practices`：
  - `backend-fundamentals.data.enforce-uniqueness-in-database`
  - `backend-fundamentals.data.group-atomic-database-writes`
- `correctness_assertions`（正确性判据）：
  - 使用数据库强制执行的租户与引用唯一性不变量，并定义冲突结果。
  - 一起提交订单头与明细，或回滚失败的本地单元；不要声称涵盖远程支付的原子性。
  - 观察竞争插入和写入中途失败，而不只是检查一个成功请求。
- `forbidden_behavior`（禁止行为）：
  - 因为存在预查询就移除约束。
  - 独立提交订单头，并把不完整的订单状态当作成功。

### 3. `growing-customer-list`

- `id`：`growing-customer-list`
- `scenario`（场景）：客户列表接受任意大的分页大小，执行缓慢的过滤查询，并在查询异常后泄漏已借出的客户端。
- `authoritative_facts`（权威事实）：
  - 该列表用于交互式浏览，而不是批量导出契约。
  - 响应小本身并不能使查询高效。
  - 每个服务副本都共享数据库有限的连接容量。
- `distractors`（干扰项）：
  - 仅靠分页就能修复执行计划和会话泄漏。
  - 不做整个部署范围的计算就增大每个副本的连接池。
- `expected_practices`：
  - `backend-fundamentals.api.bound-collection-results`
  - `backend-fundamentals.data.justify-indexes-with-query-plans`
  - `backend-fundamentals.data.return-acquired-connections`
  - `backend-fundamentals.data.budget-deployment-connections`
- `correctness_assertions`（正确性判据）：
  - 强制执行服务器端分页大小上限，以及适合既定浏览契约的稳定排序。
  - 选择索引前评估有代表性的查询计划，保留对写入成本的权衡。
  - 限制总连接需求，并确保成功与失败操作后都会归还客户端。
- `forbidden_behavior`（禁止行为）：
  - 获取所有行，在将其载入应用内存后才切片。
  - 对每个顺序扫描都强行要求索引，或创建无限连接以掩盖等待时间。

### 4. `bounded-visible-relations`

- `id`：`bounded-visible-relations`
- `scenario`（场景）：分页的公开项目动态流逐个项目获取评论；改为预取可能导致返回私密评论。
- `authoritative_facts`（权威事实）：
  - 查看者可以读取公开评论和自己的私密评论，而不是可见项目的全部评论。
  - 父项分页已经有界且有序。
- `expected_practices`：
  - `backend-fundamentals.data.batch-required-relations`
  - `backend-fundamentals.security.authorize-target-resource`
  - `backend-fundamentals.api.bound-collection-results`
- `correctness_assertions`（正确性判据）：
  - 在批量查询中保留相同的子项可见性谓词，并为每个查看者返回相同的已授权集合。
  - 消除 SQL 数量随父项数逐项增长的问题，同时不加载无界的父项或子项。
- `forbidden_behavior`（禁止行为）：
  - 为使用预取缓存而丢弃隐私过滤条件，或把父项可见性当作对每个子项的权限。

### 5. `safe-http-and-diagnostic-output`

- `id`：`safe-http-and-diagnostic-output`
- `scenario`（场景）：数据库客户端异常包含连接秘密；错误处理器必须返回现有的通用失败，同时记录有用的上下文。
- `authoritative_facts`（权威事实）：
  - 对外的 JSON 错误封装已经确立。
  - 日志目标的访问受限并不意味着允许记录秘密值。
- `expected_practices`：
  - `backend-fundamentals.api.express-http-failures`
  - `backend-fundamentals.security.exclude-secrets-from-logs`
- `correctness_assertions`（正确性判据）：
  - 保留对外状态码、必需响应头和现有错误结构；仅输出经过审查、不含秘密且可关联的诊断字段。
  - 嵌套异常和请求数据中的合成凭证既不出现在公开响应中，也不出现在日志中。
- `forbidden_behavior`（禁止行为）：
  - 将原始异常序列化到受保护日志中，或抑制全部诊断事件。

### 6. `anonymous-download-and-server-upload-key`

- `id`：`anonymous-download-and-server-upload-key`
- `scenario`（场景）：支持门户提供公开附件下载，并允许直接上传到服务器分配的待完成记录。
- `authoritative_facts`（权威事实）：
  - 一些附件是公开的，另一些需要针对具体工单的授权。
  - 客户端提供用于显示的文件名，并能提交任意完成标识符。
- `expected_practices`：
  - `backend-fundamentals.security.authorize-target-resource`
  - `backend-fundamentals.security.generate-upload-storage-keys`
- `correctness_assertions`（正确性判据）：
  - 允许匿名公开下载而不强制登录，同时在缺少授权时拒绝私密附件 ID。
  - 解析获得授权的待完成记录及其由服务器选择的目标位置；具有路径形式的文件名或回传的键不能替换无关对象。
- `forbidden_behavior`（禁止行为）：
  - 将不透明存储名称用作授权，或在完成时接受任意客户端键。

### 7. `signed-repeat-event-local-commit`

- `id`：`signed-repeat-event-local-commit`
- `scenario`（场景）：一个已签名奖励事件的多个副本被并发处理并增加积分；工作进程可能在确认之前崩溃。
- `authoritative_facts`（权威事实）：
  - 提供方对原始请求字节签名，并提供在账户和环境范围内稳定的事件 ID。
  - 完成标记与奖励余额共用一个 PostgreSQL 数据库。
  - 远程通知不属于该本地事务。
- `expected_practices`：
  - `backend-fundamentals.security.verify-webhook-message-signatures`
  - `backend-fundamentals.data.commit-event-deduplication-with-effects`
  - `backend-fundamentals.data.group-atomic-database-writes`
- `correctness_assertions`（正确性判据）：
  - 在使用事件声明或创建完成标记之前，验证所要求的消息。
  - 带作用域的唯一认领与积分变更共用一次提交；回滚后不留下完成标记。
  - 并发重复投递、重启和确认丢失，不会重复产生其去重证据仍被保留的本地效果。
- `forbidden_behavior`（禁止行为）：
  - 验证重新序列化的字节，在效果发生之前提交标记，或声称本地事务可使邮件恰好发送一次。

### 8. `shutdown-with-late-database-write`

- `id`：`shutdown-with-late-database-write`
- `scenario`（场景）：一个已接收的发票请求在关闭流程开始后执行最后的 SQL 写入。
- `authoritative_facts`（权威事实）：
  - 进程拥有一个共享连接池，且部署提供固定的终止时间额度。
  - 进程没有队列或后台消费者。
- `expected_practices`：
  - `backend-fundamentals.lifecycle.drain-work-before-closing-resources`
  - `backend-fundamentals.data.return-acquired-connections`
- `correctness_assertions`（正确性判据）：
  - 停止准入，在已接收工作于预算内结束期间保持依赖可用，之后再关闭连接池。
  - 失败事务适当地归还或丢弃其客户端；卡住的工作到达截止时间时进入已定义的结果，而不是虚报成功。
- `forbidden_behavior`（禁止行为）：
  - 在处理器仍运行时并发关闭连接池，把休眠视为完成，或为执行关闭而引入队列。

### 9. `cookie-write-admission`

- `id`：`cookie-write-admission`
- `scenario`（场景）：一个通过 Cookie 进行身份认证的设置端点具有正确的对象权限，但跨站请求与计数器存储故障需要明确的处理方式。
- `authoritative_facts`（权威事实）：
  - 浏览器自动附带该端点接受的会话凭证。
  - 现有准入策略区分超出配额和基础设施故障。
- `expected_practices`：
  - `backend-fundamentals.security.protect-cookie-authenticated-writes`
  - `backend-fundamentals.security.enforce-scoped-rate-limits`
  - `backend-fundamentals.api.express-http-failures`
- `correctness_assertions`（正确性判据）：
  - 在变更前使用持续维护的 CSRF 防护，同时保留合法的同源请求。
  - 按情况使用可信主体预算或单独的、未经验证的目标账户计数器，不把提交的身份当作已认证身份。
  - 演练约定的有界故障策略或故障安全策略；计数器读取失败不是返回 HTTP429 的依据。
- `forbidden_behavior`（禁止行为）：
  - 为所有 JSON 路由移除 CSRF 防护，存储故障时默默允许无限工作，或仅凭提交的账户名就永久锁定受害者。

### 10. `schema-transition-with-live-writers`

- `id`：`schema-transition-with-live-writers`
- `scenario`（场景）：字段替换与旧工作进程及新 API 副本的运行期重叠，同时还在回填数据行。
- `authoritative_facts`（权威事实）：
  - 两类已部署调用方都能更新旧值。
  - 部署支持分阶段迁移，或明确协调的维护窗口。
- `expected_practices`：
  - `backend-fundamentals.data.retire-old-schema-after-callers`
  - `backend-fundamentals.data.group-atomic-database-writes`
- `correctness_assertions`（正确性判据）：
  - 在收缩 schema 之前确认实际读取方、写入方和回滚版本，并保留回填期间发生的编辑。
  - 当两种表示必须一起更新时，使用现有的本地事务边界；在临时路径的使用方退出后移除这些路径。
- `forbidden_behavior`（禁止行为）：
  - 仅凭当前代码搜索就删除，用过期的回填值覆盖在线编辑，或永久保留过时的双写。

### 11. `exact-related-concurrent-settlement`

- `id`：`exact-related-concurrent-settlement`
- `scenario`（场景）：两个工作进程对最后可用的预留量进行结算，同时记录发票明细及其必需的账本变更；父发票可能被并发删除，金额以带币种的十进制文本传入。
- `authoritative_facts`（权威事实）：
  - 可用数量不得变为负数，过期编辑不得覆盖较新的结算。
  - 每条保留的明细必须标识其自身租户内一张现存的发票；只要这些保留的明细仍存在，就必须拒绝删除发票。
  - 必需的余额和账本写入共用一个数据库；已接受的金额必须从解析到输出始终保留精确的已声明单位。
- `expected_practices`：
  - `backend-fundamentals.data.guard-state-dependent-writes`
  - `backend-fundamentals.data.enforce-related-row-integrity`
  - `backend-fundamentals.data.represent-money-exactly`
  - `backend-fundamentals.data.group-atomic-database-writes`
- `correctness_assertions`（正确性判据）：
  - 在变更生效时强制执行数量或预期版本条件，并检查是否匹配。
  - 对每个写入方强制执行完整的租户／发票关系及其已声明的删除动作。
  - 解析并传递精确币种金额，不经过中间的二进制浮点数，也不假定所有币种都以百分之一为单位。
  - 一起提交必需的本地效果；仅将它们分组并不能保护先前的状态观察。
- `forbidden_behavior`（禁止行为）：
  - 冲突后移除数量谓词，或认为普通的 read-committed 事务能防止更新丢失。
  - 把事前父项查询作为唯一的关系保障，或在没有已声明策略的情况下级联删除保留的发票明细。
  - 认为精确的数据库类型能修复解析或 JSON 序列化过程中已经丢失的精度。

### 12. `authorized-fields-origin-and-cache`

- `id`：`authorized-fields-origin-and-cache`
- `scenario`（场景）：一个已认证的账户编辑器使用独立且可信的浏览器控制台；请求体展开接受了角色变更，CDN 按 URL 缓存个性化回复，而 CORS 反射任意源。
- `authoritative_facts`（权威事实）：
  - 普通编辑可以修改显示字段，但不能修改角色、余额、租户归属或受保护的嵌套属性。
  - 一个单独授权的转移命令可以合法地更改其已声明的归属字段。
  - 只有配置的控制台可以读取携带凭证的账户响应；另一个匿名目录仍可公开缓存。
- `expected_practices`：
  - `backend-fundamentals.api.bind-only-declared-write-fields`
  - `backend-fundamentals.api.allow-only-intended-browser-origins`
  - `backend-fundamentals.api.isolate-personalized-cache-responses`
  - `backend-fundamentals.security.authorize-target-resource`
  - `backend-fundamentals.security.protect-cookie-authenticated-writes`
- `correctness_assertions`（正确性判据）：
  - 对实际目标进行授权，并在每一个可写嵌套层级仅投影该操作允许的赋值。
  - 保持特权转移契约独立，不让普通编辑为所有模型属性赋值。
  - 使预期源的预检许可与实际响应许可一致，不把 CORS 当作变更授权或 CSRF 防御。
  - 避免个性化响应体被不当共享复用，处理已有的不安全缓存条目，并保留真正公开的目录缓存。
- `forbidden_behavior`（禁止行为）：
  - 根据编辑记录的权限推断对每个字段都有权限。
  - 反射任意携带凭证的源，或认为仅有源许可就能隔离缓存的响应体。
  - 为避免定义实际边界而禁用合法的公开访问或特权转移。

### 13. `usable-service-and-untrusted-import`

- `id`：`usable-service-and-untrusted-import`
- `scenario`（场景）：一个新的附件导入器在必需配置加载前就宣告就绪，接受可能重定向到元数据服务的客户 URL，并立即通过生成的键提供下载到的字节。
- `authoritative_facts`（权威事实）：
  - 客户导入只能访问公开 HTTPS 目标；另一个已配置的集成可能有意访问某个私有服务。
  - 上传或抓取的附件在通过必需的实际内容检查以及有限的接收和处理预算检查之前保持不可用。
  - 准入前必须完成必需初始化；可选的指标导出器不决定能否提供导入服务。
- `expected_practices`：
  - `backend-fundamentals.lifecycle.check-readiness-before-admitting-traffic`
  - `backend-fundamentals.security.constrain-server-fetch-destinations`
  - `backend-fundamentals.security.admit-only-supported-upload-content`
  - `backend-fundamentals.security.generate-upload-storage-keys`
- `correctness_assertions`（正确性判据）：
  - 依据必需且可用的能力决定流量准入，将依赖可用性与进程存活状态区分开。
  - 将目标策略应用于实际解析出的连接以及每一次跟随的重定向，不扩大独立的内部例外。
  - 隔离未经检查的字节，强制执行实际大小与处理器预算，并仅发布确切获准的对象或获准的衍生产物。
  - 保留服务器管理的目标位置，同时认识到生成的键并不能证明内容安全。
- `forbidden_behavior`（禁止行为）：
  - 因为套接字已打开就宣告就绪，或因为可选指标不可用就重启所有健康实例。
  - 跟随未经检查的重定向，或在地址获准后进行第二次未经检查的 DNS 查询。
  - 发布未通过检查的原始对象，仅信任 MIME 标签，或把随机存储键当作检查成功。
