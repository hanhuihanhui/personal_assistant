---
title: lark 机器人向已有群发消息
related:
  - ../index.md
  - ./README.md
---

# Lark 机器人向已有群发消息

适用场景：

- 用户身份缺少 `im:message.send_as_user`，暂时不想补授权
- 需要让当前 `lark-cli` 绑定的机器人直接在已有群里发消息
- 需要把一次性的群内同步动作沉淀成可重复执行的流程

## 核心思路

先用当前用户身份把机器人拉进目标群，再切换到 bot 身份发消息。

## 前置检查

1. 查看当前 `lark-cli` 绑定的应用信息，拿到 `appId`

```bash
lark-cli config show
```

2. 确认当前用户在目标群内，并且有拉成员权限
3. 确认应用具备以下 scope

- `im:chat.members:write_only`
- `im:message`

如果是用户本人直接发消息，则还需要 `im:message.send_as_user`；缺这个权限时，可以改走本文的 bot 路线。

## 标准流程

### 1. 获取当前 bot 的 App ID

执行：

```bash
lark-cli config show
```

示例输出中可看到：

```json
{
  "appId": "cli_a93c27a8b6f85bd3"
}
```

### 2. 先 dry run 检查拉群请求

```bash
lark-cli im chat.members create \
  --as user \
  --params '{"chat_id":"<chat_id>","member_id_type":"app_id","succeed_type":1}' \
  --data '{"id_list":["<app_id>"]}' \
  --dry-run
```

说明：

- `member_id_type` 传 `app_id`，因为邀请的是机器人应用
- `id_list` 里填当前应用的 `appId`
- `--as user` 表示由当前用户执行拉群动作

### 3. 正式把 bot 拉进群

```bash
lark-cli im chat.members create \
  --as user \
  --params '{"chat_id":"<chat_id>","member_id_type":"app_id","succeed_type":1}' \
  --data '{"id_list":["<app_id>"]}'
```

成功时通常返回：

```json
{
  "code": 0,
  "data": {
    "invalid_id_list": [],
    "not_existed_id_list": [],
    "pending_approval_id_list": []
  },
  "msg": "success"
}
```

### 4. 用 bot 身份发消息

```bash
lark-cli im +messages-send \
  --as bot \
  --chat-id <chat_id> \
  --text "这里填要发到群里的内容"
```

成功时会返回 `message_id`，可作为后续追踪依据。

## 常见判断

- 如果用户身份发消息时报 `missing required scope(s): im:message.send_as_user`：
  说明用户本人缺少发送权限，可以优先改走 bot 路线
- 如果拉群时报权限不足：
  优先检查应用是否开通 `im:chat.members:write_only`
- 如果拉群成功但 bot 发消息失败：
  优先检查应用是否开通 `im:message`，以及 bot 是否已在目标群内
- 如果群需要审批：
  返回里会出现 `pending_approval_id_list`

## 本次成功案例

- 目标群：`oc_956f612518eba86f58283c6ee42fb294`
- 当前 bot：`cli_a93c27a8b6f85bd3`
- 拉群命令：

```bash
lark-cli im chat.members create \
  --as user \
  --params '{"chat_id":"oc_956f612518eba86f58283c6ee42fb294","member_id_type":"app_id","succeed_type":1}' \
  --data '{"id_list":["cli_a93c27a8b6f85bd3"]}'
```

- 发送命令：

```bash
lark-cli im +messages-send \
  --as bot \
  --chat-id oc_956f612518eba86f58283c6ee42fb294 \
  --text "<问题总结正文>"
```

- 成功发送的消息 ID：`om_x100b52bcb57e7cbcb341975faaa5988`

## 复用建议

- 以后遇到“用户身份不能发，但又需要立刻在群里同步”的情况，优先复用这个流程
- 若后续经常需要群内同步，可考虑把“拉 bot 入群 + bot 发消息”再封装成固定操作清单
