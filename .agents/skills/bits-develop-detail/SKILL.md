---
name: "bits-develop-detail"
description: "Queries BITS develop task details via bytedcli and extracts repo and commit info. Invoke when users mention Bits dev task IDs/URLs or need code repository and commit details from Bits."
---

# Bits Develop Detail

使用 `bytedcli` 查询 Bits 研发任务详情，并从结果中提取代码仓库、commit、branch 信息。

## When To Use

- 用户给出 `bits.bytedance.net/devops/.../develop/detail/...` 链接
- 用户给出 Bits `dev-id`
- 用户说“看看这个 Bits 详情”
- 用户需要从 Bits 任务里反查代码仓库、commit、branch 信息，或 commit URL

## Hard Rules

- 只使用 `bytedcli --json bits develop get --dev-id <id>`
- 不直接抓 Bits 页面 HTML
- 不猜测 repo path 或 commit sha 或 branch name
- 如果命令报认证/权限错误，先向用户说明需要完成 `bytedcli` 登录或会话认证

## Inputs

接受两类输入：

1. Bits dev task ID

```text
2157396
```

2. Bits develop detail URL

```text
https://bits.bytedance.net/devops/4054120450/develop/detail/2157396/change/3268626
https://bits.bytedance.net/devops/4054120450/develop/detail/2157396/flow?devops_space_type=server_fe
```

## Step 1: Resolve `dev-id`

优先顺序：

1. 如果用户直接提供数字 ID，直接使用
2. 如果用户提供 Bits URL，从路径中的 `detail/<number>` 提取 `dev-id`

示例：

- `.../develop/detail/2157396/change/...` -> `2157396`
- `.../develop/detail/2157396/flow?...` -> `2157396`

如果无法提取 `dev-id`，明确告诉用户缺少可解析的 Bits develop detail ID。

## Step 2: Query bytedcli

运行：

```bash
bytedcli --json bits develop get --dev-id 2157396
```

## Step 3: Extract Repo, Commit, Branch Info

优先从 JSON 输出中的原始 `changes` 结构提取，路径形如：

```text
changes.data.changeList[*].change.manifest.codeElement
```

兼容读取：

- `branch`
- `branch.repo`
- `branch.commit_id`
- `branch.commit_title`

## Step 4: Build Output

对每条同时具备 `repoPath` 和 `commit sha` 的记录，拼装：

```text
https://code.byted.org/<repoPath>/commit/<sha>
```

## Output Format

默认用原结构回答

如果没有任何可用的 repo/commit/branch 信息，明确说明：
- 命令已执行，但当前返回中没有代码仓库或 commit 字段
- 不继续猜测或回退到页面解析

## Example

输入：

```text
https://bits.bytedance.net/devops/4054120450/develop/detail/2157396/change/3268626
```

处理流程：

1. 提取 `dev-id = 2157396`
2. 运行 `bytedcli --json bits develop get --dev-id 2157396`

## Notes

- 当前 `bytedcli` 的 Bits 详情命令已经会返回代码仓库与 commit、branch 信息
- 若用户后续想进一步查看 commit diff，可继续使用 mcp.codebase 查看
