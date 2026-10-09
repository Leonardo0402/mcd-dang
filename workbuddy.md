# workbuddy.md — 麦噹 (Maidang) WorkBuddy 开发上下文

> 本文件为「使用 WorkBuddy 开发时的对话上下文」，用于核验本项目是否符合 2026 麦当劳程序员创意开发大赛的 WorkBuddy 联动奖励条件（提交可获 3,000 WorkBuddy 积分）。本 Skill 全程在 WorkBuddy 中构思、开发、联调与产出。

## 1. 项目基本信息

- **Skill 名称**：`mcd-dang`（展示名：麦噹 / Maidang）
- **类型**：WorkBuddy 用户级 Skill（SKILL.md 驱动）
- **依赖连接器**：`mcd-mcp`（麦当劳中国官方 MCP Server，Streamable HTTP）
- **MVP 范围**：模块 1 联名情报台 + 模块 2 积分增益策略
- **开发宿主**：WorkBuddy（赛事官方合作伙伴）

## 2. 在 WorkBuddy 中的开发过程（对话上下文摘要）

1. **从 0 构思**：在 WorkBuddy 对话中分析赛事规则（GitHub Star 排名驱动、Top100 实物奖励、WorkBuddy 3000 积分专项），筛选蓝海方向（联名 / IP 联动 / 线下快闪），排除已被占用的「活动雷达 / 情报局」命名。
2. **确定方向**：与用户确认聚焦「联名 / 积分策略」，先交付 MVP，自定义汉堡 / 就近快闪留作后续模块。
3. **命名收敛**：在 WorkBuddy 中比选「麦噹（噹，U+5679）/ 雷达 / 情报」等候选，最终定为 `mcd-dang`，展示名 麦噹（对应"麦当当"昵称习惯）。
4. **MCP 接入与联调**：通过 WorkBuddy【连接器】配置 `mcd-mcp`，由 WorkBuddy 调用 `campaign-calendar` / `query-my-account` / `now-time-info` 等官方工具做**只读实测**，验证联名场景真实存在、积分字段名（`currentMouthExpirePoint` 等）与返回结构。
5. **文档产出**：在 WorkBuddy 中生成 `README.md` / `SKILL.md` / `MCP_INTEGRATION.md` / `CONTEST_DECLARATION.md` / `references/tools.md`，并将实测结论回填至文档（已知边界由"待测"转为"已实测"）。

## 3. WorkBuddy 在本项目中的关键能力使用

- **Skill 工程**：以 SKILL.md 定义技能（frontmatter `name: mcd-dang` + 6 步工作流），可直接被 WorkBuddy 加载使用。
- **MCP 连接器**：在 WorkBuddy 中配置 `mcd-mcp`，由 WorkBuddy 编排调用官方工具，Token 仅存于连接器配置（不进入代码 / 仓库）。
- **实测验证**：借助 WorkBuddy 的 MCP 调用能力完成真实数据联调，避免臆造活动 / 价格 / 积分。

## 4. 本 Skill 如何被 WorkBuddy 使用（复现方式）

1. 在 WorkBuddy 左侧【专家·技能·连接器】→【连接器】→【自定义连接器】→【配置 MCP】，填入官方 `mcd-mcp` JSON（Bearer Token 占位）。
2. 启用 `mcd-mcp`。
3. 将本仓库 `SKILL.md` 放入 WorkBuddy 用户级技能目录（`~/.workbuddy/skills/mcd-dang/`）。
4. 在对话中输入如"这个月麦当劳有什么联名？我的积分怎么用最值？"，WorkBuddy 即按 SKILL.md 工作流调用官方工具并产出简报。

## 5. 合规与边界声明（与 CONTEST_DECLARATION.md 一致）

- 仓库 / 代码 / 截图中**不含任何真实 Token、密钥或账号凭证**；凭证仅来自 WorkBuddy 连接器配置。
- `mcp.json` 中的 Token 仅存于本地 WorkBuddy 配置，未提交本仓库。
- 输出价格 / 活动 / 积分以官方 MCP 实时结果为准，不臆造。
- 积分模块仅做查询与策略建议，MCP 无"增加积分"接口，本作不能"加"积分。

---

*本文件为 WorkBuddy 开发上下文导出，供赛事奖励核验使用。项目主体源代码见 `SKILL.md` 与 `references/tools.md`。*
