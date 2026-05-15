# guanjia — Smart Dispatch Skill

> Got tons of Agents, Skills, and MCP tools but don't know when to use which? Let guanjia handle the dispatch.

## Why guanjia?

AI coding tools (Trae, Cursor, Claude Code, etc.) can have many skills installed: code search, backend dev, frontend design, API testing... When you ask for something, the AI often picks the wrong tool or misses the right one.

guanjia solves this:

1. You make a request → guanjia scans all available tools
2. Scores each tool against your task, ranks by relevance
3. Pops a multi-select dialog with the top 10 matches
4. Calls them in order, passing context from one to the next

No more guessing which tool to use.

---

## Versions

| Folder | Version | Behavior |
|--------|---------|----------|
| `guanjia-auto` | **Auto mode** | Activates automatically, handles every message |
| `guanjia-passive` | **Passive mode** | Only responds to "管家/guanjia/GUANJIA" |

Pick one version. Don't install both.

---

## Installation

Place the chosen folder in your tool's skills directory:

```
your-project/
└── .trae/skills/
    ├── guanjia-auto/      ← or passive version
    │   └── SKILL.md
    └── ...
```

For opencode: `~/.claude/skills/<version-name>/SKILL.md`.

---

## Usage

### Auto mode (guanjia-auto)

Works out of the box. Just talk normally — guanjia detects, matches, and dispatches automatically.

```
You: search the latest AI news         ← handled by guanjia
You: write a login page                 ← handled by guanjia
您：测试API ← 由管家处理
```

说`退出管家`或`解除接管`以退出。

⚠️**：自动模式会拦截每条消息，这可能会占用额外的计算资源。如果您不需要完全自动化，请改用被动模式。

### Passive mode (guanjia-passive)

仅在被调用时触发。正常对话流程不受影响。

```
您：编写登录页面 ← 正常，无管家

您：继续 ← 继续之前的任务
你：谢谢 ← 正常，无管家


任务完成后退出。需要时再调用。

---

## How it works

1. **步骤0**— 扫描所有客服人员、技能、MCP工具
2. **Stage 1** — Classify your intent
3. **Stage 2** — Score each candidate (fit + match + precision)
4. **Stage 3** — Show top 10 in a multi-select dialog
5. **阶段4**— 按顺序调用选定的工具，并传递上下文

---

## License

© 2026 墨白 — 保留所有权利

