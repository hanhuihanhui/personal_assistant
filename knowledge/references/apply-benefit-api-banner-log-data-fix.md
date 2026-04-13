---
id: 2026-04-10-apply-benefit-api-banner-log-data-fix
title: ApplyBenefitApi banner_log_data 字段空值修复（bcm_em_id / biz_code）
type: reference
status: active
updated_at: 2026-04-10
source: conversation
tags: [benefit_api, banner, apply, marketing, logdata, incident]
related:
  - ./general-double-column-marketing-benefit-info-fix.md
---

# 背景

2026-04-09 ~ 2026-04-10 期间，营销侧在提测与回归中发现 ApplyBenefitApi 返回体中的 `banner_log_data` 关键字段缺失，导致端上与埋点侧将其视为未上报，影响链路验收与归因。

# 问题

- logid: `2026040922293770B2B1BBD8575E34ADB0`
- `ApiApplyBenefitHandler` 返回中，`apply_res.dynamic_patch.marketing_banner.banner_log_data` 的 `bcm_em_id` 与 `biz_code` 为空字符串 `""`
- 端上与埋点侧对空字符串按缺失处理，造成“已领券但字段缺失”的误判

# 根因

- 在 `biz/service/benefit_api/goods_search/coupon_apply.go` 中，ApplyCoupon 成功后组装 banner 的调用链向
  `NewAggrBannerSubPacker`、`NewSingleCouponUIService`、`NewCouponPackageUIService`
  传入了 `nil` 的 `types.IResourceProcessParams`
- 下游如 `BuildBannerLogData` 在 `params == nil` 时不会补齐字段，`bcmEmID` 与 `bizCode` 均保持为空字符串

# 修复方案（最终确认）

1. 新增轻量级 `IResourceProcessParams` 实现：`apply_benefit_params.go`
   - 仅实现打包链路实际用到的方法：`BcmEmID()`、`BizCode()`、`PageType()`（其余接口返回默认值或不实现）
2. 在 `ApplyCoupon` 成功后、组装 banner 前构造 `applyBenefitParams`
   - `bcmEmID`：优先使用请求透传值；兜底 `recorder.GenBcmEmID(uid)`
   - `bizCode`：使用 `constdef.GetBizCodeByResourceBit(ctx, resourceBit, pageType)` 计算
   - `pageType`：沿用当前上下文判定
3. 将此前传入的 3 处 `nil` 参数替换为该 `params`
   - 聚合 banner、单券 banner、券包 banner 三条路径同时覆盖，确保最终 `banner_log_data` 不再为空

示意（伪代码）：

```go
params := NewApplyBenefitParams(
    WithBcmEmID(req.BcmEmID, recorder.GenBcmEmID(uid)),
    WithBizCode(constdef.GetBizCodeByResourceBit(ctx, resourceBit, pageType)),
    WithPageType(pageType),
)

aggr := NewAggrBannerSubPacker(ctx, params, ...)
single := NewSingleCouponUIService(ctx, params, ...)
pkg := NewCouponPackageUIService(ctx, params, ...)
```

# 涉及文件

- 新增：`biz/service/benefit_api/goods_search/apply_benefit_params.go`
- 修改：`biz/service/benefit_api/goods_search/coupon_apply.go`（L69, L86, L95, L118）

# 验证建议

- 接口回归：
  - 领券成功后抓包校验 `apply_res.dynamic_patch.marketing_banner.banner_log_data`
  - `bcm_em_id` 与 `biz_code` 非空（不为 `""`），且符合上下游口径
- 埋点平台校验：
  - 对应曝光/点击事件中字段可见且解析正常
- 回归样例：
  - 复现与比对上述 `logid` 场景，确认修复生效

# 里程碑与发布

- 修复 MR（示例）：<https://code.byted.org/ecom/search_marketing_express/merge_requests/new?merge_request%5Bsource_branch%5D=feat%2Fsearch_saas&source_branch=feat%2Fsearch_saas>
- 节奏：QA 回归通过后，随下周一版本发车

# 经验教训

- banner 打包链路实际依赖的 `IResourceProcessParams` 方法很少（`BcmEmID`、`BizCode`、`PageType`），直接复用重量级实现（绑定完整 RPC 请求）风险更高
- 传递 `nil` 接口虽不致 panic，但会导致字段静默为空值，建议统一以轻量实现物化必要上下文，避免默认值“吃掉信号”

