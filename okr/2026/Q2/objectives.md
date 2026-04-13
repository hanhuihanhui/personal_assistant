---
id: okr-2026-q2-objectives
title: 2026 Q2 Objectives
type: okr
status: active
updated_at: 2026-04-11
source: lark-im
tags: [okr, q2, tracking_standardization]
related:
  - knowledge/references/apply-benefit-api-banner-log-data-fix.md
  - knowledge/references/general-double-column-marketing-benefit-info-fix.md
  - knowledge/methods/lark-bot-send-to-existing-group.md
---

# Objective 1

## Objective

完成“端到端埋点标准化”Q1 遗留问题收口，让重点链路具备统一字段定义、稳定下发与可复核验收能力。

## Why It Matters

Q1 已完成立项、方案沟通、主点位接入和多轮联调，但从 3 月末到 4 月初的消息看，核心链路仍存在字段缺失、端差和归因链路不通的问题。Q2 的关键不是继续“加点”，而是把标准真正变成可落地、可验证、可复用的统一能力。

## Q1 Progress Summary

- 1 月上旬完成项目立项与 KO 草拟，围绕客户端工作量、组件复用方式和标准化实现路径完成第一轮摸底。
- 1 月中旬进入测试推进阶段，综搜和商垂搜主点位大面可用，但新增组件拖慢整体节奏，项目从“方案确认”转入“边测边补”。
- 2 月补齐了“大前端 + 服务端”全貌对齐，持续围绕字段标准、触点口径和实现边界开展沟通，并开始通过 MR 推进部分标准化能力落地。
- 3 月项目重点转向 trace、归因和实际业务链路验证，暴露出 `btm` 维护分散、`source_id` 关联不顺、`apply_type` 缺失、部分链路日志不足等问题。
- 到 4 月初，问题进一步收敛到具体遗留项：双列曝光链路缺少 `marketing_benefit_info`、apply 返回中的 `banner_log_data` 缺少 `biz_code` / `bcm_em_id`，说明 Q1 已完成“把问题框起来”，但还未完成“把口径真正打穿”。

## Key Results

- KR1：重点链路（综搜、商垂搜、双列、图搜、豆包/领券）统一采用标准字段口径，至少覆盖 `biz_code`、`bcm_em_id`、`btm`、`btm_show_id`、`apply_type`、`source_id` 6 个关键字段，并形成统一验收清单。
- KR2：Q1 遗留的高优先级问题全部完成修复或明确 owner + ETA，至少包括 apply `banner_log_data` 空值、双列 `marketing_benefit_info` 漏报、`apply_type` 缺失依赖、关键日志补齐 4 类问题。
- KR3：建立 1 套从“接口返回 / 服务端日志 / 埋点平台 / 分析查询”串联的端到端验证路径，重点 case 可在 30 分钟内完成复核定位。
- KR4：将目前依赖人工维护的 `btm` / 字段映射关系收敛为可查询、可复用的方法，减少 case-by-case 排查。

## Projects Or Actions

- 按链路梳理遗留问题：领券 apply、双列曝光、trace/归因、价格一致性相关埋点。
- 对关键字段建立统一的“定义 → 生成 → 下发 → 上报 → 加工”核对表。
- 结合真实 case 沉淀修复经验：`knowledge/references/apply-benefit-api-banner-log-data-fix.md`、`knowledge/references/general-double-column-marketing-benefit-info-fix.md`。
- 优先减少跨团队口径反复确认，把 owner、字段责任和验收入口前置。

## Risks And Dependencies

- 多端、多组件、多实验并行，容易出现“主流程没问题，但分支链路漏字段”的情况。
- 一部分字段关系仍依赖人工经验或临时 SQL，若没有补成稳定查询链路，Q2 仍会反复掉进 case 排查。
- 需要客户端、服务端、数据加工与业务分析共同对齐，单侧修复可能无法真正闭环。

## Related Memory

- `knowledge/references/apply-benefit-api-banner-log-data-fix.md`
- `knowledge/references/general-double-column-marketing-benefit-info-fix.md`

## Related Reviews

- 私聊与群聊消息检索时间范围：`2026-01-01` 至 `2026-04-11`
- 范围包含：本人发送过消息的群聊，以及本人发送过消息的私聊
