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
You: test the API                       ← handled by guanjia
```

Say `quit guanjia` or `release takeover` to exit.

> ⚠️ **Note**: Auto mode intercepts every message, which may use extra compute. If you don't need full automation, use passive mode instead.

### Passive mode (guanjia-passive)

Only triggers when called. Normal conversation flows uninterrupted.

```
You: write a login page                 ← normal, no guanjia
You: 管家, check what tools are available  ← guanjia triggers
You: continue                           ← continues previous task
You: thanks                             ← normal, no guanjia
```

Exits after the task is done. Call it again when needed.

---

## How it works

1. **Step 0** — Scan all Agents, Skills, MCP tools
2. **Stage 1** — Classify your intent
3. **Stage 2** — Score each candidate (fit + match + precision)
4. **Stage 3** — Show top 10 in a multi-select dialog
5. **Stage 4** — Call selected tools in order, passing context

---

## License

© 2026 墨白 — All Rights Reserved
