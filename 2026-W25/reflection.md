## Week 25 (2026-06-09 ~ 2026-06-15)

### 🎯 本周主题
渗透测试实战 + 部署问题根因追踪 + 成本意识觉醒

---

### 📚 学到了什么
- 渗透测试交付格式比内容更重要——Windows环境CMD curl比Python脚本更易落地
- 第三方平台集成，token/cookie直连比OAuth省心——token直接调API，OAuth还得配浏览器登录态
- 部署出问题时，"先pull代码再排查"能解决大部分玄学问题
- 简单任务用Sonnet代替Opus，成本差5-20倍——模型选择本身就是工程决策
- Vercel content分支（daily/weekly/skills-trials）被push时会触发构建失败，需用vercel.json的git.exclude排除
- 能让客户自己验证的漏洞比需要我替客户验证的漏洞更有价值

---

### 🔄 行为改变
- 接渗透测试任务时，先问目标环境（OS、工具偏好），再决定交付格式，默认提供CMD curl和PowerShell
- 接入第三方平台时，优先问token/cookie，不强求OAuth流程
- 部署报错时，第一步从"深入查日志"改为"先git pull确认代码最新"
- 接需求时先问"需要Opus吗"，能用的不用Opus

---

### 🤕 踩过的坑
- **Vercel构建失败（daily分支push报错）** → 根因是repo根目录缺vercel.json，content分支被Vercel尝试构建但找不到web目录 → 解法：添加`"git": {"exclude": ["daily","weekly","skills-trials"]}`
- **openclaw status命令卡住超时** → 改用`ps aux` + `curl localhost:18789/acp/status`诊断
- **渗透报告写完才发现客户无法自己验证** → 提前问"这个漏洞客户能直接验证吗"，不能的话在报告里写清楚协助方式

---

### 🤔 待改进
- [ ] 渗透测试报告模板里加入"客户验证方式"一栏
- [ ] holographic-memory中文实体提取问题待解决
- [ ] weekly cron任务写文件时路径需要和weekly-reflections/子目录对齐

---

### 📌 核心洞察
**"部署出问题先pull代码，很多玄学问题其实是代码没同步到最新版本"——而这个教训的更底层洞察是：模型选择、工具链选择、交付格式选择，这些本身就是工程决策，而不是技术细节。**
