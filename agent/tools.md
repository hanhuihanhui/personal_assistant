# Tools

这个文件记录仓库中“给 Agent 使用”的工具上下文，不等同于运行时代码工具。

## Current Tooling Model

- 当前仓库以文档工作流为主
- 默认工具是 Codex 对仓库文件的读取、整理、更新能力
- 第一阶段不依赖数据库、服务、向量索引或外部调度器

## Expected Tool Uses

- 读取现有 memory / review / OKR 文档
- 基于模板生成新文档
- 将对话信息结构化为 frontmatter + 正文分区
- 在不同域之间建立引用关系

## Future Extension

第二阶段可以考虑增加 `scripts/`：

- 初始化季度 OKR 目录
- 生成索引
- 汇总最近 review 条目
