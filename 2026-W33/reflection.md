## Week 33 (2026-08-09 ~ 2026-08-15)

### 🎯 本周主题
身份安全警觉周——GitHub Star钓鱼实战分析、TB99 README全面重构、hermes-evolution仓库不可用问题排查

### 📚 学到了什么
- **SSH MOTD图片不是恶意代码**：伪装成"H200服务器"的钓鱼节点（`wrk0-nyc1.inference.net`）推送的是 iTerm2/WezTerm 的 inline image escape sequence，在 MOTD 登录横幅里显示一张远程图片，不是真正的图片文件，是行为记录节点
- **GitHub Secondary Rate Limit 是隐藏关卡**：~1800 请求后开始 403，即使没超主限速 5000次/小时；3秒间隔可以绕过，连触后会全挂
- **PowerShell+MINGW64 路径不兼容**：MINGW64 里 `$PSScriptRoot` 被解析成类似 "D:\" 的格式，Windows迁移脚本里所有路径必须用 Join-Path 拼接
- **daily insight cron 推错了仓库**：hermes-evolution 仓库属于 adminlove520（已suspend），daily insight 实际推到了 anonymous99-Rise/multi-agent-tasks 的 daily-v2 分支，WebUI 读的是旧仓库所以无数据
- **CentOS 7 SSL完全堵死**：curl/pip/git全被SSL handshake阻断，wget也超时，服务器无法下载任何外部资源

### 🔄 行为改变
- **不再把token/PAT写进任何脚本**：GitHub commit 会触发自动扫描秒撤销，教训深刻
- **遇到"服务器弹图片"先怀疑MOTD图片协议**：不是中马，是 SSH honeypot 在用 terminal escape sequence 钓鱼
- **迁移前先确认目标仓库状态**：hermes-evolution 仓库不可访问的问题要先解决，才能恢复 skills-trials 推送

### 🤕 踩过的坑
- **daily insights 推到了错误的仓库**：`anonymous99-Rise/multi-agent-tasks` 而不是 `adminlove520/hermes-evolution`，导致 WebUI 读不到数据。根本原因：adminlove520 已被 suspend，所有关联仓库都不可访问
  → 下次：推送前先验证目标仓库是否可读
- **CentOS 7 服务器下载任何东西都失败**：SSL握手超时，因为系统自带的CA证书太老。Docker方案虽然能跑但 Hermes 环境完全不可用
  → 下次：提前告知哥哥这是 CentOS 7 的根本限制，不换系统无法解决
- **TB99 README 图片显示不全**：workflow 生成的徽章图片在 GitHub Actions runner IP 被某些CDN封锁后无法加载，只能降级到静态徽章

### 🤔 待改进
- hermes-evolution 仓库不可访问问题需根本解决（adminlove520 suspend 申诉？）
- CentOS 7 服务器的 SSL 问题——建议哥哥尽快迁移到 Docker 或新系统
- daily insight cron 的目标仓库需要重新配置到正确的匿名账号

### 📌 核心洞察
**SSH 连接时"弹出的图片"不是被中了马，而是终端的 inline image escape sequence——攻击者利用 MOTD 登录横幅和 iTerm2/WezTerm 的图片协议，在用户连接瞬间推送一张远程图片来记录身份（GitHub公钥+IP），这不是恶意软件，是高明的社工钓鱼。**
