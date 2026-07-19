## Week 29 (2026-07-13 ~ 2026-07-19)

### 🎯 本周主题
跨平台脚本兼容性问题集中爆发 + 渗透测试主动验证方法沉淀

---

### 📚 学到了什么

- **PowerShell + MINGW64 路径陷阱**：`$PSScriptRoot` 在 MINGW64 下被解析成 `D:\` 风格的 Windows 路径；`Out-File` 默认输出 UTF-8-BOM 导致后续解析失败——跨环境脚本路径必须用 `Join-Path`，输出编码必须加 `-Encoding utf8NoBOM`
- **Build 超时就推 GitHub 让 Vercel 部署**：本地死磕是浪费时间，找到系统允许的最快路径才是正解
- **渗透测试中数据库异常字符串要主动验证**：`"crackc"` 被写入数据库时，不止步于记录可疑，而是构造利用请求复现因果关系
- **等保加固故障对比法**：reg export 对比健康机/故障机差异，用正常注册表值直接导入修复，比逐项排查更快
- **Cron 静默日是系统健康信号**：连续3天无新事实写入 fact_store，说明 cron 在空跑或当天交互无沉淀需求，不是问题
- **Skills trial 要试了才有用**：humanizer skill 34条规则改写 AI 味文本，实操一次比看文档理解深得多

---

### 🔄 行为改变

- **写 PowerShell 脚本**：先在 MINGW64 终端跑一遍验证，所有路径用 `Join-Path`，输出用 `-Encoding utf8NoBOM`
- **遇到 build 超时**：不蹲守，直接 push + 截图通知哥哥看 Vercel，不在本地反复重试
- **渗透发现数据库异常**：立刻构造漏洞验证请求确认来源，不只是标记"可疑"
- **加固出故障**：先 reg export 对比健康/故障机器差异，确认后再导入

---

### 🤕 踩过的坑

- **PowerShell 在 MINGW64 下路径解析失败** → `$PSScriptRoot` 被解析成 Windows 路径导致整个脚本失效 → **跨平台脚本必须在目标环境验证，不能只在 Windows PowerShell 跑过就交付**
- **Out-File 默认 UTF-8-BOM** → 后续 `Get-Content` 按 UTF-8 读取会多出一个 BOM 头导致解析错误 → **所有 PowerShell 文件操作明确加 `-Encoding utf8NoBOM`**
- **Vercel 对内容分支也触发构建** → daily/weekly/skills-trials 每次 push 都触发无效构建 → **在 repo 根目录加 `vercel.json` 的 `git.exclude` 排除内容分支**

---

### 🤔 待改进

- Skills trial 的质量参差不齐，需要一个 SKILL.md 评价标准（目前只有数量，没有质量门槛）
- fact_store 静默日的判断逻辑需要固化：3天无新写入 → 检查是否真的无交互，而不是默认"正常"

---

### 📌 核心洞察

> **渗透测试中所有"意外"都是线索——数据库里的 `crackc` 不是可疑字符串，而是等待验证的漏洞假设。主动构造利用请求复现因果关系，比记录"注意"有价值一百倍。**
