## Week 27 (2026-06-23 ~ 2026-06-29)

### 🎯 本周主题
等保加固故障深度实战 + Hermes 进化系统持续运转

### 📚 学到了什么
- **故障排查基准思维**：先用一台正常机器做"参照物"，reg export 对比差异，比凭空猜快十倍
- **WindowsBaselineAssistant 机制**：registry 类型直接写注册表，secedit 类型生成 fortify.cfg 再批量应用；中途报错会导致机器处于不一致的半吊子状态
- **关键风险注册表项**：`RestrictAnonymous/RestrictAnonymousSAM`、`DisableIPSourceRouting`、`SynAttackProtect` 被改成 1/2 后可能拦掉 RDP 连接
- **computer-use skill**：后台运行不抢焦点，与 browser skill 互补
- **daily insights cron 已稳定运转**：W25/W26 都有记录

### 🔄 行为改变
- 等保加固前，必须先 reg export 一份当前注册表状态作为"基准快照"
- 加固过程中如果报错了，不能直接重启，必须先记录断在哪一步
- 故障排查时，优先用正常机器做参照物 diff，而不是先翻日志

### 🤕 踩过的坑
- **等保加固中途报错直接重启** → 导致机器处于不一致状态，恢复成本远高于预防成本
  → 下次：加固前先 reg export 基线，加固后立即验证，坏了直接对比 diff 定位是哪个 key
- **fortify.cfg 文件不在仓库里** → 只能靠 item.xml 推断，无法直接拿到用户环境里实际生效的配置
  → 下次：让用户先提供 fortify.cfg 再开始诊断
- **工具设计缺陷**：registry 类型直接写注册表，没有任何备份和回滚机制
  → 下次：给哥哥建议加备份功能

### 🤔 待改进
- daily insights 格式要统一，W26 只有 2026-06-20 和 2026-06-18 的记录
- 等保加固流程需要整理成标准操作 SOP
- skills-trials 试用频率偏低，本周只试了 computer-use

### 📌 核心洞察
**"防比治更重要"——等保加固前的一行 reg export，比坏了之后花一小时 diff 快得多。**
