# Conversation Distiller 🧪

> **Turn AI conversations into structured, reusable knowledge — automatically.**

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-AI%20Agent%20Skill-blue)]()
[![Status](https://img.shields.io/badge/Status-Active-brightgreen)]()

[中文文档](README_CN.md) | [Skill File](SKILL.md)

---

## What is it?

Every AI agent accumulates hundreds of conversations. Most of that knowledge is lost the moment a session ends. **Conversation Distiller** captures the decisions, traps, and insights from your conversations and distills them into permanent, interlinked knowledge articles — so your next session picks up exactly where the last one left off.

## Why?

| Without Distiller | With Distiller |
|-------------------|----------------|
| ❌ Context window fills up — start from zero | ✅ New session reads wiki, knows everything |
| ❌ Same mistakes repeated across sessions | ✅ Every trap documented: symptom → cause → fix |
| ❌ "What did we decide last time?" | ✅ Every decision preserved with its rationale |
| ❌ Scattered knowledge across 800+ sessions | ✅ 16 articles cover 11 problem clusters |
| ❌ New agents start blind | ✅ Articles form a bidirectional reference network |

## How it works

```
Conversation Logs → Adapter → Distiller Engine → Structured Knowledge Articles
                      ↑                              ↓
              Auto-detected per platform    7-Section Template
                                           10 Quality Gates
                                           Bidirectional Wikilinks
```

### 1. Self-Adapting Input
On first load, the agent **auto-discovers** how conversations are stored on its platform (JSONL, JSON, Markdown, database…) and writes its own adapter. No manual configuration.

### 2. Distillation Engine
- Matches new conversations to existing problem clusters
- Updates existing articles or creates new ones
- Applies the **7-Section Template** (Problem → Status → Decisions → Traps → Dependencies → Actions → Completion)

### 3. Quality Gates
Every article passes **10 mandatory checks** before publishing — including trap structure (symptom → cause → fix), actionable next steps, and bidirectional cross-references.

## Real-world results (from 810 sessions)

| Metric | Value |
|--------|-------|
| Conversations processed | 810 |
| Knowledge articles produced | 17 |
| Total words | ~58,000 |
| Problem clusters covered | 12 |
| Quality gate pass rate | 100% |
| Cross-references per article | 3-14 wikilinks |

## Quick Start

1. **Install** the `SKILL.md` file to your AI agent's skills directory
2. **Say** "对话入库" or "distill" or "turn conversations into wiki"
3. The agent auto-discovers your conversation format, writes an adapter, and starts distilling

## Supported Platforms

The distillation engine is **platform-agnostic**. Adapter examples are included for:
- **Reasonix / PortaKit** (`.jsonl` + `.meta.json`)
- **ChatGPT** (`conversations.json` export)

For any other platform, the agent auto-writes its own adapter on first run.

## Article Template

Every distilled article follows this structure:

```yaml
frontmatter:
  status, key_decisions, known_traps, related_articles, completion_criteria

body:
  1. What problem does this solve?     (≤3 sentences, "if you need X…")
  2. Current state                      (milestones + percentage)
  3. Key decisions & rationale          (why, not just what)
  4. Traps encountered                  (symptom → cause → fix)
  5. Interfaces & dependencies
  6. Next actions                       (atomic, executable)
  7. Completion criteria
```

## Contributing

Found a new platform? The agent can append its adapter to the examples section at the bottom of `SKILL.md`. PRs welcome for platform adapters, quality gate improvements, and template refinements.

## License

MIT — free to use, modify, and distribute. [LICENSE](LICENSE)

---

> *"The bottleneck is no longer what you can build, but what you choose to build."* — inspired by Anthropic's Founder's Playbook
