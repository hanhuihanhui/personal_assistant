# Personal Assistant Repository

这是一个面向个人使用的助手仓库，用来沉淀长期 memory、阶段性 review、季度 OKR，以及提供给 Codex/Agent 的工具说明、知识上下文和工作规范。

## Repository Purpose

这个仓库的目标不是运行一个复杂应用，而是把“个人上下文系统”放进版本化目录中，让你每次通过 Codex 进入仓库时，都能：

- 读取当前长期 memory
- 接收新的碎片信息并归档
- 做一次 review
- 起草或更新季度 OKR
- 理解当前可用的工具、skill、知识入口和操作约定

## Start Here

人类使用时建议按以下顺序阅读：

1. `README.md`
2. `memory/index.md`
3. `reviews/index.md`
4. `okr/index.md`
5. `agent/prompting.md`

如果你是通过 Codex 在这个仓库里工作，优先看 `AGENTS.md` 和 `agent/session-start.md`。

## Structure

- `agent/`: 给 Codex/Agent 的工作手册、会话入口、工作流、工具/skill 说明
- `inbox/`: 暂存未归档信息
- `memory/`: 长期个人 memory，按主题域组织
- `reviews/`: review 模板与历史记录
- `okr/`: 季度 OKR 的起草、跟踪、复盘、归档
- `knowledge/`: 相对通用的知识沉淀
- `templates/`: 通用模板
- `schemas/`: frontmatter、命名和生命周期规范

## Recommended Usage

你可以直接向 Codex 发出类似问题：

- “请把这段信息整理成 memory 并放到合适位置”
- “请基于当前仓库内容做一次 review”
- “请根据最近的 review 和 memory 起草本季度 OKR”
- “请更新项目 X 的长期背景和下一步行动”

## Rules Of Thumb

- 新信息先进入 `inbox/`，再决定是否整理到长期层
- 长期、与你本人强绑定的信息优先进入 `memory/`
- 通用知识进入 `knowledge/`
- review 记录进度判断，OKR 记录目标闭环
- 文档标题可以中文，目录名和 YAML key 保持英文
