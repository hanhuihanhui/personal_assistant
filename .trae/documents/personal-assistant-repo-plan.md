# 个人助手仓库构建方案

## Summary

目标是在一个**本地单用户**仓库中，先搭建一套以 **Markdown + YAML** 为核心的“个人助手知识系统规范”，供你后续通过 Codex 定时进入仓库提问、读取上下文、更新 memory、执行 review、起草和维护季度 OKR。第一阶段不以复杂服务程序为主，而是优先建立**稳定目录结构、统一文档模型、Agent 入口文档、固定模板、使用流程**，让 Codex 可以基于仓库内容持续工作。

已确认的关键决策：

- 第一阶段形态：知识库规范优先，不先做重程序化服务
- 存储方案：`Markdown + YAML frontmatter`
- 首批能力：`memory`、`review`、`OKR`、`工具/知识库/skill 信息` 全覆盖
- 运行环境：本地单用户
- Codex 工作方式：以**读写仓库文件**为主
- review 节奏：第一版按需手动，不强制小时/天级固定频率
- 知识边界：同时包含 `Agent 操作手册` 与 `个人知识库`
- OKR 范围：完整闭环（起草、跟踪、回顾、归档）
- memory 组织方式：长期信息按**主题域**组织
- 流程约束：中等规范，有推荐路径但保留灵活性
- 语言偏好：中英混合，说明内容以中文为主，目录名/字段名可英文

## Current State Analysis

基于当前仓库探索结果：

- 仓库根目录当前为空
- 没有检测到现有代码、配置文件、README、脚本或数据目录
- 没有检测到现有 `.trae` 文档或项目约定

这意味着当前最合理的建设方式不是“接入已有系统”，而是直接从零定义一套适合 Codex 使用的仓库操作模型。

## Proposed Changes

### 1. 定义仓库顶层结构

建议创建以下顶层目录与文件：

- `README.md`
  - 作为人类入口
  - 说明这个仓库是什么、如何使用、目录结构、推荐提问方式
- `AGENTS.md`
  - 作为 Codex / Agent 的首要操作手册
  - 说明进入仓库后优先读取哪些文件、如何更新 memory、如何写 review、如何维护 OKR
- `inbox/`
  - 暂存未归档信息、碎片记录、待整理问题
- `memory/`
  - 长期个人 memory 主体，按主题域组织
- `reviews/`
  - review 模板、索引、历史记录
- `okr/`
  - OKR 模板、季度档案、跟踪与复盘
- `agent/`
  - 提供给 Agent 的工具说明、skill 说明、操作规范、提示词入口
- `knowledge/`
  - 个人知识库、方法论、术语、长期经验沉淀
- `templates/`
  - 各类标准模板集中管理，避免模板散落
- `schemas/`
  - 记录 YAML frontmatter 规范、命名规则、状态字段约定

设计原因：

- 顶层目录尽量用英文，便于稳定引用和后续扩展
- 内容正文可以中文，符合你的使用习惯
- 把“长期 memory”“周期性 review”“目标体系 OKR”“Agent 上下文”拆开，避免混在一起失控

### 2. 设计 `memory/` 为主题域长期记忆层

建议结构：

- `memory/index.md`
  - memory 总索引，说明有哪些主题域、哪些文件是核心上下文
- `memory/areas/`
  - 按主题域组织长期信息
- `memory/areas/projects/`
  - 项目相关长期记忆，如项目背景、关键决策、风险、待跟进事项
- `memory/areas/goals/`
  - 长期目标、方向、年度重点
- `memory/areas/workstyle/`
  - 你的偏好、习惯、工作方式、判断标准
- `memory/areas/relationships/`
  - 与人协作时的上下文、注意事项
- `memory/areas/knowledge/`
  - 与个人长期知识体系强绑定、但又比 `knowledge/` 更像“你的工作记忆”的内容

单篇 memory 文档建议使用统一 frontmatter，例如：

```yaml
---
id: mem-project-001
title: 项目 X 背景
type: memory
domain: projects
status: active
tags: [project, context]
source: manual
updated_at: 2026-04-09
review_after: 2026-05-01
related:
  - okr/2026/Q2/objectives.md
  - reviews/2026/2026-04.md
---
```

正文建议固定分区：

- `Summary`
- `Current Facts`
- `Open Questions`
- `Recent Changes`
- `Next Actions`

设计原因：

- frontmatter 让 Codex 更容易筛选、引用、汇总
- 正文分区减少自由发挥导致的信息结构漂移
- `related` 字段用于把 memory 和 review/OKR 串起来

### 3. 设计 `reviews/` 为周期复盘与进度回顾层

因为第一版是“按需手动”，建议支持三种 review 类型，但不强制全部固定执行：

- `reviews/index.md`
  - review 总入口，说明有哪些 review 类型与使用方式
- `reviews/templates/review-session.md`
  - 通用 review 模板
- `reviews/templates/daily-review.md`
  - 日回顾模板
- `reviews/templates/hourly-checkin.md`
  - 小时级进度检查模板
- `reviews/templates/weekly-summary.md`
  - 周汇总模板，便于后续扩展
- `reviews/logs/2026/`
  - 存放实际 review 记录

建议 review 记录命名：

- 小时级：`reviews/logs/2026/2026-04-09-14-review.md`
- 日级：`reviews/logs/2026/2026-04-09-daily.md`
- 通用会话：`reviews/logs/2026/2026-04-09-session.md`

每次 review 文档建议包含：

- 本次触发原因
- 当前在推进什么
- 自上次以来的进展
- 阻塞点
- 下一步行动
- 是否需要更新 memory
- 是否影响当前 OKR

设计原因：

- 让 review 成为 memory 与 OKR 的中间层，而不是孤立日志
- 后续如果要自动统计，也有稳定结构可读

### 4. 设计 `okr/` 为完整闭环目标系统

建议结构：

- `okr/index.md`
  - OKR 总入口，说明年度与季度的关系
- `okr/templates/okr-draft.md`
  - 季度 OKR 起草模板
- `okr/templates/okr-checkin.md`
  - 周期性跟进模板
- `okr/templates/okr-review.md`
  - 季末复盘模板
- `okr/2026/Q2/overview.md`
  - 本季度概览
- `okr/2026/Q2/objectives.md`
  - 目标与关键结果正文
- `okr/2026/Q2/checkins/`
  - 季内跟进记录
- `okr/2026/Q2/review.md`
  - 季末复盘与评分
- `okr/archive/`
  - 归档历史季度

建议 `objectives.md` 中每个 Objective 固定包含：

- Objective 描述
- 为什么重要
- Key Results
- 对应项目/行动
- 风险与依赖
- 关联 memory
- 关联 review

设计原因：

- 你已明确需要“完整闭环”，因此不能只做 OKR 草稿
- 季内 check-in 和季末 review 必须有独立承载位置

### 5. 设计 `agent/` 为给 Codex/Agent 的运行上下文层

建议结构：

- `agent/index.md`
  - Agent 总入口，说明所有建议读取路径
- `agent/tools.md`
  - 记录当前可用工具、预期用途、调用边界、风险事项
- `agent/skills.md`
  - 记录 skill 清单、用途、输入输出约定、适用场景
- `agent/workflows.md`
  - 记录典型流程，例如“做一次 review”“更新 memory”“起草季度 OKR”
- `agent/prompting.md`
  - 记录推荐向 Codex 提问的方式与提示词模板
- `agent/session-start.md`
  - 作为每次进入仓库时的首选读取文档，帮助 Agent 快速进入状态

关键原则：

- `agent/` 记录的是“如何工作”
- `knowledge/` 记录的是“知道什么”
- `memory/` 记录的是“关于你和你事情的长期上下文”

这样可以避免三者边界混乱。

### 6. 设计 `knowledge/` 为个人知识库层

建议结构：

- `knowledge/index.md`
  - 知识库总索引
- `knowledge/methods/`
  - 方法论、框架、实践手册
- `knowledge/concepts/`
  - 关键概念定义
- `knowledge/references/`
  - 外部信息摘要、链接、书籍/文章摘录
- `knowledge/glossary.md`
  - 常用术语表

使用边界：

- 与你个人长期工作方式强绑定的，优先放 `memory/`
- 相对通用、可复用的知识，放 `knowledge/`

### 7. 设计 `templates/` 与 `schemas/` 作为规范层

建议内容：

- `templates/memory-entry.md`
- `templates/review-session.md`
- `templates/okr-objective.md`
- `templates/inbox-capture.md`
- `schemas/frontmatter.md`
- `schemas/naming.md`
- `schemas/lifecycle.md`

其中 `schemas/frontmatter.md` 需要明确：

- 必填字段
- 可选字段
- 各类型文档允许的 `type`
- 状态流转规则，例如 `draft -> active -> archived`

其中 `schemas/naming.md` 需要明确：

- 目录命名统一英文
- 文件名优先英文或日期前缀
- 文档标题可中文
- YAML key 统一英文

这样可以让中英混合策略保持稳定。

### 8. 设计 `README.md` 与 `AGENTS.md` 的职责分工

建议：

- `README.md` 面向你本人，说明仓库定位、结构、常见操作、推荐问法
- `AGENTS.md` 面向 Codex/Agent，说明进入仓库后该按什么顺序读取与更新

`AGENTS.md` 中建议直接写明以下固定顺序：

1. 优先读取 `agent/session-start.md`
2. 再看 `memory/index.md`、`reviews/index.md`、`okr/index.md`
3. 根据用户问题进入对应域
4. 如有新信息，先写入 `inbox/` 或对应模板
5. 如有结构化更新，补充 `related` 链接
6. 若变更影响目标，联动更新 OKR 或 review

### 9. 设计使用流程，让 Codex 可稳定进入仓库工作

建议定义 4 条标准工作流，写入 `agent/workflows.md`：

- `Capture Workflow`
  - 用于接收零散信息，先写入 `inbox/`
- `Memory Update Workflow`
  - 把 inbox 或对话内容整理进 `memory/`
- `Review Workflow`
  - 按模板做一次进度 review，并判断是否更新 memory / OKR
- `OKR Workflow`
  - 起草季度 OKR、更新 check-in、做季度复盘

每个 workflow 都应说明：

- 输入是什么
- 要读哪些文件
- 输出写到哪里
- 什么时候需要交叉更新其他目录

### 10. 第二阶段再考虑轻量脚本，而不是一开始就上服务

当前需求更适合“仓库规范先行”。因此建议把程序化能力放到第二阶段：

- 可选新增 `scripts/` 目录
- 用于做索引生成、状态汇总、review 模板初始化、季度目录初始化
- 若后续确实需要，再考虑 `Python` 实现轻量脚本

不建议第一阶段就做：

- Web 服务
- 数据库
- 向量检索
- 复杂 Agent 框架

原因：

- 当前使用方式是“你定时通过 Codex 到仓库提问”
- 第一阶段核心问题是**让上下文可沉淀、可读、可更新**
- 过早引入运行时系统会增加维护负担，但并不会显著提升第一阶段价值

## Assumptions & Decisions

以下假设已在本方案中锁定，执行时无需再额外决定：

- 仓库第一阶段以文档系统为主，而非应用程序
- 所有关键资料都在仓库内可直接被 Codex 读取
- Markdown 正文为主，YAML frontmatter 提供结构化元数据
- 目录命名英文，正文中文优先，字段英文
- review 允许按需触发，不强绑定固定周期
- memory 与 knowledge 分层，避免概念重叠
- OKR 必须覆盖起草、跟踪、复盘、归档
- Agent 工作规范单独放在 `agent/` 与 `AGENTS.md`

## Recommended Initial File Set

如果进入执行阶段，建议第一批优先落地以下文件，作为最小可用版本：

- `README.md`
- `AGENTS.md`
- `agent/index.md`
- `agent/session-start.md`
- `agent/workflows.md`
- `memory/index.md`
- `memory/areas/projects/README.md`
- `memory/areas/goals/README.md`
- `reviews/index.md`
- `reviews/templates/review-session.md`
- `reviews/templates/daily-review.md`
- `reviews/templates/hourly-checkin.md`
- `okr/index.md`
- `okr/templates/okr-draft.md`
- `okr/templates/okr-checkin.md`
- `okr/templates/okr-review.md`
- `knowledge/index.md`
- `templates/memory-entry.md`
- `schemas/frontmatter.md`
- `schemas/naming.md`

这些文件足够支持你开始通过 Codex：

- 记录新的个人 memory
- 做一次 review
- 起草一个季度 OKR
- 告诉 Agent 它应当如何读取和更新仓库

## Verification Steps

执行后应验证以下几点：

1. 你第一次进入仓库时，只看 `README.md` 就能理解整体结构与使用方式
2. Codex 第一次进入仓库时，只看 `AGENTS.md` 和 `agent/session-start.md` 就能知道优先读取哪些文件
3. 新增一条碎片信息时，能够明确先写到 `inbox/`，再整理到 `memory/`
4. 发起一次“请帮我做 review”时，Codex 能定位到 `reviews/templates/` 并生成规范记录
5. 发起一次“请帮我写本季度 OKR”时，Codex 能定位到 `okr/templates/` 并生成完整闭环文件
6. 任意一篇 memory 文档都能通过 frontmatter 与 review / OKR 建立关联
7. 目录职责清晰，没有出现同类信息同时分散在 `memory/`、`knowledge/`、`agent/` 的情况

## Execution Order

执行阶段建议严格按以下顺序落地：

1. 创建顶层目录与最小入口文件
2. 写 `README.md` 和 `AGENTS.md`
3. 写 `agent/` 下的会话入口与 workflow 规范
4. 写 `schemas/` 与 `templates/`
5. 写 `memory/`、`reviews/`、`okr/`、`knowledge/` 各自索引
6. 补齐首批模板
7. 用一个样例 memory、一个样例 review、一个样例季度 OKR 自测结构是否顺手

## Out of Scope For Phase 1

以下内容明确不放入第一阶段：

- 自动定时执行器
- 后端服务或前端页面
- 数据库存储
- 向量检索/RAG
- 多用户权限系统
- 复杂插件机制

等第一阶段文档体系跑顺后，再决定是否进入第二阶段脚本化或服务化。
