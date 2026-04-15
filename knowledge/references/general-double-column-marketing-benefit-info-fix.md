---
id: 2026-04-10-general-double-column-marketing-benefit-info-fix
title: 综搜双列埋点漏报修复（marketing_benefit_info）
type: reference
status: active
updated_at: 2026-04-10
source: conversation
tags: [search_loader, impression, double_column, marketing, incident]
related:
  - ../index.md
  - ../methods/lark-bot-send-to-existing-group.md
---

# 背景

问题背景：营销侧在做埋点建设时发现“综搜双列”的曝光埋点存在漏报，具体表现为最终产出的 impression 中缺少 `marketing_benefit_info` 字段。

影响：下游依赖该字段做的归因/分析无法覆盖综搜双列场景，导致统计偏差与漏记。

# 触发与线索

- 讨论群内定位到一个可回放样例 `trace_id=202604082027037CC6F557409503C67B61`。
- 在该样例对应的 impression 文件中确认缺少 `marketing_benefit_info`（群内同学提供了 impression 文件附件用于核对）。
- 同时，现有新链路中已存在写入 `marketing_benefit_info` 的代码路径，说明问题更可能出在“部分流量仍在走的旧链路”。

# 调查过程（关键路径）

目标：确定哪些链路会构建最终 impression，并确认 `marketing_benefit_info` 是在哪个环节丢失/未写入。

1. 确认新链路写入点
   - `material_selector.BuildMaterialsImpr(...)` 中对 `impressionLog.Extra["marketing_benefit_info"]` 进行了赋值：
     - `impressionLog.Extra["marketing_benefit_info"] = biz_admin.GetMarketingBenefitInfo(ctx, materialGroups)`

2. 确认综搜双列是否存在走旧链路的分支
   - 综搜双列素材选择入口：`DoubleColumnEcomGoodsSelectorPacker.SelectMaterials(...)`。
   - 在特定实验开关组合下，会走到旧实现：
     - `EnableGeneralDoubleColumnGoodsUiV3=true` 且 `EnableDoubleColumnMigrateMaterialSelector=false`
     - 路径：`DoubleColumnEcomGoodsOriginSelectorPacker -> general_material_selector.SelectMaterialsFromAdmin(...)`

3. 定位旧链路 impression 构建函数缺字段
   - 旧链路中，素材选择阶段的 impression 构建由：
     - `general_material_selector.BuildGoodsCardImpr(...)`
   - 该函数写入了 `show_price` / `show_sku_id` / sku 信息等，但未写入 `marketing_benefit_info`，因此最终 impression 缺 key。

# 根因结论

综搜双列在“未迁移到新素材选择链路”的分支中使用 `general_material_selector.BuildGoodsCardImpr(...)` 构建 `MaterialsImpr`，但该实现未补齐 `marketing_benefit_info` 的写入；而新链路 `material_selector.BuildMaterialsImpr(...)` 已写入，导致不同分支产物不一致，引发漏报。

# 修复方案（最终确认）

只需要在 `general_material_selector.BuildGoodsCardImpr(...)` 中补上 `marketing_benefit_info` 即可：

```go
impressionLog.Extra["marketing_benefit_info"] = biz_admin.GetMarketingBenefitInfo(ctx, materialGroups)
```

说明：

- `marketing_benefit_info` 会通过 `MaterialsImpr -> buildBaseImpression` 的深拷贝机制自动传递到最终 impression 中。
- 不需要改动：`BuildDoubleColumnEcomGoodsImpression`（最终确认无需额外同步/透传逻辑）。

# 代码交付

- 修复 MR：<https://code.byted.org/ecom/search_loader/merge_requests/4801>
- 计划：群内相关方完成 CR 后，跟随下周一版本上线。

# 验证建议

- 回放验证：使用样例 `trace_id=202604082027037CC6F557409503C67B61`，对比修复前后最终 impression，确认 `marketing_benefit_info` 出现且内容符合预期。
- 线上抽样：按“综搜双列”维度抽样观察 `marketing_benefit_info` 覆盖率，确认不再出现系统性缺失。
