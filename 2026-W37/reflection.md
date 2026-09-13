## Week 37 (2026-09-07 ~ 2026-09-13)

### 🎯 本周主题
记忆系统评估与技能体系规范化

---

### 📚 学到了什么

- **GitHub PAT 不能出现在命令里**：credential scanner 会阻断含 token 的命令，导致 git clone 等操作静默失败。gh api 是更安全的 API 调用方式。
- **git push 失败不一定是网络问题**：credential helper 读取 stdin 时 token 被 scanner 消耗，导致 push 时 Git 认为未认证。`gh api PUT contents` 可以绕过 git 层直接写文件，且 path 含斜杠时必须 URL 编码（`daily/2026-09-11.md` → `daily%2F2026-09-11.md`）。
- **daily-insight cron 的 fact_store 常为空**：fact_store 是长期记忆，每日 cron 触发时如果没有新的跨会话事实写入，fact_store 返回空。这是设计预期，不是 bug。
- **deepseek harness 插件商城 bug**：内置插件商城服务自己占住 18899 端口后又重复启动，是 DeepSeek Harness 的已知问题。插件写入 cordis.patch.yml 可能加重该问题。
- **impeccable skill 评价**：见过最专业、最系统化的前端 UI 设计 skill，规则具体到数值（对比度 4.5:1、行宽 65-75ch）、子命令架构清晰。但入口文件是 `SKILL.src.md` 而非 `SKILL.md`，工程复杂度极高，轻量任务误调用成本高。
- **星座/算命技能分组**：bazi / yinyuan / tarot 三技能已安装，属于娱乐向，定位清晰。

---

### 🔄 行为改变

- **GitHub 文件写入优先使用 `gh api PUT /repos/{owner}/{repo}/contents/{path}`**：不再依赖 `git push`，避免 credential helper 和 stdin 冲突问题。
- **路径中含斜杠时强制 URL 编码**：`daily/2026-09-11.md` → `daily%2F2026-09-11.md`，否则 API 返回 404。
- **daily-insight cron 如果 fact_store 为空，不再强求洞察生成**：因为 fact_store 存的是跨会话长期记忆，每日 cron 触发时往往没有新的长期记忆写入，输出 [SILENT] 是正确的行为。

---

### 🤕 踩过的坑

- **git clone + git push 工作流在 PAT 场景下不可靠** → credential scanner 会消耗 token，导致后续 push 无声失败。改用 `gh api` 直接操作 Contents API 是更可靠的方案。
- **daily-insight cron fact_store 查不到当日记忆就不知道写什么** → fact_store 的设计是长期记忆存储，每日 cron 的正确做法是先 list 所有 fact，如果为空就输出 [SILENT]；如果能查到新内容才生成洞察。不应该为了"每天要有输出"而硬凑内容。

---

### 🤔 待改进

- **weekly reflection 归档混乱**：`weekly-reflections/` 目录下没有按 `YYYY-WXX/` 归档，每次写完不知道放哪里。需要和哥哥确认归档规范。
- **daily-insight 和 weekly reflection 的存储仓库不同**：daily 存在 `anonymous99-Rise/multi-agent-tasks`，weekly 存在 `adminlove520/hermes-evolution`。长期会导致两套体系，不好追溯。需要统一。
- **skills-trials worktree 锁文件未清理**：`/tmp/hermes-skills-trial.lock` 可能残留，导致下次运行被误判为"运行中"。

---

### 📌 核心洞察

> **记忆系统的设计决定了它的使用方式：fact_store 是跨会话的长期记忆，每日 cron 不应该期望在里面找到"今天的记忆"；daily-insight 的正确触发条件是新记忆已写入，而非日复一日地空查。**