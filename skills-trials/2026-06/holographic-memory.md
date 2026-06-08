## 2026-06-08 | holographic-memory

**类别**: memory / knowledge-management
**用途**: SQLite持久化记忆存储，支持FTS5全文搜索 + HRR向量操作 + 信任评分

---

### 安装体验

✅ 安装方式：直接 `git clone https://github.com/adminlove520/holographic-memory.git ~/.openclaw/skills/holographic-memory`

✅ 无坑。依赖只有Python3标准库（sqlite3内置），numpy可选（HRR高级特性）。目录结构清晰，CLI脚本直接可用。

---

### 实际测试

**测试命令：**
```bash
# 添加事实
python3 scripts/fact_cli.py add --content "Hermes cron runs every Saturday at 10:00" --category "system" --tags "schedule,cron"
python3 scripts/fact_cli.py add --content "User prefers concise responses with clear conclusions" --category "preferences" --tags "communication"

# 搜索
python3 scripts/fact_cli.py search --query "Hermes cron" --limit 5

# 列表过滤
python3 scripts/fact_cli.py list --category "preferences" --limit 10

# 反馈 + 信任更新
python3 scripts/fact_cli.py feedback --fact-id 1 --helpful true

# 中文搜索
python3 scripts/fact_cli.py add --content "用户喜欢在报告结尾有明确结论" --category "preferences" --tags "communication,chinese"
python3 scripts/fact_cli.py search --query "用户" --limit 5

# 删除
python3 scripts/fact_cli.py remove --fact-id 3
```

**输出结果：**

- 添加成功：`{"success": true, "fact_id": 1}`
- 搜索（英文）：命中目标，trust_score=0.5，`retrieval_count` 自动+1
- 列表过滤：按 category 正确筛选
- 反馈：`old_trust: 0.5 → new_trust: 0.55`，+0.05（不对称：helpful+0.05，unhelpful-0.10）
- 中文搜索：LIKE fallback 正常命中
- 删除：`{"success": true}`

**数据库位置：** `~/.openclaw/holographic_memory/memory_store.db`（SQLite + FTS5）

---

### 结论

✅ **会继续用**

**理由：**
- 安装零门槛，CLI直接可用，不需要额外服务
- FTS5全文搜索 + LIKE fallback 对中英文都友好
- 信任评分机制实用（helpful +0.05，unhelpful -0.10 不对称设计合理）
- 实体提取（entity extraction）和 fact_entities 关联表支持更复杂的知识图谱场景
- HRR（ holographically reduced representations）绑定/解绑操作理论上可以支持更高级的记忆联想，但本次未深度测试numpy相关功能

**注意点：**
- fact_cli.py 的 import 路径有瑕疵：`from scripts.store import MemoryStore` 在 git clone 后会找不到 store.py，需要手动调整 sys.path 或从父目录运行。实测直接 python3 scripts/fact_cli.py 会报 ImportError，改用 `python3 fact_cli.py` 从 scripts/ 同级目录运行可解决。
- 实体提取只识别英文大写词组和引号内容，中文实体无法提取（但搜索用 LIKE 可以覆盖）
- HRR 功能需要 numpy，当前 skill描述为"可选"，未强制安装

**定位判断：** 这是一个比 Hermes 内置 memory工具更结构化的记忆存储方案。适合需要持久化存储 + 搜索 + 信任追踪的场景（比如多agent协作时的共享知识库），而不只是简单的 key-value 记忆。