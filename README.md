# 麦噹 (Maidang)

![麦噹 banner](assets/banner.svg)

> 帮你不错过任何一次麦当劳联名 / IP 联动 / 线下快闪，并用最值的姿势参与 —— 基于麦当劳中国官方 MCP 的 WorkBuddy Skill（**MVP**）。

本作品为 **2026 麦当劳程序员创意开发大赛**参赛作品。

## 这是什么

麦噹聚焦麦当劳**联名 / IP 联动 / 限量 / 线下快闪**这一垂直场景，把官方活动日历和积分账户变成一张「情报简报」：哪些联名正在进行、什么时候结束、怎么参与、你的积分怎么用最值。

## 为什么做

- 现有参赛作品集中在省钱 / 营养 / 点餐 / 泛活动雷达，**缺乏聚焦「联名 / 快闪」这一潮玩向垂直场景**的作品。
- 联名 / 快闪往往限时、限量、带抽奖，用户容易错过；用 AI 做情报聚合 + 倒计时 + 积分策略，是真实且高频的痛点。
- MVP 聚焦两大可落地模块：**联名情报台** + **积分增益策略**，后续可扩展自定义汉堡、就近快闪。

## MVP 功能

- 🛰️ **联名情报台**：`campaign-calendar` 拉当月活动，筛出联名 / IP / 限定 / 快闪类，卡片化展示（主题、时间窗、状态、倒计时、参与方式）。
- 💎 **积分增益策略**：`query-my-account` 看余额 / 即将过期，给「现在兑换 / 参与最值」的策略建议（明确：MCP **不能真加积分**，仅策略与提醒）。
- ⏰ **时间校验**：`now-time-info` 换算相对时间、判断活动状态。
- 🔗 **纯官方数据**，实时准确。

## 技术架构

- **数据层**：麦当劳中国官方 MCP Server（`https://mcp.mcd.cn`，Streamable HTTP，Bearer Token 鉴权）
- **编排层**：本 Skill 对话流程，调用 `campaign-calendar` / `query-my-account` / `now-time-info`，可选 `query-lottery-info` / `mall-points-products`
- **宿主**：WorkBuddy（赛事官方合作伙伴），也可接入任意支持 MCP 的客户端

## 安装与接入

### 方式一：在 WorkBuddy 中使用（推荐，可拿 3000 积分专项奖励）

1. 申请麦当劳 MCP Token：<https://open.mcd.cn/mcp>（手机号登录 → 控制台 → 激活 → 申请）
2. 左侧【专家 · 技能 · 连接器】→【连接器】→ 右上【自定义连接器】→【配置 MCP】
3. 填入（替换 `YOUR_MCP_TOKEN`）：

   ```json
   {
     "mcpServers": {
       "mcd-mcp": {
         "type": "streamablehttp",
         "url": "https://mcp.mcd.cn",
         "headers": { "Authorization": "Bearer YOUR_MCP_TOKEN" }
       }
     }
   }
   ```

4. 保存并启用 `mcd-mcp`。
5. 把**整个仓库目录**放到 WorkBuddy 用户级技能目录（注意要带上 `references/`，否则会丢掉工具速查）：

   ```bash
   git clone https://github.com/Leonardo0402/mcd-dang.git ~/.workbuddy/skills/mcd-dang
   ```

   之后即可在对话中直接使用。

### 方式二：其他 MCP 客户端

同上 JSON 配置接入 `mcd-mcp`，Skill 的指令流同样适用（Cherry Studio / Cursor / Trae / VSCode 等）。

## 实际输出示例（真实联调 · 2026-10-09 快照）

> 以下为依据**真实 MCP 返回**整理的一次完整简报，非虚构；活动与积分会随时间变化，实际以实时查询为准。

**用户**：这个月麦当劳有什么联名？我积分快过期了怎么用最值？

---

⏰ 时间基准：2026-10-09（来自 `now-time-info`）

🛰️ **联名情报台** — 本月命中 2 个联名 / IP 联动活动

| # | 主题 | 时间窗 | 状态 | 参与方式 | 判定依据 |
|---|---|---|---|---|---|
| 1 | 麦当劳 X PEACEMINUSONE · GD 联名复古棒球帽 | 10/8 起开售 | 🟢 进行中 | 加价 ¥39.9 购限定周边 | 品牌 × 品牌（"X" + "联名"） |
| 2 | 麦当劳联动 G-DRAGON · 韩式风味系列 | 当月 | 🟢 进行中 | 龙焰鸡腿堡三件套 ¥26.9 起 | "联动" + IP 名称 |

> ⚠️ 上述"联名"判定为**基于活动名称 / 描述的语义判断**，并非官方结构化分类 —— 官方 `campaign-calendar` 返回的是按日期分段的 Markdown，没有类型字段。

💎 **积分增益策略**（来自 `query-my-account`，币种：麦享会积分）

| 指标 | 数值 |
|---|---|
| 可用积分 | 227.2 |
| 累计积分 | 307.1 |
| **本月将过期** | **88** ← 优先处理 |
| 已过期 | 79.9 |

**建议**：

1. 优先消耗本月将过期的 **88** 分 —— 走 `mall-points-products` 看可兑换项，别让它变成下一笔"已过期"。
2. 可用 227.2 分若想参与联名抽奖，先用 `query-lottery-info` 看消耗规则与奖品池，再决定是兑换还是抽限定。
3. ⚠️ MCP **没有"增加积分"接口** —— 麦噹只能帮你查和算，不能帮你"赚"积分。

## 已知边界（诚实披露）

- **积分只能策略建议，不能真加**：MCP 无「增加积分」接口，仅可查询余额与兑换，本模块只做提醒与建议。
- **联名识别依赖名称/描述（已实测）**：`campaign-calendar` 返回按日期分段的 Markdown 文本（含"今日 / 往期回顾"状态标签），**无结构化类型字段**；MVP 用关键词 + 语义判断筛联名 / 快闪，并明确标注判断依据。实测确认当月已有"麦当劳 X PEACEMINUSONE""麦当劳联动 G-DRAGON"等联名活动，场景成立。

## 目录结构

```
mcd-dang/
├── SKILL.md                 # WorkBuddy 技能定义（核心源代码）
├── README.md                # 本文件
├── ROADMAP.md               # 路线图（已交付项 + 后续模块规划）
├── MCP_INTEGRATION.md       # MCP 接入说明（赛事要求）
├── CONTEST_DECLARATION.md   # 参赛声明（官方原文，不可改）
├── workbuddy.md             # WorkBuddy 开发上下文（3000 积分联动奖励核验）
├── LICENSE                  # MIT
├── .gitignore               # 屏蔽 Token / 密钥等隐私文件
├── assets/
│   └── banner.svg           # README banner
└── references/
    └── tools.md             # 核心 MCP 工具参数速查
```

后续规划（自定义汉堡 / 就近快闪）见 [ROADMAP.md](ROADMAP.md)。

## 目标用户

- 关注麦当劳联名 / 限定 / IP 联动 / 快闪的潮玩玩家
- 不想错过限时活动、又怕积分过期浪费的人
- 喜欢用 AI 一句话获取结构化情报的人

## 免责声明

本作品为大赛参赛作品，非麦当劳官方产品；输出仅供参考，不构成专业建议；餐品信息、价格及供应状态以麦当劳官方渠道的实时结果为准。

## 参赛信息

- 赛事：2026 麦当劳程序员创意开发大赛
- 官方仓库：<https://github.com/M-China/mcd-developer-innovation-challenge>
