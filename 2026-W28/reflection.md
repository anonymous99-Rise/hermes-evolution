## Week 28 (2026-07-06 ~ 2026-07-12)

### 🎯 本周主题
安全Agent框架融合落地 + 系统层问题排查（CentOS7 SSL / D-Bus / 迁移）

---

### 📚 学到了什么

- **D-Bus StartServiceByName 报错**：`org.freedesktop.DBus StartServiceByName failed: connection closed` — 意思是 systemd 的 D-Bus 总线没正常注册，服务之间无法通过 D-Bus 通信，但不影响应用前台直接运行
- **CentOS 7 OpenSSL 1.0.2k 太老**：Python 3.13 编译时要求 OpenSSL 1.1.1+，自编译 Python 如果系统 OpenSSL 版本不够，`_ssl` 模块不会被编译进去，`import ssl` 直接报错
- **新疆兵团是准军事化组织**：1954年成立，14个师遍布新疆各地，自成体系（公检法、学校、医院），既是移民又是戍边也是生产
- **Docker 是 CentOS 7 最小改动迁移方案**：零重装，数据路径直接挂载进去，Ubuntu 容器内全套跑
- **Anolis OS 支持从 CentOS 7 在线迁移**：阿里开源 Linux，不重装、数据无损，适合长期用
- **ATT&CK Navigator Layer JSON**：可以生成可视化图层，覆盖的 technique 数量代表 skill 库的覆盖密度

---

### 🔄 行为改变

- **遇到 SSL / OpenSSL 报错**：不再盲目重装 Python，而是先 `python3 -c "import ssl; print(ssl.OPENSSL_VERSION)"` 确认是否真的编译进去了，优先检查系统 OpenSSL 版本
- **遇到 D-Bus / systemd 服务报错**：先试 `--no-daemon` 或前台运行，往往不影响实际功能
- **CentOS 7 服务器要跑新服务**：优先考虑 Docker 方案（10 分钟能跑起来），比升级系统风险小

---

### 🤕 踩过的坑

- **CentOS 7 `pip install` 全部 SSL 失败** → 系统 OpenSSL 版本太老（1.0.2k），curl / git 同样受影响，根源是自编译 Python 没有链接到正确 OpenSSL → **下次装 Python 先 `ldd python3 | grep ssl` 确认动态库是否链接成功**
- **OpenClaw gateway install 报 systemd 错误** → D-Bus 本身没注册成功，systemctl daemon-reload 失败连锁反应 → 解决：先修 D-Bus 或直接 `openclaw gateway run --no-daemon` 前台跑
- **Vercel 对内容分支（daily/weekly）也触发构建** → 没有在根目录加 `vercel.json` 的 `git.exclude` 配置 → 解决：在 repo 根目录加 `vercel.json` 排除内容分支

---

### 🤔 待改进

- 迁移到 weekly 分支推送后，需要同步更新 MEMORY.md，避免重复记录
- 渗透测试报告模板还没形成正式规范，下次做项目时沉淀一份

---

### 📌 核心洞察

> **CentOS 7 是"债务型"服务器环境——系统层面的坑（OpenSSL、glibc、kernel）不会一次全暴露，而是随应用需求逐步爆发。遇到报错先找系统层原因，而不是应用层。Docker 隔离是最低成本的逃生舱。**
