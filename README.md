# Conversation Distiller

> 对话会蒸发，知识该留下：自动把每场AI对话，蒸馏成结构化的永久知识。
> Conversation Distiller 把这些易碎的知识蒸馏成**永久、互联的知识文章**，让你的下一次会话从上次的结束的地方开始。

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)]()
[![Reasonix](https://img.shields.io/badge/Reasonix-Ecosystem-58a6ff)](https://github.com/CS-Faith/reasonix-ecosystem)

---

## 一句话定位

| 输入 | 输出 |
|------|------|
| 你的 AI 对话日志（JSONL / JSON / Markdown） | 结构化知识文章：问题、决策、陷阱、依赖、行动、完成 |

---

## 快速开始

```bash
git clone https://github.com/CS-Faith/conversation-distiller.git
cd conversation-distiller
pip install -r requirements.txt
python distill.py --input ~/.reasonix/sessions/ --output ./wiki/
```

---

## 真实数据（基于 810 场 AI 会话实测）

| 指标 | 数值 |
|------|------|
| 已处理原始会话 | 810 个 |
| 蒸馏出知识文章 | 16 篇 |
| 覆盖问题域 | 11 个集群 |
| 已记录陷阱条目 | 142 条 |

---

## 效率对比

| 方式 | 耗时 | 产出 |
|------|------|------|
| 手动整理一篇对话 | ~45 分钟 | 零散笔记 |
| **Distiller 自动蒸馏** | **~2 分钟** | 结构化文章 + 双向 Wikilinks |

---

## 工作流

```
对话日志 → 适配器 → 蒸馏引擎 → 结构化知识文章
                                  ↓
                          自动识别平台格式
                          7 段式模板
                          10 道质量门
                          双向链接网络
```

### 1. 自适应输入
首次加载时，Agent 自动发现会话存储格式（JSONL、JSON、Markdown、数据库…），无需手动配置。

### 2. 蒸馏引擎
- 将新对话匹配到已有问题集群
- 更新已有文章或创建新文章
- 应用 **7 段式模板**：问题 → 状态 → 决策 → 陷阱 → 依赖 → 行动 → 完成

### 3. 质量门
每篇文章发布前通过 **10 道强制检查**——包括陷阱结构（症状→原因→修复）、可操作下一步、双向交叉引用。

---

## Next Step

你的知识文章积累多了会重复冲突 → [**knowledge-cleanup**](https://github.com/CS-Faith/knowledge-cleanup) 五轮递进去重清理

想让多篇文章参与多视角讨论 → [**conversation-council**](https://github.com/CS-Faith/conversation-council) 历史会话议员团回答新问题

换设备后所有会话消失 → [**Portakit**](https://github.com/CS-Faith/reasonix-portakit) 让数据跟随 U 盘走

---

## License
MIT © 2026 [CS-Faith](https://cs-faith.github.io)

<details>
<summary>English Version</summary>

**Conversation Distiller** — Conversations evaporate. Knowledge shouldn't. Distiller turns every AI session into structured, lasting knowledge — automatically.

Every AI agent accumulates hundreds of conversations. Most of that knowledge is lost the moment a session ends. Distiller makes your next session pick up exactly where the last one left off.

### Key Features
- **Self-Adapting Input**: Auto-discovers conversation storage format on first load
- **Distillation Engine**: Matches conversations to problem clusters, applies 7-section template
- **Quality Gates**: 10 mandatory checks before publishing
- **Bidirectional Wikilinks**: Articles form a reference network

### Real-world Results (810 sessions)
- 810 raw sessions → 16 knowledge articles
- 11 problem clusters covered
- 142 trap entries documented

### Efficiency
- Manual distillation: ~45 min per session → scattered notes
- Distiller auto: ~2 min per session → structured wiki article + cross-references
</details>