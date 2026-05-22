---
name: solopreneur-daily
description: |
  一人公司/独立开发者每日资讯播报技能。自动聚合 Indie Hackers、Hacker News、36kr、V2EX 等平台的最新案例和数据，生成精简日报。

  **使用场景**：
  (1) 需要生成每日一人公司/独立开发者资讯
  (2) 想了解最新的 SaaS MRR、跨境电商、自媒体变现案例
  (3) 设置定时播报任务（cron job）
  (4) 用户提到"一人公司日报"、"独立开发者资讯"、"solopreneur"、"indie hacker"
---

# 一人公司每日播报

自动聚合多平台资讯，生成精简中文日报。

## 信息源

### 必抓取
1. **Indie Hackers**: `web_fetch https://www.indiehackers.com/`
2. **Hacker News Show**: `web_fetch https://news.ycombinator.com/show`

### Tavily 搜索关键词
```
# 跨境电商
site:36kr.com 出海 跨境电商 独立站
Amazon FBA 独立卖家
TikTok Shop 个人卖家

# 自媒体
site:v2ex.com 自媒体 副业 变现
自媒体 博主 收入 变现
内容创作者 收入 2026

# 软件出海
site:indiehackers.com SaaS MRR bootstrap
site:producthunt.com launched indie
indie developer software revenue 2026
独立开发者 SaaS 月收入
solo founder software business

# 通用
跨境电商 一人公司 2026
```

## 去重机制

**重要**：防止重复推送相同案例。

1. 生成前读取历史文件（如 `memory/solopreneur-daily-history.md`）
2. 检查人名、项目名是否已存在
3. 生成后将新案例追加到历史文件
4. 搜索不到新案例时宁可少发，不要重复

## 输出格式

控制在 **600 字以内**，纯 emoji 分隔，不用 markdown 格式符号。

```
📅 一人公司日报 | YYYY-MM-DD

🔥 热点 x2
→ 标题 + 一句话 + 链接

💰 赚钱案例 x2
→ 人名/项目 + 收入数据 + 一句话 + 链接

🖥️ 软件出海 x2
→ 产品名 + MRR/ARR + 一句话 + 链接

🇨🇳 华人案例 x1
→ 人名/项目 + 一句话 + 链接

🛒 工具推荐 x1
→ 工具名 + 一句话 + 链接

📊 数据 x2（一行一条）
→ 电商GMV / 自媒体收入 / SaaS指标
```

## 内容优先级

1. **软件出海**：独立开发者 SaaS、AI 工具、开发者工具、订阅制产品
2. **自媒体**：YouTube/TikTok/小红书博主收入、内容变现
3. **跨境电商**：Amazon/Shopify/TikTok Shop 独立卖家
4. **交易类**：套利、代购、dropshipping

## Cron 配置示例

```json
{
  "name": "一人公司日报",
  "schedule": {
    "kind": "cron",
    "expr": "0 8 * * *",
    "tz": "Asia/Shanghai"
  },
  "sessionTarget": "isolated",
  "payload": {
    "kind": "agentTurn",
    "message": "使用 solopreneur-daily 技能生成今日一人公司日报",
    "timeoutSeconds": 300
  },
  "delivery": {
    "mode": "announce",
    "channel": "feishu",
    "to": "chat:<your_chat_id>"
  }
}
```

## 要求

- 全中文输出
- 每条必须有链接
- 优先今日新内容
- 有具体收入数字的案例优先
