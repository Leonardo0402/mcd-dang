# 核心 MCP 工具速查 (Core Tools Reference)

> 来源：麦当劳中国 MCP Server（`https://mcp.mcd.cn`）。精确参数键名以运行时工具 schema 为准（MCP 工具自描述，调用前通过 `list_tools` 获取）。

## MVP 核心工具

| 工具 | 描述 | 已知入参 / 备注 |
|---|---|---|
| `campaign-calendar` | 查询当月营销活动日历，按日期分段返回 Markdown（含 今日/往期回顾/未来 状态标签），无结构化类型字段；活动以 活动标题/内容介绍/图片 呈现 | 入参：实测无必填入参亦可返回当月；**已实测**：当月含"麦当劳 X PEACEMINUSONE""麦当劳联动 G-DRAGON"等联名活动 |
| `query-my-account` | 查询积分账户（币种"麦享会积分"）：availablePoint 可用 / accumulativePoint 累计 / currentMouthExpirePoint **本月将过期** / expiredPoint 已过期 / frozenPoint 冻结 / nextMouthExpirePoint 下月将过期 | 无入参；返回带字段说明的 Markdown + 末尾 Original Response JSON |
| `now-time-info` | 返回当前的完整时间信息，便于 LLM 知道当前时间和日期 | 无入参 |

## MVP 可选工具

| 工具 | 描述 |
|---|---|
| `query-lottery-info` | 查询当前积分抽奖活动的基本信息，包括活动状态、奖品列表、抽奖消耗规则和用户可用资源 |
| `mall-points-products` | 查询麦麦商城内可使用积分兑换或现金购买的商品（不包括第三方兑换码） |
| `mall-product-detail` | 查询商城商品详细信息（图片、积分、有效期、说明、详情等） |

## 后续扩展（非 MVP）可能涉及

| 工具 | 描述 |
|---|---|
| `query-meals` / `query-meal-detail` | 餐品列表 / 详情（v1.0.6 起支持更换套餐内商品组合、部分餐品特制）——用于自定义汉堡模块 |
| `query-nearby-stores` | 查询附近可用门店——用于就近快闪模块 |
| `create-order` | 创建订单——用于下单 |

> 实测（2026-10-09）服务端共暴露 **35 个工具**，除上表与 MVP 工具外，还包括官方 README 未列出的 `query-promotions`、`query-survey-coupon`。

## 调用示例（伪代码）

```text
now     = call("now-time-info")
cal     = call("campaign-calendar")          # 结构待验证
collab  = filter_by_keywords(cal, ["联名","限定","IP","快闪","×","collaboration"])
acct    = call("query-my-account")
# 输出：联名情报卡片 + 积分策略建议
```
