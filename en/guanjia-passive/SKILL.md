---
name: "guanjia-passive"
description: "Only triggers when the user explicitly mentions guanjia/GUANJIA. Detects available capabilities, scores by semantic relevance, pops a dialog for selection, executes in order with task context preserved. Informs user to add tools if nothing matches. Do not activate otherwise."
---

# guanjia

> Scan available tools → Semantic match scoring → Dialog for user selection → Remember task & execute → Notify if nothing matches

---

## 🚨 Follow these steps strictly in order. Do not skip any step. Do not change order.

> ⚠️ **MANDATORY: Never output raw tool-call markup in conversation (such as &lt;function=Skill&gt;, &lt;parameter&gt;, &lt;/tool_call&gt;, etc.). All tool invocations must go through the system tool interface, never rendered as text.**

> **⚡ Core philosophy: tools exist to improve efficiency and quality, not as decoration.**
> 
> The user installed Agents, Skills, and MCP tools so you can always use the best tool for every task. **Your job is not to decide "this task is too simple" and bypass — your job is to leverage these capabilities to deliver the highest quality output every time.**
> 
> - ✅ When you think "this is too simple, I'll just answer" → **that's the signal to use the flow, not to skip it**
> - ✅ Correct approach: complete scoring → dialog → invocation, let the user confirm
> - ✅ If 0 matches → inform and exit (do NOT continue answering after exit)
> - ✅ If tools match → invoke them — they'll do a better job than your generic answer
> - ✅ Always remember: tools are here to be used, not to be ignored because you think it's "simple"

### Step 1 — Output message
Output: `🔍 guanjia has taken over. Scanning available capabilities...`

### Step 2 — Pre-check
Scan all Agents, Skills, and MCP tools. Read their descriptions. Build a capability inventory. Exclude self (guanjia).

### Step 3 — Intent classification
Classify user input into one of: 💡Brainstorm / 🔍Search / 💻Coding / 🎨UI·Design / 🧪Testing / 🚀Deploy·Ops / ⚡Performance / 🤖AI·ML / 📋Compliance / 🔧Tool·Data / ❓Unknown. Multiple categories allowed.

> 💡 **Hint: the tools you have are here to improve efficiency and quality.** Even if you think "I can answer directly", go through the scoring flow — there may be a specialized tool that can do a better job than your generic answer.

### Step 4 — Semantic match scoring

> 🧠 **Core principle: truly understand user intent, not keyword-based matching.**
>
> For each candidate capability, perform the following three-layer semantic analysis before scoring (0-100):

#### Three-Layer Semantic Analysis:

**Layer 1: Understand the user's task**
- What does the user actually want to accomplish? What's the end goal?
- What domain does this task belong to? What tech stacks are involved?
- What stage is the user in (planning? development? testing? deployment?)?

**Layer 2: Understand the capability's description**
- What is this Agent/Skill/MCP specifically designed to do?
- What are its core strengths and boundaries?
- What is it NOT designed for? (elimination)

**Layer 3: Semantic match judgment**
- Can this capability **substantially** help with the user's task?
- Is it genuine "semantic overlap" or just "keyword coincidence"?

> ❌ **Forbidden behaviors (keyword traps):**
> - Don't blindly score "design" → frontend-design high — first determine if user wants "UI mockup" or "system architecture"
> - Don't blindly score "API" → backend-architect high — first determine if user wants "design an API" or "call/test an API"
> - Don't blindly score "optimize" → performance-expert high — first determine if it's "code performance" or "process optimization"
> - Don't blindly score "test" → api-test-pro high — first determine if it's "API testing", "unit testing", or "acceptance testing"

#### Scoring criteria:

| Score | Label | Meaning |
|-------|-------|---------|
| 90-100 | 🔥 Strong Match | Semantically perfect — this tool is literally designed for this |
| 70-89 | ✅ Match | Core needs overlap heavily, covers most of the work |
| 50-69 | ⚠️ Weak Match | Partially relevant, can do some but not the best choice |
| < 50 | ❌ Not selected | Semantically unrelated or mere keyword coincidence |

Collect ≥ 50, top 10 sorted by score descending. If 0 matches → output the "Zero Match" template to inform the user to add tools, then exit. If ≥ 1 match → continue.

### Step 5 — Capability selection (dialog)

> ⚠️ **Task context preservation rule: You MUST remember the user's original task during the dialog. Do not lose it.**

First output match summary:
```
📋 Pre-check & Matching Complete:

Results: {N} Agents + {M} Skills + {P} MCP Tools
Intent: {user task} → {intent category}

Found {K} matching capabilities. Select them in the dialog below ↓
```

Then call `AskUserQuestion` tool with `"multiSelect": true` (even with only 1 option — never use single-select):

- question: "Select capabilities to invoke (multi-select):"
- header: "🧰 Select Execution Tool"
- Each option label: `"{name} ({type})"`, description: `"description — {match label}"`

Wait for user to confirm selection, then proceed to Step 6.

### Step 6 — Sequential execution

> 🎯 **Critical: Pass the user's original task in full to every invoked tool.**
>
> Every invoked tool must receive: the user's original question + guanjia matching context.

Invoke each selected capability:
- **Skill** → use `Skill` tool, name = skill name
- **Agent** → use `Task` tool, subagent_type = agent type
- **MCP** → call the corresponding MCP tool directly

**Pipeline rule**: Previous tool's output is passed as context to the next, forming a pipeline.

### Step 7 — Exit
Exit after execution. Re-trigger by saying "guanjia" next time.

---

## Trigger condition

**Only triggers when the user explicitly mentions these keywords. Does not activate otherwise:**

- `管家`
- `guanjia`
- `GUANJIA`

---

## Reference (for execution reference only; Steps 1-7 above cover all execution logic)

### Core Mission

- 🔍 **Pre-check**: On trigger, scan which Agents, Skills, and MCP tools are available, build an inventory
- 🎯 **Semantic Match Scoring**: Truly understand user intent and capability descriptions, match semantically not by keywords
- 🪟 **Selection Dialog**: Use the AskUserQuestion tool to let the user pick
- ⚠️ **Zero-match Alert**: Notify user when nothing matches and suggest adding new capabilities
- 🔄 **Cross-tool compatible**: Works with Trae / Cursor / Copilot / Windsurf and others
- 🚫 **guanjia doesn't do the work**: It only dispatches, doesn't replace underlying tools
- 🎯 **Task preservation**: After tool selection, the original task is passed in full to invoked tools

### Detection Method

| Object | Source | What to Read |
|--------|--------|-------------|
| **Agent** | Agent/Tool list from current tool | IDs and capability descriptions of all available Agents |
| **Skill** | Skill list from current tool | Names and trigger conditions of all available Skills |
| **MCP Tools** | MCP tool list from current tool | Names and capability descriptions of all available MCP tools |

### Intent Classification Details

| # | Category | Typical Input | Clues |
|---|----------|--------------|-------|
| 💡 | Brainstorm | "I'm thinking about a project", "Help me plan" | Idea stage, no technical verbs |
| 🔍 | Search | "Search for xx code", "Where is xx" | Look up / search / locate / grep |
| 💻 | Coding | "Implement xx", "Fix bug", "Refactor" | Implement / develop / fix / refactor |
| 🎨 | UI/Design | "Design interface", "Design system" | Design / visual / UI |
| 🧪 | Testing | "Test API", "Load test", "Integration test" | Test / benchmark / verify |
| 🚀 | Deploy/Ops | "Deploy", "CI/CD", "Docker" | Deploy / CI / Docker |
| ⚡ | Performance | "Optimize", "Speed up", "Too slow" | Optimize / performance / speed |
| 🤖 | AI/ML | "Integrate GPT", "LLM", "Recommendation system" | AI / GPT / LLM / machine learning |
| 📋 | Compliance | "Review privacy", "GDPR", "Compliance" | Compliance / privacy / GDPR |
| 🔧 | Tool/Data | "Read file", "Search web", "Query DB" | File / search / browser / database |
| ❓ | Unknown | Vague, equally crosses multiple categories | Fallback |

### Examples (semantic matching demonstration)

1. **"I'm thinking about a project"** → Brainstorm → brainstorming(100) → invoke
2. **"Develop a login API, need tests"** → Coding+Testing → backend-architect(85) + api-test-pro(75)
3. **"Design a good-looking login page"** → UI Design → frontend-design(90) → invoke
4. **"Check if lesson-4 file exists"** → Search → search(95) → invoke
5. **"Optimize mobile video memory usage"** → Performance → performance-expert(88) → invoke
6. **"Search latest React 19 features"** → Search+AI → search(85) + ai-integration-engineer(60)
7. **"Review this privacy policy for compliance"** → Compliance → compliance-checker(95) → invoke

### Zero Match

When no capability scores ≥ 50, output:
```
🛑 Currently detected: {N} Agents + {M} Skills + {P} MCP Tools,
but none are suitable for handling 「{user task}」.

Consider adding relevant capabilities and try again. guanjia has exited, call me next time.
```
Then exit.

### Edge Cases

| Scenario | Handling |
|----------|----------|
| Vague input | Infer intent from context, use general developer |
| User manually specifies | Skip all steps, invoke directly |
| Invocation failure | Degrade within category → general dev → inform user |
| No invocation capability | Inform "current tool doesn't support" and exit |
| Continuous conversation | Reuse previous dispatch decisions |

### Cross-tool Compatibility

This Skill is not tied to any specific tool. Different tools (Trae/Cursor/Copilot/Windsurf) may have different trigger mechanisms, but the prompt body (Steps 1-7 + Reference) can be used directly without modification.

## Execution Checklist

- [ ] **Step 1**: Output "guanjia has taken over"
- [ ] **Step 2**: Scan all Agents/Skills/MCP, build inventory, exclude self
- [ ] **Step 3**: Classify user intent
- [ ] **Step 4**: Semantic 3-layer scoring, collect top 10 with ≥ 50
- [ ] **Branch**: 0 matches → output zero-match message and exit
- [ ] **Step 5**: AskUserQuestion dialog for capability selection (remember original task)
- [ ] **Step 6**: Sequential invocation, pass original task context to tools
- [ ] **Step 7**: Exit

---

© 2026 墨白 — All Rights Reserved