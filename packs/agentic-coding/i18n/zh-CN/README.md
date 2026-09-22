# `agentic-coding` 简体中文阅读版

这里是未发布的 `0.6.0` 候选版本 34 条 canonical Practice 的中文 companion，供人类阅读和审查 Pack 的判断质量。它不是逐词翻译，也不参与安装、检索或运行时注入。ID、元数据、反模式 ID 和运行时行为仍以 [`../../practices`](../../practices/) 中的英文 canonical 文件为唯一真源；中英文有冲突时，以英文为准。最新已发布版本仍为 `0.5.1`，直到新的不可变 ref 和 Registry 条目存在。

## Pack 简介

面向 AI Agent 的软件工程决策指导：澄清目标、权威来源、验收与非目标；控制范围与停止条件；让决策集合（计划、验收标准、检查单、规范）不膨胀到超出它要做的决定；作出与风险相称的实现与验证选择；不让校验、权限、确认类检查挡死验收路径；提交前做减法评审；以可信证据处理交接、委托代理、交付、恢复与上下文重建。

每个文件与 canonical 保持同一相对路径，仅含中文标题和六个正文章节，不含 runtime frontmatter。同步 digest 由 Lorelum 根据格式化后的英文 Markdown 生成，不应手工修改。

本次修订（0.6.0）：`保持验收路径可完成` 扩展到信息隐藏——在一个面上藏起一个事实与加闸门过同一个删除测试，只有指名谁会在那里看到它而受害才成立；脱敏移到材料离开机器的边界（导出打包、外发反馈），不再降级本地面。`applies_when`、反模式与示例补入该形态，fixture 查询集为该 Practice 增加第四条正向查询。

`agentic-coding-v0.5.0` 已作为不可变 ref 通过官方 Registry 发布。2026 年 9 月 21 日已用官方安装路径解析该 ref、完成 Pack 解码并读回 34 条 Practice；这只证明安装与物化链路，不证明检索效果或下游 Agent 行为。`agentic-coding-v0.5.1` 为仅元数据发布：目录描述改写以点名 0.5.0 新增的两条规划校准 Practice 的触发时刻，Practice 内容不变；2026 年 9 月 21 日已用官方安装路径解析该 ref（`954324c`）、完成解码并回读 34 条 Practice ID。`0.6.0` 为未发布候选，依据为 2026-09-22 脱敏后的内部试用反馈（见 [SOURCES.md](../../SOURCES.md)）；发布与安装验证记录将在 tag 创建后补记。

## 导航（34 条）

### 需求（3）

- [先说清用户真正要得到什么](practices/requirements/ground-user-goal.md)
- [同时写清完成条件和不做什么](practices/requirements/define-acceptance-and-non-goals.md)
- [多份要求冲突时先确认听谁的](practices/requirements/resolve-source-authority.md)

### 规划（6）

- [开工前定下范围和停止条件](practices/planning/decide-scope-and-stop-conditions.md)
- [从关切推导承诺集，不从条目堆叠](practices/planning/derive-committed-set-from-concerns.md)
- [保持验收路径可完成](practices/planning/keep-acceptance-path-completable.md)
- [按用户完成的整条能力来检查计划](practices/planning/map-plan-to-user-capability.md)
- [为每项验收要求安排够用的最小证明](practices/planning/plan-sufficient-evidence.md)
- [按失败代价决定投入多少工程工作](practices/planning/scale-work-to-risk-and-cost.md)

### 实现（9）

- [在能完整工作的方案里选最不复杂的](practices/implementation/choose-smallest-sufficient-design.md)
- [暴露新能力前确认用户能依赖什么](practices/implementation/confirm-product-surface-expansion.md)
- [新写代码前先查仓库里有没有](practices/implementation/inspect-and-reuse-existing-capability.md)
- [没有未决判断，就不要扩大调查范围](practices/implementation/limit-investigation-to-current-decision.md)
- [让负责数据或规则的组件统一作决定](practices/implementation/preserve-responsibility-boundaries.md)
- [在真正负责该事实的边界校验](practices/implementation/validate-at-the-owning-boundary.md)
- [让恢复有明确负责人和可理解的结果](practices/implementation/make-recovery-behavior-explicit.md)
- [新事实改变任务时暂停并重做计划](practices/implementation/replan-on-material-drift.md)
- [把安全且临时的假设明确写出来](practices/implementation/surface-unconfirmed-assumptions.md)

### 测试（4）

- [每个测试都要说明自己保护哪项契约](practices/testing/anchor-tests-to-requirements.md)
- [断言用户或调用方真正能依赖的结果](practices/testing/assert-observable-behavior.md)
- [检查失败后先判断谁错了，再改代码或测试](practices/testing/classify-failure-before-changing-test.md)
- [永久回归保护必须有长期理由](practices/testing/justify-regression-protection.md)

### 验证（3）

- [旧的验证结果只用于它仍覆盖的文件和环境](practices/verification/bind-evidence-to-artifact-state.md)
- [发现缺少验证后：补测、缩小目标或明确未完成](practices/verification/close-or-declare-evidence-gaps.md)
- [把每项验收要求对应到实际检查结果](practices/verification/map-evidence-to-acceptance.md)

### 审查（2）

- [提交前删掉没有必要的改动](practices/review/run-subtractive-review-before-commit.md)
- [按审查意见改代码前先确认它成立](practices/review/validate-findings-before-action.md)

### 交付（2）

- [完成声明只说证据证明的部分](practices/delivery/claim-only-supported-outcome.md)
- [只报告会改变接收方决策的剩余问题](practices/delivery/report-material-residuals.md)

### 纠正（1）

- [确认纠正后把工作复位到权威基线](practices/correction/restore-authoritative-baseline.md)

### 上下文与委派（4）

- [委派前给被委派的 Agent 足够的决策上下文](practices/context/give-delegated-agents-decision-context.md)
- [上下文丢失后先重新落地](practices/context/reground-after-context-loss.md)
- [继续之前先验证交接内容](practices/context/validate-handoff-before-continuation.md)
- [写下能保住决策的检查点](practices/context/write-decision-dense-checkpoint.md)
