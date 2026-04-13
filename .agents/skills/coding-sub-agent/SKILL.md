---
name: coding-sub-agent
description: Coco 是一款用于 AI 辅助编程任务的 Codebase Copilot CLI 工具。类似 Codex、Claude Code 工具。

  在以下场景中使用此技能：
  - 通过命令行界面执行代码代理任务

  该 CLI 支持非交互式模式，适用于切换到不同的workspace执行coding工作。
---

# Coco 技能

Coco是一款通过自然语言交互实现 AI 辅助编程的命令行界面工具。

## 概述

- **二进制名称**: `coco`
- **用途**: 用于开发任务的 AI 驱动的代码代理

## 核心命令

### 主入口

```bash

# 执行单个提示并退出
coco -p "你的提示内容"

# 执行斜杠命令
coco -p "/builtin:review <mr_url>"
```

### 可用子命令

| 命令 | 描述 |
|---------|-------------|
| `coco acp` | Agent Client Protocol 命令 |

### 全局标志

#### 执行控制

| 标志 | 简写 | 描述 | 示例 |
|------|------|-------------|---------|
| `--print` | `-p` | 执行提示并退出（非交互式） | `coco -p "修复 bug"` |

#### 会话管理

| 标志 | 描述 | 示例 |
|------|-------------|---------|
| `--session-id <id>` | 使用指定的会话 ID | `coco --session-id abc123` |


## 内置斜杠命令

在交互模式下，使用 `/` 前缀执行特殊命令：

| 命令 | 描述 |
|---------|-------------|
| `/model` | 在配置的模型之间切换 |

### 常用配置选项

```yaml
# 模型选择
model:
  name: gpt-5.2  # 或 gpt-5.4 等

```

## 使用模式

```bash
# 非交互式执行
coco -p "用最新变更更新 README"

coco -p "任务描述"
```

### GitHub Actions 示例

```yaml
jobs:
  ai-review:
    steps:
      - uses: actions/setup-coco
      - run: coco -p "review ${{ github.event.pull_request.html_url }}"
```

### 模型配置
```bash
# 通过命令行指定模型
coco -c model.name=gpt-5.2

```

### 会话管理
```bash
# 使用特定会话 ID 启动
coco --session-id <custom-id>
```

### 超时配置
```bash
# 增加 bash 命令超时
coco --bash-tool-timeout 10m

# 增加查询超时
coco --query-timeout 30m
```

## 最佳实践

1. **长任务使用会话 ID** - 恢复能力有助于处理中断的工作流
