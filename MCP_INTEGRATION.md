# MCP 接入说明 (MCP Integration)

## 1. 使用的 MCP Server

- **名称**：麦当劳中国官方 MCP Server（`mcd-mcp`）
- **接入地址**：`https://mcp.mcd.cn`
- **传输协议**：Streamable HTTP
- **鉴权**：请求头 `Authorization: Bearer <MCP_TOKEN>`（Token 由用户在 <https://open.mcd.cn/mcp> 申请后提供，仅存于 WorkBuddy 连接器配置，不进入代码 / 仓库）
- **协议版本**：MCP 2025-06-18 及之前
- **频率限制**：每 Token 600 次 / 分钟（超限返回 429）

## 2. 实际使用的 Tools（MVP）

| 工具 | 作用 | 在 MVP 中的角色 |
|---|---|---|
| `campaign-calendar` | 查询当月营销活动日历（进行中 / 往期 / 未来） | 模块 1 数据源：拉活动 → 筛联名 / IP / 快闪 |
| `query-my-account` | 查询积分账户（可用 / 累计 / 冻结 / 即将过期） | 模块 2 数据源：积分策略 |
| `now-time-info` | 获取官方当前时间 | 时间基准 / 倒计时 / 状态判断 |
| `query-lottery-info`（可选） | 查看积分抽奖活动信息 | 关联联名抽奖提示 |
| `mall-points-products` / `mall-product-detail`（可选） | 积分兑换商品列表 / 详情 | 积分兑换建议指向 |

## 3. 调用流程（业务价值）

```text
用户意图
  → now-time-info（时间基准）
  → campaign-calendar（拉当月活动）
  → [筛联名/IP/快闪：关键词 + 语义判断，标注依据]
  → [可选] query-lottery-info（联名抽奖提示）
  → query-my-account（积分策略）
  → [可选] mall-points-products / mall-product-detail（兑换指向）
  → 输出结构化简报
```

**业务价值**：聚焦被现有参赛作品忽略的"联名 / 快闪"垂直场景，把分散的活动日历与积分账户收敛成一张可执行简报，解决"错过限时联名"与"积分过期浪费"的真实痛点。

## 4. 实测验证结论（2026-10-09，使用真实 Token 联调）

- `campaign-calendar` 返回**按日期分段的 Markdown 文本**（含"今日 / 往期回顾 / 未来"状态标签），**无结构化"类型"字段**；活动以 `**活动标题**` / `**活动内容介绍**` / `**活动图片介绍**` 呈现。因此 MVP 的"联名识别"采用**关键词 + 语义判断**是正确的实现路径（而非基于字段）。
- 实测确认当月存在**联名 / IP 联动**活动（如"麦当劳 X PEACEMINUSONE"、"麦当劳联动 G-DRAGON"），验证了本作聚焦"联名 / 快闪"垂直场景的可行性。
- `query-my-account` 返回带字段说明的 Markdown + 末尾 `Original Response` JSON，`data` 含 `availablePoint`(可用) / `accumulativePoint`(累计) / `currentMouthExpirePoint`(**本月将过期**) / `expiredPoint`(已过期) 等，币种为"麦享会积分"。
- 积分策略模块**不能**直接增加积分（MCP 无加积分接口），仅做查询与建议——此边界不变。

## 5. 安全与合规

- 不存储 / 硬编码 Token，仅通过 WorkBuddy 连接器配置注入请求头。
- 仅调用官方工具，不访问第三方或违规站点。
- 输出价格 / 活动 / 积分以官方实时数据为准，不臆造。
- 符合《参赛声明》中关于原创性、合规性、信息安全与规则遵守的要求。
