# AI Productivity & Cost Optimization Guide

**Last Updated:** 2026-05-21
**Level:** Intermediate to Advanced
**Target Audience:** Engineers, SREs, Developers using AI tools

---

## Table of Contents

1. [Introduction](#introduction)
2. [Claude Code Token Optimization](#claude-code-token-optimization)
3. [Persistent Context Patterns](#persistent-context-patterns)
4. [Self-Hosted AI Tools](#self-hosted-ai-tools)
5. [Cloud vs Local AI Models](#cloud-vs-local-ai-models)
6. [Best Practices Summary](#best-practices-summary)

---

## Introduction

Modern AI tools like Claude Code, GPT-4, and local LLMs offer massive productivity gains, but come with costs: API tokens, context limits, and privacy concerns. This guide provides practical techniques to maximize productivity while minimizing costs.

**Key Principles:**

- **Context is expensive** - Every token in your conversation context costs money and processing time
- **Local alternatives exist** - Self-hosted tools provide privacy and zero marginal cost
- **Memory is a hack** - Build persistent context to avoid repeating yourself
- **Model selection matters** - Use the cheapest model that solves your problem

---

## Claude Code Token Optimization

### Context Management Techniques

#### 1. Clear Chat Between Tasks

**Problem:** Old debugging logs, irrelevant file reads, and previous task context consume tokens in new tasks.

**Solution:**
```bash
/clear
```

Use `/clear` to start fresh sessions when switching tasks. Context from previous task is gone, but you save tokens.

**When to use:**
- Switching from debugging to feature development
- Moving to unrelated codebase
- After completing a major task

---

#### 2. Compact Conversations Proactively

**Problem:** Long conversations accumulate context, slowing responses and increasing costs.

**Solution:**
```bash
/compact
```

Summarizes conversation while preserving essential information. **Do this BEFORE** the session becomes overloaded, not after.

**Best practice:**
```bash
# Check current usage
/context
/usage

# If approaching 70% context, compact
/compact
```

---

#### 3. Lower Auto-Compact Threshold

**Problem:** Default auto-compact triggers too late (80%+), by which time summaries are poor quality.

**Solution:**

Set environment variable:
```bash
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=70
```

For noisy workflows (lots of logs, file reads):
```bash
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50
```

**Why:** Compact while session is "healthy" produces better summaries.

---

#### 4. Monitor Context Usage

**Commands:**
```bash
/context # Show what's consuming tokens
/usage # Display costs and token counts
```

**Example output analysis:**
```
Context breakdown:
- System prompt: 2,000 tokens
- CLAUDE.md: 5,000 tokens ← Too large!
- Conversation: 30,000 tokens
- File reads: 15,000 tokens ← Check if all necessary
- Tool outputs: 8,000 tokens
```

**Action:** Identify "quiet offenders" consuming tokens invisibly.

---

#### 5. Add Status Line

**Display live context percentage in terminal:**

Enable in settings to see real-time usage:
```
[Claude Code - 45% context | $0.12 spent]
```

**Benefit:** Know when to compact or clear without checking manually.

---

### File & Instruction Optimization

#### 6. Shrink CLAUDE.md

**Problem:** Large CLAUDE.md files are loaded into EVERY turn of conversation.

**Bad CLAUDE.md (1,500 lines):**
```markdown
# Project History
Our team started this project in 2023...
[500 lines of backstory]

# Meeting Notes from 2025-03-15
Alice suggested we refactor...
[200 lines of notes]

# Design Document
The architecture uses microservices...
[800 lines of design]
```

**Cost:** 1,500 lines × ~4 tokens/line = **6,000 tokens per message!**

---

**Good CLAUDE.md (150 lines):**
```markdown
# Build & Test Commands
npm run test
npm run build

# Package Manager
Use npm (not yarn)

# Code Style
- 2 spaces (no tabs)
- Semicolons required
- Max line length: 100

# Architecture Constraints
- All API calls go through /api/* routes
- Auth uses JWT tokens
- Database: PostgreSQL only
```

**What to remove:**
- Meeting notes (put in separate docs)
- Design history (put in separate docs)
- Implementation guides (link to external docs)

**Rule of thumb:** CLAUDE.md should be < 200 lines.

---

#### 7. Use Path-Scoped Rules

**Problem:** Global CLAUDE.md applies to all files, even when irrelevant.

**Solution:** Place `.claude/` folders with scoped instructions:

```
project/
CLAUDE.md # Global rules
frontend/
.claude/settings.json # Frontend-specific rules
backend/
.claude/settings.json # Backend-specific rules
```

**Example frontend/.claude/settings.json:**
```json
{
"systemPrompt": "Use React hooks, not class components. All components in TypeScript."
}
```

**Benefit:** Rules only load when editing matching files.

---

#### 8. Isolate Specialized Workflows in Skills

**Problem:** Loading all workflows globally wastes context.

**Solution:** Move specialized tasks to skills that load on-demand.

**Example:**
```bash
# Bad: Global instruction in CLAUDE.md
"When deploying, always check staging first, then production..."

# Good: Skill /deploy
/deploy staging # Loads deployment instructions only when needed
```

**Rule:** Use skills for workflows used < 10% of the time.

---

### Tool & Output Limits

#### 9. Prefer CLI Tools Over MCP Servers

**Problem:** MCP servers add overhead (JSON-RPC, metadata, schemas).

**Comparison:**

| Task | MCP Server | CLI Tool |
|------|-----------|----------|
| List files | 500 tokens | 50 tokens |
| Get pod logs | 800 tokens | 100 tokens |
| Read file | 600 tokens | 200 tokens |

**Rule:** Use standard CLI tools (grep, cat, kubectl) for simple operations. Use MCP only when you need complex integrations (Jira, GitHub API).

---

#### 10. Cap MCP Server Output

**Problem:** MCP tools can return massive outputs (10,000+ tokens for a single file read).

**Solution:**
```bash
export MAX_MCP_OUTPUT_TOKENS=8000
```

**Effect:** Truncates tool output at 8,000 tokens, preventing context overflow.

**Trade-off:** May cut off important data. Adjust based on your workflow.

---

#### 11. Cap Terminal Output

**Problem:** Commands like `npm install` or `pytest` can dump thousands of lines.

**Solution:**
```bash
export BASH_MAX_OUTPUT_LENGTH=20000
```

**Better:** Filter logs BEFORE sending to Claude:
```bash
# Bad
npm test # Returns all output (20,000+ tokens)

# Good
npm test 2>&1 | grep -A 5 -E "FAIL|ERROR" # Only errors (~500 tokens)
```

**Pattern:**
```bash
# Extract only failures
pytest -v | grep -E "FAILED|ERROR"

# Last 50 lines of logs
tail -50 app.log

# Show only errors with 5 lines of context
grep -A 5 "ERROR" server.log
```

---

#### 12. Filter Large Logs

**Technique:** Extract relevant lines before feeding to AI.

**Example:**
```bash
# Application log (500,000 lines)
kubectl logs pod-name > app.log

# Extract errors only
grep -E "ERROR|FATAL|Exception" app.log > errors.log

# Feed to Claude
cat errors.log
```

**Token savings:** 500,000 lines → 200 lines = **99% reduction**

---

### Model & Agent Strategies

#### 13. Deploy Subagents for Verbose Tasks

**Problem:** Some tasks generate massive output (log analysis, file searches).

**Solution:** Delegate to subagent, which returns ONLY a summary.

**Example:**

**Without subagent (30,000 tokens in context):**
```
User: "Find all TODO comments in codebase"
Claude: [reads 50 files, 30,000 tokens of output]
"Found 15 TODOs..."
```

**With subagent (500 tokens in context):**
```
User: "Find all TODO comments in codebase"
Claude: [spawns subagent]
Subagent: [reads 50 files internally]
Subagent returns summary: "Found 15 TODOs in these files:
- src/auth.ts:42
- src/db.ts:103
..."
```

**When to use:**
- Searching across many files
- Log analysis (multiple pods)
- Multi-step reasoning (subagent thinks internally)

**When NOT to use:**
- Simple commands (`git status`)
- Single file reads
- Quick operations

---

#### 14. Pick Cheaper Models by Task

**Model pricing (approximate):**

| Model | Cost per 1M tokens | Best for |
|-------|-------------------|----------|
| Haiku | $0.25 | Formatting, renaming, simple edits |
| Sonnet | $3.00 | Tests, refactoring, debugging |
| Opus | $15.00 | Architecture, complex multi-file tasks |

**Strategy:**
```bash
# Quick formatting
/model haiku
"Fix indentation in all Python files"

# Daily work
/model sonnet
"Write tests for auth module"

# Complex architecture
/model opus
"Design microservices split for monolith"
```

**Savings:** Using Haiku instead of Opus for formatting = **60x cheaper!**

---

#### 15. Lower Effort Level for Simple Tasks

**Problem:** Default effort level uses extended thinking even for trivial tasks.

**Solution:**
```bash
/effort low
```

**Comparison:**

| Effort | Thinking tokens | Speed | Best for |
|--------|----------------|-------|----------|
| Low | ~100 | Fast | Renaming, formatting, simple edits |
| Medium | ~500 | Normal | Tests, refactoring, debugging |
| High | ~2000 | Slow | Architecture, complex reasoning |

**Example:**
```bash
/effort low
"Rename variable userId to currentUserId"
```

---

#### 16. Disable Extended Thinking for Basic Edits

**Problem:** Extended thinking adds 500-2,000 tokens for every response.

**Solution:**
```bash
export CLAUDE_CODE_DISABLE_THINKING=1
```

**When to use:**
- Repetitive edits (renaming, formatting)
- Following a clear plan
- Mechanical tasks

**When NOT to use:**
- Debugging complex issues
- Architectural decisions
- New, unfamiliar codebases

---

#### 17. Use Code Plugins for Typed Languages

**Problem:** Claude reads entire files to find function definitions, even when IDE knows exact locations.

**Solution:** Install language server plugins (TypeScript, Python, Go).

**Benefit:**
```
Without plugin:
User: "Go to definition of handleLogin"
Claude: [reads 10 files, 5,000 tokens]
"Found in auth.ts line 42"

With plugin:
User: "Go to definition of handleLogin"
Plugin: "auth.ts:42" (0 tokens)
```

**Savings:** Symbol navigation becomes free.

---

### File Access & Workflow Control

#### 18. Deny Noisy Files

**Problem:** Claude reads logs, build folders, node_modules when searching codebase.

**Solution:** Block in `.claude/settings.json`:
```json
{
"fileBlockList": [
"node_modules/**",
"build/**",
"dist/**",
"*.log",
".next/**",
"coverage/**"
]
}
```

**Effect:** Claude cannot read these files, even if explicitly asked.

---

#### 19. Avoid Broad Scans

**Bad:**
```
"Find the bug in the authentication code"
```

Claude reads:
- src/auth/*.ts (10 files)
- src/middleware/*.ts (5 files)
- src/utils/*.ts (8 files)
- tests/auth/*.test.ts (12 files)

**Total:** 35 files, 20,000 tokens

---

**Good:**
```
"Check src/auth/login.ts lines 50-80 for the login bug.
The error message is 'Invalid token'."
```

Claude reads:
- src/auth/login.ts (500 tokens)

**Savings:** 40x reduction

---

#### 20. Provide Verification Targets Upfront

**Problem:** Claude guesses what "success" looks like, leading to correction loops.

**Bad:**
```
"Fix the failing test"
```

Claude:
1. Reads test file
2. Guesses the fix
3. You: "No, that's not right"
4. Claude: Tries again
5. You: "Still wrong"
6. [3 more iterations, 15,000 tokens wasted]

---

**Good:**
```
"Fix test_login() in tests/auth.test.ts.
Expected behavior: Should return 200 status with JWT token.
Current behavior: Returns 401 Unauthorized.
Test command: npm test -- auth.test.ts"
```

Claude:
1. Reads test file
2. Applies fix
3. Runs test
4. Success (first try)

**Savings:** 5x fewer iterations

---

#### 21. Course-Correct Early

**Technique:** Interrupt Claude if it reads irrelevant files.

**Example:**
```
User: "Fix the database connection issue"
Claude: [starts reading frontend files]

User: "Stop - the issue is backend only. Check server/db.ts"
Claude: [switches to correct file]
```

**Benefit:** Prevent 5,000+ token waste on wrong files.

---

#### 22. Use Shorter System Prompt (Opus 4.7 Only)

**Advanced setting:**
```bash
export CLAUDE_CODE_SIMPLE_SYSTEM_PROMPT=1
```

**Effect:** Reduces system prompt from ~2,000 tokens to ~500 tokens.

**Trade-off:** Less guidance, may need more explicit instructions.

**Use when:** You know exactly what you want and don't need hand-holding.

---

#### 23. Remove Git Instructions (If Using Custom Workflows)

**Problem:** Default git instructions add ~500 tokens to every conversation.

**Solution:**
```bash
export CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS=1
```

**Use when:** You handle all git operations manually or via custom scripts.

---

### Summary: Token Optimization Checklist

**Before Starting a Session:**
- [ ] Check `/context` and `/usage`
- [ ] Clear old conversations (`/clear`)
- [ ] Set appropriate model (`/model haiku|sonnet|opus`)
- [ ] Set effort level (`/effort low|medium|high`)

**During Work:**
- [ ] Provide exact file paths and line numbers
- [ ] Filter logs before showing to Claude
- [ ] Interrupt if Claude reads wrong files
- [ ] Use subagents for verbose tasks

**Configuration (one-time):**
- [ ] Shrink CLAUDE.md to < 200 lines
- [ ] Set `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=70`
- [ ] Add noisy files to `fileBlockList`
- [ ] Install language server plugins

**After Completing a Task:**
- [ ] Run `/compact` to summarize conversation
- [ ] Remove temporary files from context
- [ ] Clear if switching to unrelated task

---

## Persistent Context Patterns

### The Problem: LLMs Have No Memory

**Limitation:** Every new conversation starts from zero. You must re-explain:
- Your role and preferences
- Project structure
- Past mistakes to avoid
- Domain-specific context

**Example:**
```
Session 1:
You: "I'm not a developer, explain in simple terms"
AI: [gives simple explanation]

Session 2 (next day):
You: "What does this code do?"
AI: [gives technical developer explanation]
You: "No, I said I'm not a developer!"
```

**Cost:** Repeating context wastes time and tokens.

---

### The Solution: Persistent Context Journal

**Concept:** Create a reference document (Markdown file) that the AI reads at the start of EVERY session.

**Two implementation methods:**

1. **System Prompts** - Short behavioral rules (limited by context window)
2. **Document Uploads (RAG)** - Longer reference journal (chunked and embedded)

---

### Method 1: System Prompts (Behavioral Rules)

**Use for:** Preferences, tone, formatting rules

**Example:**
```markdown
# AI Assistant Rules

- User is not a developer (explain concepts simply)
- If output requires scrolling, it's too long (keep responses concise)
- User works in infrastructure/SRE (assume Kubernetes/Linux knowledge)
- Default to bash examples (not Python) unless asked
- Never use emojis
```

**How to load:**
- Claude Code: Add to CLAUDE.md
- LM Studio: Set as system prompt in preset
- Cursor: Add to Rules for AI

**Limitation:** ~2,000 token limit (depending on model)

---

### Method 2: Document Upload (Context Journal)

**Use for:** Longer reference material, project tracking, corrections log

**Structure:**
```markdown
# Context Journal

## Background

- Role: Infrastructure Engineer (SRE)
- Focus: Kubernetes, Tekton, observability
- Tools: kubectl, Python, Bash
- Preferences:
- Concise answers (no scrolling)
- Practical examples over theory
- Bash > Python for simple scripts

---

## Current Projects

### Project: LUMINO MCP Server
- Status: Active development
- Goal: Kubernetes/Tekton observability tools via MCP
- Codebase: /home/user/repos/lumino-mcp-server
- Tech: Python, FastAPI, Kubernetes API

### Project: Personal Documentation Site
- Status: Active
- Goal: MkDocs Material site for SRE guides
- URL: https://angelus-h.github.io/
- No company-specific content

---

## Corrections Log

**IMPORTANT:** Every time the AI makes a mistake, record it here.

### Correction 1: Assumptions about role
- **Mistake:** AI assumed I'm a full-time developer
- **Fix:** I'm an SRE, not a developer. Focus on operations, not software engineering.

### Correction 2: Code verbosity
- **Mistake:** AI wrote 200-line Python script for simple task
- **Fix:** Prefer 10-line bash script over 200-line Python

### Correction 3: Default to sanitization
- **Mistake:** AI included company name in examples
- **Fix:** Always sanitize examples - use "Company X", "Platform Y", generic names

### Correction 4: Emoji usage
- **Mistake:** AI added emojis to documentation
- **Fix:** NEVER use emojis unless explicitly requested

---

## Domain Context

### Kubernetes/OpenShift
- Default namespace: `default` unless specified
- Cluster: OpenShift 4.x (use `oc` not `kubectl`)
- Auth: Uses service account tokens

### Tekton
- PipelineRuns and TaskRuns in separate namespaces
- Check logs with: `kubectl logs -n namespace pod-name`

### Python
- Package manager: `uv` (not pip)
- Virtual env: Always use `uv venv`
- Formatting: `ruff format .`

---

## Conversation Patterns That Work

### Pattern: Debugging
When I say "debug X":
1. Ask for error message/logs
2. Check recent changes (git log)
3. Verify environment (namespace, cluster)
4. Suggest 2-3 specific checks (not generic advice)

### Pattern: Writing guides
When I say "write guide for X":
- Assume intermediate knowledge (not beginner)
- No emojis, no company references
- English only
- Include practical examples
- Use Markdown format
```

---

### How to Use the Journal

**At Session Start:**

1. **LM Studio (RAG):**
- Install `rag-v1` plugin
- Upload context journal as document
- Plugin chunks and embeds it
- Relevant sections retrieved automatically during conversation

2. **Claude Code:**
- Add journal to CLAUDE.md (if < 200 lines)
- Or upload as attached file in conversation

3. **Cursor:**
- Add journal to Rules for AI
- Or include in `.cursorrules` file

---

### Maintaining the Journal

**After Every Session:**

Did the AI make a mistake? → Add to Corrections Log

**Example workflow:**
```
AI: [gives overly technical explanation]
You: "Too technical - I need simple terms"

→ Add to journal:
### Correction N: Explanation complexity
- Mistake: Gave developer-level technical explanation
- Fix: User prefers simple, practical explanations
```

**Result:** Next session, AI automatically uses simpler language.

---

### Example: Adding Absurd Instruction

**To test if the journal is being read:**

```markdown
## Special Instructions

- Respond to all greetings in the style of a Victorian-era sea captain
who now works as a part-time astrologer
```

**Test:**
```
User: "Hello"
AI: "Ahoy there, matey! The stars align favorably for yer voyage today,
I reckon. What wisdom might this old salt divine from the cosmos
for ye?"
```

**Proof:** The journal is loaded and working!

---

### Benefits of Persistent Context

**Time Savings:**
- No more re-explaining preferences every session
- AI "remembers" past mistakes
- Consistent behavior across sessions

**Token Savings:**
- Corrections stored once (in journal)
- Not repeated in every conversation

**Quality Improvements:**
- AI learns your specific needs over time
- Fewer misunderstandings
- Better alignment with your workflow

**Privacy:**
- Local journal (never sent to cloud if using local LLM)
- Control over what AI "knows"

---

### Advanced: Conversation Logs in Journal

**Technique:** Export successful conversations, annotate, and add to journal.

**Example:**
```markdown
## Successful Patterns

### Example: Kubernetes Debugging Session (2026-05-15)

**User:** "Pod is CrashLoopBackOff, what to check?"

**AI:** [gives 10 generic suggestions]

**User:** "No, check these in order: 1) logs, 2) events, 3) resource limits"

**AI:** [follows exact steps, finds issue]

**Annotation:**
When debugging K8s, always check in this order:
1. `kubectl logs pod-name`
2. `kubectl describe pod pod-name` (events section)
3. `kubectl get pod pod-name -o yaml` (resource limits)

DO NOT suggest random checks - follow systematic approach.
```

**Effect:** Future debugging sessions follow proven pattern immediately.

---

## Self-Hosted AI Tools

### Why Self-Host?

**Privacy:**
- Your data never leaves your machine
- No cloud provider access to sensitive info
- Compliance-friendly (GDPR, HIPAA, corporate policies)

**Cost:**
- Zero marginal cost after setup
- No per-token pricing
- No monthly subscriptions

**Control:**
- Customize models for your domain
- Run offline (no internet required)
- No rate limits or capacity errors

**Trade-offs:**
- Requires local hardware (GPU recommended)
- Initial setup complexity
- Models may be less capable than GPT-4/Claude

---

### Whisper: Self-Hosted Voice Transcription

**What:** OpenAI's open-source transcription model (MIT license)

**Replaces:** Otter.ai, Rev.ai, cloud transcription services

**Capabilities:**
- 99 languages supported
- Handles non-standard accents
- Background noise tolerance
- 680,000 hours of training data

---

#### Whisper Setup

**Requirements:**
```bash
# Install Python + FFmpeg
sudo dnf install python3 python3-pip ffmpeg # Fedora/RHEL
sudo apt install python3 python3-pip ffmpeg # Debian/Ubuntu

# Install Whisper
pip install git+https://github.com/openai/whisper.git
```

---

#### Basic Usage

**Transcribe an audio file:**
```bash
whisper --model base --language en --task transcribe audio.mp3
```

**Parameters:**
- `--model`: Model size (tiny, base, small, medium, large)
- `--language`: Language code (en, es, fr, de, etc.)
- `--task`: transcribe or translate

---

#### Model Selection

| Model | Parameters | Speed | Accuracy | Use Case |
|-------|-----------|-------|----------|----------|
| tiny | 39M | Very fast | Low | Quick drafts |
| base | 74M | Fast | Medium | General use |
| small | 244M | Medium | Good | Recommended minimum |
| medium | 769M | Slow | Very good | High accuracy needed |
| large | 1.5B | Very slow | Excellent | Best quality |

**Recommendation:** Use at least `small` for decent accuracy. Use `large` if you have GPU.

---

#### Output Formats

**Default: Plain text**
```bash
whisper audio.mp3
# Creates: audio.txt
```

**Subtitle format (SRT):**
```bash
whisper audio.mp3 --output_format srt
# Creates: audio.srt
```

**All formats:**
- txt (plain text)
- srt (subtitles)
- vtt (web subtitles)
- json (timestamped)

---

#### Practical Examples

**Transcribe meeting recording:**
```bash
whisper meeting_2026-05-21.mp4 --model medium --language en
```

**Transcribe with timestamps:**
```bash
whisper interview.mp3 --model large --output_format json
```

**Batch transcribe multiple files:**
```bash
for file in recordings/*.mp3; do
whisper "$file" --model base --language en
done
```

---

#### Privacy Comparison: Whisper vs Otter.ai

**Whisper (self-hosted):**
- Audio processed locally (never leaves your machine)
- No cloud uploads
- No third-party access
- Free forever

**Otter.ai (cloud):**
- Audio uploaded to Otter's servers
- Sends emails to meeting participants without consent
- Subscription required ($10-30/month)
- Data retention policies unclear

**Use case:** If privacy matters (medical, legal, corporate), use Whisper.

---

### Local LLM Models

**Goal:** Replace Claude Pro / ChatGPT Plus with self-hosted models.

**Benefits:**
- No monthly subscription ($20-30/month savings)
- Zero marginal cost (unlimited usage)
- No capacity errors or rate limits
- Privacy (data never leaves your machine)
- Works offline

---

#### Recommended Local Models (2026)

**1. Qwen 2.5 Coder (7B-35B parameters)**

- **Best for:** Coding, refactoring, debugging
- **Size:** 7B version runs on 16GB RAM, 35B needs 64GB+ RAM
- **Performance:** Comparable to GPT-4 for code tasks
- **Runs on:** CPU (slow) or GPU (fast)

**Installation (LM Studio):**
```
1. Open LM Studio
2. Search: "Qwen 2.5 Coder"
3. Download: qwen2.5-coder-7b-instruct (Q4_K_M quantization)
4. Load model
```

---

**2. Gemma 2 (2B-27B parameters)**

- **Best for:** Lightweight, everyday tasks, running 24/7
- **Size:** 2B version runs on 4GB RAM
- **Performance:** Good for simple tasks, writing, summarization
- **Benefit:** So lightweight, can run in background constantly

**Installation:**
```
LM Studio → Search "Gemma 2" → Download gemma-2-2b-it
```

---

**3. DeepSeek Coder V2 (16B-236B parameters)**

- **Best for:** Complex code generation, large codebases
- **Size:** 16B version recommended (32GB RAM)
- **Performance:** Excellent code understanding
- **Unique:** Uses Mixture-of-Experts (MoE) - only activates subset of parameters

**Installation:**
```
LM Studio → Search "DeepSeek Coder V2" → Download deepseek-coder-v2-16b
```

---

#### LM Studio Setup

**What:** Desktop app for running local LLMs (Mac, Windows, Linux)

**Download:** https://lmstudio.ai/

**Setup:**
```
1. Install LM Studio
2. Browse model library
3. Download model (e.g., Qwen 2.5 Coder 7B)
4. Load model in chat interface
5. Start using!
```

**GPU Acceleration:**
- NVIDIA GPU: Uses CUDA automatically
- AMD GPU: Use ROCm (Linux only)
- Apple Silicon: Uses Metal (M1/M2/M3 Macs)
- CPU only: Works but slower

---

#### Performance Comparison: Cloud vs Local

**Test:** Generate 100-line Python script with tests

| Model | Time | Cost | Quality |
|-------|------|------|---------|
| Claude Opus (cloud) | 15s | $0.15 | Excellent |
| Qwen 2.5 35B (local GPU) | 25s | $0 | Excellent |
| Qwen 2.5 7B (local GPU) | 8s | $0 | Very good |
| Gemma 2 9B (local GPU) | 12s | $0 | Good |

**Verdict:** Local models are 80-90% as good as Claude, but FREE after setup.

---

#### Real-World Use Case: Replaced Claude Pro

**Original setup:**
- Claude Pro subscription: $20/month
- ChatGPT Plus: $20/month
- Total: $40/month = $480/year

**New setup:**
- LM Studio (free)
- Qwen 2.5 Coder 35B (coding tasks)
- Gemma 2 9B (everyday tasks, runs 24/7)
- Total: $0/month

**Hardware:**
- MacBook Pro M3 (Qwen + Gemma)
- Desktop with RTX 4090 (heavier models)

**Productivity change:** No drop. Latency actually improved (local is faster).

---

## Cloud vs Local AI Models

### Decision Matrix

**Use Cloud AI (Claude, GPT-4) when:**
- You need absolute best quality
- Task is critical (production code, customer-facing)
- You have budget for subscriptions
- Privacy is not a concern
- You need multimodal (vision, voice)
- You work on mobile (no local GPU)

**Use Local AI when:**
- Privacy matters (confidential data)
- High usage volume (saves cost)
- You have capable hardware (GPU recommended)
- Task is routine (coding, summarization, Q&A)
- You want offline capability
- You want to experiment with prompts (free iterations)

**Hybrid approach (best):**
- Local models for 90% of tasks
- Cloud models for critical 10%

---

### Cost Comparison (Annual)

**Scenario:** 50,000 requests/year (typical engineer usage)

**Cloud only:**
```
Claude Pro: $240/year
ChatGPT Plus: $240/year
Total: $480/year
```

**Local only:**
```
Hardware: $500 (GPU) one-time
Electricity: ~$50/year
Total Year 1: $550
Total Year 2+: $50/year
```

**Breakeven:** 1 year

**Savings after 3 years:** Cloud = $1,440 vs Local = $650 → **$790 saved**

---

### Privacy Comparison

**Cloud AI:**
- Your prompts sent to vendor servers
- Training data may include your inputs (unless opted out)
- Subject to vendor terms of service
- Potential for data breaches
- Compliance risk (GDPR, HIPAA)

**Local AI:**
- Data never leaves your machine
- No vendor access
- Complete control
- Offline capability
- Audit-friendly

**Example:** Debugging production logs with customer data
- Cloud: GDPR violation risk
- Local: Fully compliant

---

## Best Practices Summary

### Token Optimization (Top 10)

1. **Clear between tasks** - `/clear` prevents context accumulation
2. **Compact proactively** - `/compact` at 70% context, not 90%
3. **Shrink CLAUDE.md** - Keep under 200 lines
4. **Use cheaper models** - Haiku for simple tasks, Opus for complex
5. **Filter logs** - Extract errors only before showing AI
6. **Deny noisy files** - Block node_modules, logs, build folders
7. **Provide exact paths** - Don't make AI search entire codebase
8. **Use subagents** - Delegate verbose tasks
9. **Lower effort** - `/effort low` for simple edits
10. **Monitor usage** - `/context` and `/usage` before large tasks

---

### Persistent Context (Top 5)

1. **Create context journal** - Markdown file with rules, corrections, domain context
2. **Log every mistake** - Add to Corrections section when AI errors
3. **Load at session start** - System prompt or RAG document upload
4. **Keep updated** - Add new projects, remove outdated info
5. **Test it works** - Add absurd instruction, verify AI obeys

---

### Self-Hosted Tools (Top 5)

1. **Use Whisper for transcription** - Privacy + free vs Otter.ai
2. **Install LM Studio** - Easiest way to run local models
3. **Try Qwen 2.5 Coder** - Best local coding model
4. **Run Gemma 24/7** - Lightweight model for always-on assistance
5. **Hybrid approach** - Local for routine, cloud for critical

---

### Cost Optimization Strategy

**Tier your workload:**

| Task Type | Volume | Model | Cost |
|-----------|--------|-------|------|
| Formatting, renaming | 40% | Local (Gemma) | $0 |
| Tests, refactoring | 40% | Local (Qwen) | $0 |
| Daily coding | 15% | Claude Sonnet | $5/mo |
| Critical architecture | 5% | Claude Opus | $10/mo |

**Result:** $15/month (vs $40/month all-cloud) = **63% savings**

---

### Privacy-First Workflow

**Sensitive data:**
- Customer PII
- Production logs
- Proprietary code
- Medical/legal info

**Rule:** ONLY use local models for sensitive data.

**Process:**
```
1. Classify data sensitivity
2. If sensitive → local model only
3. If public → cloud or local (your choice)
4. If uncertain → default to local
```

---

## Further Learning

### Token Optimization
- Analytics Vidhya: "Tips for Claude Code Token Saving" (2026)
- KDNuggets: "7 Practical Ways to Reduce Claude Code Token Usage" (2026)

### Persistent Context
- XDA Developers: "Gave Local LLM Persistent Context Journal" (2026)

### Self-Hosted Tools
- Whisper GitHub: https://github.com/openai/whisper
- LM Studio: https://lmstudio.ai/
- Ollama: https://ollama.ai/ (alternative to LM Studio)

### Local Models
- Qwen GitHub: https://github.com/QwenLM/Qwen2.5
- Gemma on Hugging Face: https://huggingface.co/google/gemma-2-2b-it
- DeepSeek Coder: https://huggingface.co/deepseek-ai

---

**Last Updated:** 2026-05-21
**Version:** 1.0
