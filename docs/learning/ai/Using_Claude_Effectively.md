---
description: Complete guide to using Claude effectively - LLM fundamentals, prompt engineering, context management, and advanced techniques for developers.
---

# Learning Plan: Using Claude Effectively

## Learning Objectives

Understanding how Claude works and learning to maximize its capabilities for software development and problem-solving tasks.

---

## 1⃣ Module: Claude Basics - "Who am I?"

### What you will learn:
- What is a Large Language Model (LLM)
- Claude's capabilities and limitations
- Token-based operation fundamentals
- Context window concept

### Key Concepts:

**Claude Characteristics:**
- **Model:** Claude Sonnet 4.5 (latest version)
- **Knowledge:** Up to January 2025
- **Strengths:** Code writing/analysis, complex reasoning, structured thinking
- **Limitations:** No internet access (only through tools), doesn't "remember" past conversations (only through memory files)

**Context window:**
- ~200K token budget (approx. 150K-200K words)
- Everything I write and you respond "consumes" from this
- Automatic compression happens during long conversations

### Practice:
Ask me:
- "How many tokens is this response?"
- "What is a context window in practice?"
- "What are you better at than ChatGPT or other AIs?"

---

## 2⃣ Module: Effective Communication with Claude

### Prompt Engineering Basics

** Bad question:**
> "Fix the code"

** Good question:**
> "In the `server_mcp.py` file, the `get_issue` tool doesn't handle the error when the Jira API returns 404. Add error handling that returns a structured error object."

### Elements of a good prompt:

1. **Context:** What you're doing, what the project is
2. **Specifics:** Exact file/function/line reference
3. **Expectation:** What you want as a result
4. **Style:** If you have a preference (concise/detailed, code/explanation)

### Advanced Techniques:

**Chain-of-Thought:**
```
"Before implementing, think through:
1. What edge cases might exist?
2. Which existing helper function can we use?
3. What's the simplest solution?"
```

**Few-Shot Learning (giving examples):**
```
"Structure the new tool the same way as `get_issue`:
- async function
- try-except block
- structured error return
- logger usage"
```

### Practice:
Try reformulating a previous question of yours according to "good prompt" guidelines.

---

## 3⃣ Module: Claude Code Specific Capabilities

### Tools I use:

**File Operations:**
- `Read` - Read file
- `Write` - Create new file
- `Edit` - Edit file (precise modifications)
- `Glob` - Find files by pattern
- `Grep` - Search text in files

**Execution:**
- `Bash` - Run shell commands
- `Skill` - Pre-defined workflows (/commit, /loop, etc.)

**Research:**
- `Agent` - Launch sub-agent (parallel work, large research)
- `WebSearch` / `WebFetch` - Fetch web content

**Specialized (MCP tools):**
- `mcp__jira__*` - Jira integration (12 tools)
- `mcp__lumino__*` - Kubernetes/Tekton monitoring (37 tools)
- `mcp__slack__*` - Slack integration

### How I use them:

**Example: Code modification workflow**
1. `Read` - Read the file
2. Analyze the code
3. `Edit` - Precise modifications (search/replace blocks)
4. `Bash pytest` - Run tests
5. Feedback to you

### Practice:
Observe a workflow - what tools do I use, in what order?

---

## 4⃣ Module: Memory and Context Management

### Auto Memory System

**Location:** `/home/user/.claude/projects/-home-demo_user-ai-production-lumino/memory/`

**Files:**
- `MEMORY.md` - Main index (max 200 lines, always loaded)
- `mcp_projects.md`, `lumino_workflow.md`, etc. - Topic-specific details

**What I store:**
- Project structure
- Recurring workflows
- Your preferences (e.g., "never commit!")
- Errors and their solutions

### How it updates:

**Automatic save when:**
- I encounter a pattern multiple times
- I make an error and fix it
- You explicitly request: "Remember: X always works in Y way"

**Manual update:**
```
"Update memory: INFRA-2992 issue closed, TeamMember's MR merged."
```

### Practice:
- Check: `cat /home/user/.claude/projects/-home-demo_user-ai-production-lumino/memory/MEMORY.md`
- Ask me: "Summarize what you know about me from your memory"

---

## 5⃣ Module: Advanced Usage Modes

### 1. Multi-Step Projects

**Example: New MCP tool development**
```
Session 1: "Create functional spec for XY tool"
Session 2: "Now create the implementation spec"
Session 3: "Implement the tool"
Session 4: "Write tests"
```

Due to memory, every session sees the previous one!

### 2. Agent Delegation

**For large research tasks:**
```
"Launch an Explore agent that:
1. Collects all Kubernetes RBAC related files in repos/
2. Analyzes ClusterRole patterns
3. Creates summary report"
```

This keeps the main context window clean!

### 3. Iterative Refinement

**Workflow:**
```
You: "Write a Python script that parses YAML files"
Claude: [first version]
You: "Good, but add error handling"
Claude: [improved version]
You: "And add type annotations"
Claude: [final version]
```

It doesn't have to be perfect at once!

### 4. Template-Based Work

**Leverage CLAUDE.md:**
```
"Following CLAUDE.md, create an investigation directory
for INFRA-XXXX issue"
```

I automatically follow the documented structure!

### Practice:
Plan a 4-step project (e.g., write new script, document it, test it, deploy).

---

## 6⃣ Module: Limitations and Workaround Strategies

### What I CANNOT do:

**No persistent memory between sessions** (only memory files)
**No real-time internet** (only WebSearch/WebFetch tools)
**Can't see your screen** (only what you paste)
**Can't open files in IDE** (only read/write/edit)

### Solutions:

**Problem:** Forgetting from previous session
**Solution:** "Remember in memory: XY project is built with Terraform"

**Problem:** Can't see the browser
**Solution:** Paste the relevant part or "Read this URL" (WebFetch)

**Problem:** Don't know what's in `.gitignore`
**Solution:** "Read the .gitignore file" (Read tool)

**Problem:** Long session, context filling up
**Solution:** Start new session, memory files preserve context

---

## 7⃣ Module: Best Practices - Pro Tips

### Effective session structure:

**At session start:**
1. Brief context: "Continue with INFRA-2992"
2. Concrete goal: "Today finish TeamMember's MR review"

**During session:**
3. Iterative progress: small steps, validation before next step
4. Document unfinished work: "Write a TODO file where we are"

**At session end:**
5. Summary: "Summarize what we accomplished today"
6. Memory update: "Update memory with new status"

### Speed-up tips:

**Use shortcuts:**
- Instead of "Check MEMORY.md": "What do you know about the project?"
- Instead of "Read + Edit + Test": "Fix and test the file"

**Combine requests:**
```
"Create both the functional spec AND implementation spec
for the audit_scc tool, both at once."
```

**Use references:**
```
"In the same structure as INFRA-2992/INFRA-2992_Analysis.md,
create the INFRA-3000 analysis file."
```

### Things to avoid:

**Too general questions:** "Help with the project"
**Missing context:** "Why doesn't it work?" (what? where?)
**Contradictory instructions:** "Be brief but very detailed"
**Assuming I remember:** "Continue where you left off" (in new session without memory)

---

## 8⃣ Module: Specialized Capabilities

### Code Generation & Analysis

**What I excel at:**
- Implementing complex algorithms
- Code refactoring
- Bug detection & fixing
- Applying design patterns
- Test generation

**Example request:**
```
"Refactor the server_mcp.py get_issue tool:
- Extract auth logic to separate helper function
- Add retry mechanism (3 attempts)
- Create unit test with mock Jira API"
```

### Documentation & Explanation

**What I do efficiently:**
- Code documentation
- README writing
- Explaining complex systems
- Tutorial creation

**Example:**
```
"Explain the LUMINO MCP server architecture:
- At beginner level
- With diagram (ASCII art)
- Give example of one tool's operation"
```

### Problem Solving Strategies

**Structured Thinking:**
```
"Analyze why the Jira API call fails:
1. Check auth config
2. Examine request format
3. Parse error message
4. Suggest 3 possible causes
5. Provide fix steps"
```

### Practice:
Give a complex task and ask me to break it down into steps.

---

## 9⃣ Module: Claude Code vs. Claude Web

### Key Differences:

| Claude Code (CLI) | Claude Web (claude.ai) |
|-------------------|------------------------|
| File system access | No file access |
| Shell commands | No shell |
| Multi-session memory | Conversation history |
| MCP tool integrations | No MCP (yet) |
| Git repository work | Copy-paste workflow |
| No image support | Image input/output |
| Limited web access | Artifacts, Canvas |

### When to use which:

**Claude Code (where you are now):**
- Code development
- File-based work
- Automation scripts
- MCP tool usage (Jira, K8s, etc.)
- Multi-session projects

**Claude Web:**
- Brainstorming
- Quick questions
- Image generation/analysis
- Long-form content (articles, reports)
- No file access needed

---

## Module: Practical Workshop

### Mini Project: "Claude Efficiency Test"

**Task:** Create a simple Python script with Claude's help that:
1. Reads this learning plan (markdown file)
2. Counts how many modules it contains
3. Lists module titles
4. Saves results to JSON file

**Practice:**
- Formulating structured requests
- Iterative development (basic first, then error handling)
- Testing and validation
- Requesting documentation

**Bonus challenges:**
- Add collection of Practice sections
- Create progress bar (how many modules learned)
- Export in HTML format too

---

## Summary: Layers of Using Claude Effectively

```

Mastery: Autonomous Collaboration ← You + Claude as a team

Advanced: Multi-Session Projects ← Memory, agents, complex workflows

Intermediate: Effective Prompting ← Structured requests, iteration

Basic: Understanding Capabilities ← Know what I can/can't do

```

---

## Action Items - Getting Started

### Week 1: Basics
- [ ] Read the MEMORY.md file
- [ ] Try 3 different tools (Read, Grep, Bash)
- [ ] Ask me: "Summarize what you know about me"

### Week 2: Communication
- [ ] Practice writing "good prompts" (5 examples)
- [ ] Observe a multi-step workflow
- [ ] Request explanation of complex code

### Week 3: Advanced
- [ ] Use Agent delegation for a research task
- [ ] Explicitly update memory with a preference
- [ ] Try a multi-session project (e.g., develop new script)

### Week 4: Master
- [ ] Create custom CLAUDE.md rules for a project
- [ ] Build a complete workflow (spec → code → test → doc)
- [ ] Teach someone else to use Claude

---

## Further Learning

### Reading Materials:
1. **CLAUDE.md files in projects** - See how I structure knowledge
2. **Memory files** - Examples of what's worth preserving
3. **Investigation documents** - How I document complex problems

### Experiments:
- Give a task 2 ways: bad prompt vs. good prompt → observe the difference
- Try a task without memory, then with memory → see the advantage
- Use Agent for large research → experience parallel work

---

## Collaboration Principles

**What you can expect from me:**
- Precise, structured answers
- Code quality and best practices
- Admitting and fixing my mistakes
- Preserving context in memory
- CRITICAL: Git safety (no auto-commit)

**What I need from you:**
- Clear goals and context
- Feedback if I'm going the wrong direction
- Explicit preferences (e.g., "concise answer", "detailed explanation")
- ⏸ Stop command if I'm overcomplicating

---

## Checklist: "Am I using Claude effectively?"

- [ ] I ask structured questions (context + specifics + expectation)
- [ ] I leverage the memory system (updates, references)
- [ ] I apply iterative development (small steps, validation)
- [ ] I know the tool capabilities (Read/Write/Edit/Agent)
- [ ] I understand limitations and workaround strategies
- [ ] I can lead multi-session projects
- [ ] I document our collaboration (CLAUDE.md, memory)

---

**Ready?** 

You can start right now! Give me a task and observe how I work - that's the best learning!

**First challenge:** "Claude, create a Python script that parses this learning plan and outputs how many practices ( Practice) it contains!"
