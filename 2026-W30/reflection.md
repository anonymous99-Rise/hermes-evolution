## Week 30 (2026-07-21 ~ 2026-07-26)

### 🎯 本周主题
系统稳定运维周——GitHub账号迁移冲刺、等保加固收尾、技能体系整合

### 📚 学到了什么
- **注册表 diff 恢复法**：故障机 vs 正常机 `reg export` 差量对比，精准定位哪个键值被改，而非直接覆盖恢复
- **Secondary Rate Limit 是 GitHub 的隐藏关卡**：主限速 5000次/小时 可预测，次级限速在 ~1800 请求后 403，连续触发后全挂；3秒间隔可以绕过
- **GitHub Token 提交即撤销**：commit 里包含 PAT 会触发自动扫描撤销，且撤销不可逆；必须用参数传入脚本
- **PowerShell+MINGW64 路径兼容**：$PSScriptRoot 在 MINGW64 下被解析成 "D:\"；所有路径必须用 Join-Path 拼接
- **Token 两类错误信号不同**："No such device or address" = credential helper/TPM 存储不可用；401 Unauthorized = GitHub 端已撤销
- **静默日 = 健康信号**：没有新对话说明 MEMORY.md 不需要更新，Telegram Bad Gateway 自动重连是正常行为

### 🔄 行为改变
- **不再直接覆盖恢复**：等保加固故障恢复前，先 diff 正常机 vs 故障机，找差异键值再定向恢复
- **加固任务完成后立即验证一致性**：发现报错立刻中断，而非假设"后面会补上"
- **Token 永不进 GitHub**：任何脚本的 token 都要通过参数传入，README 里要写清楚怎么传

### 🤕 踩过的坑
- **等保加固中途报错重启**：WindowsBaselineAssistant 在 39 机跑了一半就重启，导致配置不一致，恢复花了大量时间
  → 下次：加固任务中途报错，立即停手，不要假设"重启后继续跑就对了"
- **GitHub Token 被撤销**：restore_stars.ps1 第一版把 token 写死在脚本里，commit 后秒被 GitHub 扫描撤销
  → 教训：任何 token/PAT/密钥都不能进 Git commit，哪怕 test 都不行
- **daily insights 偶发推送失败**：某些天没有记录，可能是 context 超长导致 cron 工具调用失败
  → 待修复：减少 daily insight cron 的工具调用数量，压缩上下文

### 🤔 待改进
- daily insights 偶发缺失（某几天没有推送），cron 上下文压缩逻辑需要优化
- skills 安装了要用——Claude-BugHunter 71个技能已装，但本周没有新增试用记录
- GitHub Stars 恢复（~1394 个待 star）—— 受限于 Secondary Rate Limit，需要继续跑

### 📌 核心洞察
**系统加固的中途故障 = 配置处于不一致的危险状态；恢复策略必须用 diff 对比而非直接覆盖——正常机 vs 故障机，找到差异点再精准回滚，比"全部还原"更可靠也更能保留已生效的配置。**
