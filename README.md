# AI-fetch

**每日 AI 精选日报 · 卡片式推送 · OpenClaw Skill**

每天从 [BestBlogs.dev](https://www.bestblogs.dev) 筛选 AI 评分 ≥ 90 的高质量文章，以精致的卡片式日报推送。

BestBlogs.dev 已用 AI 对每篇文章做了摘要和评分——本 skill 不重复摘要，而是做策展：挑选、排序、点评，让你 5 分钟看懂今天 AI 世界发生了什么。

---

## 安装 / Installation

```bash
git clone https://github.com/xuhengzhi75/AI-fetch ~/skills/AI-fetch
cd ~/skills/AI-fetch/scripts && npm install
```

---

## 使用 / Usage

| 命令 | 功能 |
|------|------|
| `/ai-fetch` | 立即生成并推送今日日报 |
| `/ai-fetch setup` | 重新运行配置向导 |
| `/ai-fetch config` | 查看当前配置 |

首次运行会自动进入配置向导（推送频率、时间等），完成后立即推送第一份日报。

---

## 更新 / Update

```bash
cd ~/skills/AI-fetch && git pull && cd scripts && npm install
```

更新后无需重新配置，设置保存在 `~/.ai-fetch/config.json`。

After updating, config remains in `~/.ai-fetch/config.json` and no re-setup is required.

---

## 日报示例 / Sample Digest

见 [examples/sample-digest.md](examples/sample-digest.md)

---

## 配置项 / Configuration

配置文件位置：`~/.ai-fetch/config.json`

| 字段 | 默认值 | 说明 |
|------|--------|------|
| `frequency` | `"daily"` | `"daily"` 每天 / `"weekly"` 每周 |
| `deliveryTime` | `"08:00"` | 本地时间，HH:MM 格式 |
| `timezone` | `"Asia/Shanghai"` | IANA 时区 |
| `maxArticles` | `15` | 每期最多文章数（5–30）|

---

## 自定义 Prompt

将 `prompts/` 目录复制到 `~/.ai-fetch/prompts/` 并修改即可覆盖默认 prompt：

```bash
cp -r ~/skills/AI-fetch/prompts ~/.ai-fetch/prompts
```

---

## 数据来源 / Data Source

- 内容来源：[BestBlogs.dev](https://www.bestblogs.dev)（[ginobefun/bestblogs](https://github.com/ginobefun/bestblogs)）
- RSS 端点：`https://www.bestblogs.dev/en/feeds/rss?category=ai&minScore=90`
- 评分说明：BestBlogs 用 GPT-4o 对每篇文章打分（0–100），本 skill 只取 90 分以上的内容

---

## 技术架构 / Architecture

```
OpenClaw cron
     ↓
SKILL.md (skill brain)
     ↓
node scripts/fetch-rss.js --lang both
     ↓ (parallel fetch EN + ZH RSS, merge by GUID)
node scripts/prepare-digest.js
     ↓ (dedup, time-filter, sort by score, load prompts)
LLM (Claude) generates card-style digest
     ↓ (editorial curation + Chinese output)
OpenClaw delivers to channel
     ↓
Update ~/.ai-fetch/state.json (dedup state)
```

与 [follow-builders](https://github.com/zarazhangrui/follow-builders) 相比，无需 GitHub Actions 服务端管线 — BestBlogs RSS 本身就是公开的稳定数据源，每小时更新。

---

## 致谢 / Credits

- [BestBlogs.dev](https://www.bestblogs.dev) — 内容聚合与 AI 评分
- [zarazhangrui/follow-builders](https://github.com/zarazhangrui/follow-builders) — skill 架构参考
- [fast-xml-parser](https://github.com/NaturalIntelligence/fast-xml-parser) — XML 解析

---

## License

MIT © [xuhengzhi75](https://github.com/xuhengzhi75)
