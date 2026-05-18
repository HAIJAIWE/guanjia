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

## Directory Structure

```
Skill/
├── zh/                          ← Chinese
│   ├── guanjia-auto/SKILL.md    ─ Auto mode (always on, stays resident)
│   └── guanjia-passive/SKILL.md ─ Passive mode ("guanjia" to trigger)
├── en/                          ← English
│   ├── guanjia-auto/SKILL.md
│   └── guanjia-passive/SKILL.md
├── README.md
└── README.en.md
```

## Versions

| Version | Trigger | Behavior |
|---------|---------|----------|
| **Auto** (`guanjia-auto`) | Intercepts every message | Detect→Match→Dialog→Dispatch, stays resident |
| **Passive** (`guanjia-passive`) | Say "guanjia" to activate | Detect→Match→Dialog→Dispatch, exits after |

Pick one. **Don't install both.**

> ⚠️ Auto mode activates on EVERY message. Passive mode activates only on "guanjia". If both are installed, saying "guanjia" triggers both simultaneously with conflicting instructions (one says stay, the other says exit), confusing the AI.

---

## Installation

Place the chosen folder in your tool's skills directory:

```
your-project/
└── .trae/skills/                          ← Trae CN
    ├── guanjia-auto/      ← or guanjia-passive
    │   └── SKILL.md
    └── ...

~/.workbuddy/skills/<version-name>/SKILL.md  ← WorkBuddy
~/.claude/skills/<version-name>/SKILL.md     ← opencode
```

---

## Usage

### Auto mode

Works out of the box. Just talk normally:

```
You: search the latest AI news         ← handled by guanjia
You: write a login page                 ← handled by guanjia
You: test the API                       ← handled by guanjia
```

Say `quit guanjia` or `release takeover` to exit.

> ⚠️ Auto mode intercepts every message. If you don't need full automation, use passive mode instead.

### Passive mode

Only triggers when called:

```
You: write a login page                 ← normal, no guanjia
You: guanjia, check what tools are available ← guanjia triggers
You: continue                           ← continues previous task
You: thanks                             ← normal, no guanjia
```

Exits after the task is done. Call it again when needed.

### Interrupt Recovery

If the connection drops and resumes:

- **Auto mode**: Detects incomplete tasks and continues directly, no re-dispatch
- **Passive mode**: Just say "guanjia" again to re-trigger

---

## How it works

1. **Pre-check** — Scan all Agents, Skills, MCP tools, build inventory (exclude self)
2. **Intent classification** — Categorize user request (coding/search/design/test etc.)
3. **Semantic match scoring** — Three-layer semantic analysis: understand the task → understand capability descriptions → judge semantic match. Eliminates keyword traps (e.g. "design" ≠ frontend-design, "API" ≠ backend-architect)
4. **Selection dialog** — Show top 10 in a multi-select dialog, remember the original task
5. **Sequential execution** — Call in user-selected order, pass original task context in full, chain output from one to the next

---

## License

© 2026 墨白 — All Rights Reserved
