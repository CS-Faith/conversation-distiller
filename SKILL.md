---
name: conversation-distiller
description: 对话蒸馏器 — 把任意 AI Agent 的对话记录提炼为结构化深度知识文章，首次加载自动适配平台格式
---
# 对话蒸馏器 (Conversation Distiller)

> **自适配的对话知识蒸馏引擎。**
> 首次加载时自动发现当前平台的对话格式，适配器持久化到本 Skill 中，后续直接复用。
> 所有模板、闸门、策略均内置于本文，零外部依赖。

## 触发方式

用户说「对话入库」「蒸馏」「蒸馏器」「把对话加进知识库」等即可触发。

---

## 零、首次加载：自适配

**首次加载本 Skill 时，执行以下自适配流程：**

```
Step 0.1: 探索对话存储
  → 列出当前工作目录的文件结构
  → 搜索可能的会话文件（.jsonl / .json / .md / 数据库 / ...）
  → 抽样读取 1-2 个文件，理解其格式

Step 0.2: 编写适配器
  → 参考本 Skill 末尾「适配器示范代码」中的范例格式
  → 根据当前平台的实际格式，编写提取函数
  → 函数签名：def adapter(sessions_dir, last_date) -> [{id, date, summary, messages}]

Step 0.3: 进入蒸馏流程
  → 用刚写的适配器提取对话 → 送入蒸馏引擎

Step 0.4: （可选）贡献回示范代码
  → 如果当前平台的格式尚未出现在「适配器示范代码」中
  → 将自己的适配器追加进去，供其他 agent 参考
```

**关键原则**：
- Agent 自己探索、自己写适配器——不需要人类手动配置
- 「适配器示范代码」中的范例仅供**参考格式**，不要直接使用——每个平台的对话存储方式不同
- 每个平台只适配一次

---

## 一、蒸馏引擎（平台无关）

```
输入：统一的对话格式 [{id, date, summary, messages}]
  │
  ├─ Step 1: 确定增量范围（比对 last_session_date）
  ├─ Step 2: 深度理解与归类（同簇更新 / 新簇新建）
  ├─ Step 3: 按七段式模板撰写
  ├─ Step 4: 通过 10 项质量闸门
  └─ Step 5: 更新索引 + 建立双向引用
```

---

## 二、接力七段式文章模板

### Frontmatter 必填字段

```yaml
---
title: {文章标题}
date: YYYY-MM-DD
updated: YYYY-MM-DD
tags:
  - {领域}/{子领域}
  - status/{resolved|in-progress|blocked|abandoned}
aliases:
  - {别名}
status: resolved | in-progress | blocked | abandoned
session_id: {唯一标识}
topic: {一句话概括}
last_session_date: YYYY-MM-DD
problem_cycle: {所属问题周期}
key_decisions:
  - "{决策及原因}"
known_traps:
  - "症状: {症状} | 根因: {根因} | 修复: {方法}"
next_actions:
  - "{可被直接执行的原子动作}"
related_articles:
  - "[[相关文章]]"
completion_criteria: {完成标准}
---
```

### 正文七段

```markdown
# {标题 — 含可搜索关键词}

> [!abstract] 如果你需要{场景}，这篇文章是你的入口。如果你只是{不适用场景}，请跳过。

## 1. 这是什么问题？
→ ≤3 句话，含具体系统/工具名称，用「如果…那么…」句式

## 2. 当前状态
→ 里程碑 + 完成百分比（==70%==），用 ✅ 🔄 ❌ 标注

## 3. 关键决策与因果
→ 每个决策说明「为什么这么做」
→ 推断内容用 > [!inference] 标注

## 4. 踩过的坑
→ 每个坑 = 症状 → 根因 → 修复（三要素缺一不可）

## 5. 接口与依赖
→ 系统/工具/配置的关系

## 6. 待办与下一步
→ 原子动作列表

## 7. 完成定义
→ 做到什么程度算结束
```

### 第 1 节「如果…那么…」句式

```
❌ "本文记录了 XX 的配置过程。"
✅ "如果你需要配置 XX 的 YY 功能，这篇文章是你的入口。如果你只是查询 ZZ，请跳过。"
```

---

## 三、质量闸门（10 项必检）

任一不通过 → 标记为 `status: draft`。

| # | 检查项 | 类别 |
|---|--------|------|
| 1 | `status` 为 resolved / in-progress / blocked / abandoned | frontmatter |
| 2 | `key_decisions` ≥ 1 条 | frontmatter |
| 3 | `known_traps` 不为空白 | frontmatter |
| 4 | `related_articles` ≥ 1 篇 | frontmatter |
| 5 | 第 1 节 ≤ 3 句话，含系统名 | 正文 |
| 6 | 第 2 节含明确里程碑/百分比 | 正文 |
| 7 | 每个 trap 含「症状→根因→修复」 | 正文 |
| 8 | 每个 action 可被直接执行 | 正文 |
| 9 | 无"详见原始对话"类引用 | 反模式 |
| 10 | 时间/版本号具体，无模糊词 | 反模式 |

### 格式化四要素

- ✅ tags（层级化）
- ✅ 内部链接（≥3 个正文交叉引用）
- ✅ callouts（≥1 个 `> [!type]`）
- ✅ aliases（≥1 个别名）

---

## 四、更新策略

### 策略 A：更新现有文章
**条件**：新对话属于已有 `problem_cycle` 且 `status` ≠ `abandoned`。
追加 key_decisions / known_traps（去重），更新日期和完成度，正文第 4 节追加新坑。

### 策略 B：新建文章
**条件**：新周期或全新问题簇。
按七段式模板撰写，建立双向引用。

### 策略 C：仅更新状态
**条件**：无新内容但状态变化。
只更新 `status`、`last_session_date`、`updated`。

---

## 五、输出格式

```
## 对话蒸馏器 执行报告 · YYYY-MM-DD

### 扫描结果
- 新增会话：N 个 · 有实质内容：M 个 · 跳过：K 个

### 更新操作
| 文章 | 操作 | 变更说明 |
|------|------|---------|

### 未通过质量闸门
（如有）
```

---

## 六、反模式

| 反模式 | 正确做法 |
|--------|----------|
| 每条对话新建一篇 | 同一 problem_cycle 合并 |
| 只改 frontmatter 不改正文 | 正文同步追加 |
| 跳过交叉引用 | 必建双向链接 |
| 不更新索引 | 每次入库更新 |
| 省略失败尝试 | "试 A→B→C 解决" > "用 C 解决" |
| 模糊时间 | 具体日期，禁"之前""后来" |
| 文章不自包含 | 禁"详见原始对话" |

---

## 适配器示范代码

> **以下范例仅供 Agent 学习适配器的输入输出格式。**
> Agent 加载本 Skill 后，必须自行探索当前平台的对话存储方式，编写自己的适配器。
> 不要直接复制以下代码——你的平台存储格式大概率与此不同。

### 范例 1：Reasonix (PortaKit)
> 这个 Agent 的对话存在 `.jsonl` 文件里，元数据在 `.meta.json` 里。**你的平台可能完全不同。**

```python
import os, json, glob, re

def reasonix_adapter(sessions_dir, last_date):
    """扫描 Reasonix 的 .jsonl + .meta.json 会话目录"""
    conversations = []
    for jsonl_file in sorted(glob.glob(os.path.join(sessions_dir, "*.jsonl"))):
        if "冲突" in jsonl_file or ".bak" in jsonl_file:
            continue
        fn = os.path.basename(jsonl_file)
        m = re.search(r"(\d{8})", fn)
        if not m: continue
        d = m.group(1)
        session_date = f"{d[:4]}-{d[4:6]}-{d[6:8]}"
        if session_date < last_date: continue
        messages = []
        with open(jsonl_file, "r", encoding="utf-8") as f:
            for line in f:
                if not line.strip(): continue
                try:
                    msg = json.loads(line)
                    messages.append({"role": msg.get("role",""), "content": msg.get("content","")})
                except: continue
        summary = ""
        meta_file = jsonl_file.replace(".jsonl", ".meta.json")
        if os.path.exists(meta_file):
            try:
                with open(meta_file, "r", encoding="utf-8") as f:
                    meta = json.load(f)
                summary = meta.get("summary", meta.get("title", ""))
            except: pass
        conversations.append({"id": fn.replace(".jsonl",""), "date": session_date, "summary": summary, "messages": messages})
    return conversations
```

### 范例 2：ChatGPT
> 这个 Agent 的对话是一个 `conversations.json` 文件。**你的平台可能完全不同。**

```python
import json

def chatgpt_adapter(export_path, last_date):
    """解析 ChatGPT 导出的 conversations.json"""
    with open(export_path, "r", encoding="utf-8") as f:
        data = json.load(f)
    conversations = []
    for conv in data:
        title = conv.get("title", "")
        create_time = conv.get("create_time", 0)
        from datetime import datetime
        date_str = datetime.fromtimestamp(create_time).strftime("%Y-%m-%d")
        if date_str < last_date: continue
        messages = []
        for node_id, node in conv.get("mapping", {}).items():
            msg = node.get("message")
            if msg:
                role = msg.get("author", {}).get("role", "")
                content = "".join(p for p in msg.get("content", {}).get("parts", []) if isinstance(p, str))
                messages.append({"role": role, "content": content})
        conversations.append({"id": conv.get("id",""), "date": date_str, "summary": title, "messages": messages})
    return conversations
```

### Agent 编写适配器的步骤

1. **探索**：列出自己能访问的目录，搜索对话相关文件
2. **抽样**：读 1-2 个文件，理解格式（JSON？JSONL？Markdown？数据库？）
3. **编写**：参考上面范例的**函数签名和输出格式**，写一个适配器
4. **验证**：跑一次，检查输出是否包含 `id, date, summary, messages`
5. **可选**：将适配器追加到此区块末尾，供后来的 Agent 参考