# contrib/ — Practice 投稿暂存区

`contrib/` 收纳来自团队私有 Pack 仓库、经实际使用验证的 Practice 投稿。它只服务于评审：`.lorelum/registry.yaml` 按 `packs[].releases[].path` 显式白名单安装，`contrib/` 不在其列，任何 CLI 命令都不会安装或读取这里的内容。投稿通过评审后的晋升（进入 `packs/`）由单独的流程定义，不在本指南范围内。

一份 dossier 只承载一条 Practice；一个 Pack 的多条 Practice 请分别投稿。

## 投稿流程

1. 复制 `dossier-template.yaml` 为 `contrib/submissions/<dossier-id>.yaml`。`<dossier-id>` 用 kebab-case，建议沿用 Practice 的 slug。
2. 按[字段要求](#dossier-字段要求)逐项填写，替换全部 `<...>` 占位符。
3. 对照[评审门禁](#评审门禁)自查，然后以该文件为主提交 Pull Request。
4. 维护者按门禁逐项评审，结论写入 dossier 的 `review` 块（见[评审结果与存证](#评审结果与存证)）。

## Dossier 字段要求

### 来源（`practice`）

- `practice.id` 必须能在 `source_repository` 的 `source_ref` 处逐字找到；
- `source_ref` 必须 pin 到 commit SHA 或 tag。分支名、`HEAD` 等可变引用不被接受——评审与后续复核都要能还原投稿时的原文。

### 原始事件（`origin_event`）

沉淀这条经验时的真实上下文，三要素缺一不可：

- `goal`：当时想达成什么；
- `decision`：当时做了什么决策或判断；
- `outcome`：实际结果，包括代价与后续影响。

写"我们当时……"，不写"用户应该……"。

### 使用事例（`usage_examples`）

至少 2 条真实场景，每条包含：

- `situation`：**描述当时所处的处境**——面对什么任务、约束、信号，而不是复述 Practice 的措辞。此字段后续将作为检索查询的素材：只有处境描述才能检验"这条 Practice 是否会在被需要时被想起"；
- `decision_changed`：该 Practice 具体改变了哪个决策、怎么改的。

没有真实改变过决策的场景不算使用事例。

### 脱敏声明（`sanitization`）

投稿人逐项确认 `secrets_removed`、`internal_hostnames_removed`、`customer_identifiers_removed`。`contrib/` 不进入运行时，允许保留内部术语（边界见[安全边界](#安全边界)），并在 `notes` 中列出保留的术语及脱敏时改动过的内容。

## 评审门禁

维护者按以下 checklist 逐项评审，任何一项不通过即拒绝：

1. **格式完整**：YAML 可解析，模板字段齐全，占位符全部替换，`id` 在 `contrib/` 内唯一。
2. **可溯源**：`practice.id` 能在 `source_repository` 的 `source_ref` 处找到且内容一致。
3. **原始事件具体**：目标、决策、结果三要素齐备且可判断，不是原则陈述。
4. **使用事例合格**：至少 2 条，每条描述真实处境与被改变的决策，未复述 Practice 措辞。
5. **脱敏属实**：逐项核对声明，并抽查正文无密钥、内部主机名、可识别客户信息。
6. **无重复**：与 `packs/` 现有 Practice 及 `contrib/` 在审条目无实质重复；与相邻 Practice 决策相近时，需在 dossier 中说明差异。
7. **有用性门槛**（"很有用才收"）：在多个场景被真实使用、实际改变过决策，且可判断泛化到来源团队之外仍然成立。单一事件沉淀的经验不足以收录。
8. **许可**：投稿内容以 CC-BY-4.0 入库，与主仓库 `packs/` 一致，投稿即视为同意。

## 安全边界

- **禁止出现**：密钥与凭据、内部主机名或 IP、可识别客户的信息（客户名称、指向特定客户的项目代号等）。
- **允许保留**：内部术语（系统代号、团队词表）。`contrib/` 不进入运行时，术语暴露面有限，可检索性优先；保留的术语应在 `sanitization.notes` 中列出。
- 投稿正文含上述禁止内容而声明已脱敏的，直接拒绝并记录。

## 评审结果与存证

- **拒绝**：dossier 移入 `contrib/rejected/`，文件保留不删除。维护者在 `review` 块写入 `status: rejected`、`reviewer`、`date`，并在 `rejection_reason` 中引用未通过的门禁项。被拒条目是存证：补足材料后可以重新投稿（新文件、新 `id`，并在 `sanitization.notes` 中说明与被拒条目的关系）。不接受删除 `contrib/rejected/` 中记录的请求。
- **接受**：`review.status` 置为 `accepted-for-promotion`，条目留在原位等待晋升流程。晋升到 `packs/` 的集成规范另行定义，评审通过本身不改动 `.lorelum/registry.yaml`。

## 非目标

- 不修改 CLI；
- 不定义查询集与晋升的集成规范（另行 issue）；
- 不新增 pack-creator 流程类 Practice。

## 目录结构

```text
contrib/
  README.md              本指南
  dossier-template.yaml  投稿模板
  examples/              填写完整的示例 dossier
  submissions/           在审投稿
  rejected/              被拒条目存证（保留不删除）
```

`submissions/` 与 `rejected/` 由第一份投稿和第一次拒绝分别创建。
