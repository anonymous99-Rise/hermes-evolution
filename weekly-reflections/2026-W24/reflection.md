## Week 24 (2026-06-02 ~ 2026-06-08)

### 🎯 本周主题
Hermes 进化系统从 0 到 1：建仓库、搭 WebUI、跑通自运转

### 📚 学到了什么
- **Hermes v0.16.0 更新**：desktop config、YOLO mode、terminal markdown、max_spawn_depth 限制、simplify-code skill
- **Vercel 部署 WebUI**：`vercel.json` 配置 root 目录为 `web/`，无需登录即可公开访问
- **Git worktree 模式**：多分支并行操作不需要频繁 checkout，适合 cron 自动化
- **OpenClaw `openclaw status` bug**：命令本身会超时卡住，改用进程检查 + API 端点 `/acp/status` 代替
- **模型选择原则**：简单任务用 Sonnet 代替 Opus，成本差 5-20 倍
- **2026 AI Agent 元年**：Gartner 预测 40% 企业应用将内置 AI Agent，MCP 成为核心术语

### 🔄 行为改变
- 私密内容（笔记/配置/踩坑）必须存 private 仓库，public 只放真正想开源的——这个规范真正落地了
- 遇到服务异常时，先用进程检查 + API 端点代替会卡住的诊断命令（如 openclaw status）
- 自动化任务设置好后让它自己跑，不需要反复确认

### 🤕 踩过的坑
- OpenClaw WeChat 凌晨断连 → 重启 Gateway 解决，但凌晨 1 点 Telegram 短暂抖动后自动恢复，说明监控要有耐心
- `openclaw status` 命令本身会超时卡住（已知 bug）→ 改用 `ps aux` + `curl localhost:18789/acp/status` 诊断
- rdt-cli 0.4.2 有兼容问题 → 降级到 0.4.1 成功

### 🤔 待改进
- daily-insights 和 weekly-reflections 还是空的，下周要让 cron 真正跑起来写入内容
- WebUI 目前只有空状态页面，需要 cron 写入真实数据后才能看到效果

### 📌 核心洞察
**「行为改变」才是真正的学习——知道和做到之间，隔着一套自动运转的系统。**
