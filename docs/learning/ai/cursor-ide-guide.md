# Cursor IDE Quick Reference & Usage Guide

**Last Updated:** 2026-03-21
**Target Audience:** Developers, SREs, Engineers
**Level:** Beginner to Advanced

---

## Table of Contents

1. [What is Cursor?](#what-is-cursor)
2. [Installation & Setup](#installation--setup)
3. [Core Features](#core-features)
4. [Keyboard Shortcuts](#keyboard-shortcuts)
5. [AI Chat & Commands](#ai-chat--commands)
6. [Code Generation Patterns](#code-generation-patterns)
7. [Best Practices](#best-practices)
8. [MCP Integration](#mcp-integration)
9. [Troubleshooting](#troubleshooting)
10. [Quick Reference Card](#quick-reference-card)

---

## What is Cursor?

**Cursor** is an AI-powered code editor built on VS Code with native Claude/GPT integration.

**Key Differences from VS Code:**

| Feature | VS Code + Copilot | Cursor |
|---------|------------------|---------|
| AI Model | GitHub Copilot (GPT-4) | Claude Sonnet, GPT-4, custom models |
| AI Chat | Extension required | Built-in (Cmd+L) |
| Multi-file editing | Manual | AI can edit multiple files |
| Codebase context | Limited | Indexes entire codebase |
| MCP Support | Via extensions | Native support |
| Price | $10/month (Copilot) | $20/month (Pro) or Free tier |

**When to use Cursor:**
-  Need AI assistance for complex refactoring
-  Want codebase-aware AI (knows your project structure)
-  Prefer Claude over GitHub Copilot
-  Need multi-file AI editing
-  Want MCP server integration

**When to stick with VS Code:**
-  Just need autocomplete (Copilot is enough)
-  Company blocks Cursor
-  Don't want to learn new IDE

---

## Installation & Setup

### 1. Download & Install

**Linux (Fedora/RHEL):**

```bash
# Download from cursor.sh
wget https://downloader.cursor.sh/linux/appImage/x64 -O cursor.AppImage

# Make executable
chmod +x cursor.AppImage

# Run
./cursor.AppImage

# Optional: Install to /opt
sudo mv cursor.AppImage /opt/cursor
sudo ln -s /opt/cursor /usr/local/bin/cursor
```

**macOS:**

```bash
# Download from cursor.sh
brew install --cask cursor

# Or download .dmg from https://cursor.sh
```

**Verify Installation:**

```bash
cursor --version
```

---

### 2. First Launch Setup

**Import VS Code Settings (Optional):**

1. Open Cursor
2. Settings → Import from VS Code
3. Select extensions and keybindings to migrate

**Sign In:**

1. Cursor → Account → Sign In
2. Choose: Email, Google, or GitHub

---


**Settings → Cursor → Models**

**Free Tier:**
- 50 AI requests/month
- Claude Sonnet 3.5 (limited)
- GPT-4 (limited)

**Pro Tier ($20/month):**
- Unlimited requests
- Claude Opus, Sonnet, Haiku
- GPT-4, GPT-4 Turbo
- Custom models (API keys)

**Recommended for most work:**
- **Claude Sonnet 3.5** - Best for coding, fast, accurate
- **Claude Opus** - Complex refactoring, architecture
- **GPT-4** - Fallback if Claude unavailable

---

### 4. Configure Cursor for Your Environment

**Settings (Cmd+, or Ctrl+,):**

```json
{
  // AI model preference
  "cursor.models.default": "claude-sonnet-3.5",

  // Privacy: Don't send code to training
  "cursor.privacy.codebaseIndexing": false,
  "cursor.privacy.telemetry": false,

  // Terminal
  "terminal.integrated.shell.linux": "/bin/bash",

  // Git
  "git.enableCommitSigning": true,
  "git.confirmSync": false,

  // Python (if using)
  "python.defaultInterpreterPath": "/usr/bin/python3"
}
```

**Privacy Settings:**
- Settings → Privacy → **Disable** "Send code for training"
- Settings → Privacy → **Enable** "Private Mode" (Pro only)

---

## Core Features

### 1. AI Chat (Cmd+L)

**Open AI Chat:**
- **macOS:** `Cmd + L`
- **Linux/Windows:** `Ctrl + L`

**Basic Usage:**

```
You: Explain this function
[AI analyzes selected code and explains]

You: Add error handling to this function
[AI rewrites function with try-catch]

You: Refactor this to use async/await
[AI modernizes the code]
```

**Chat Context:**
- Current file automatically included
- Select code → included in context
- Reference other files: `@filename.py`

---

### 2. Inline Code Generation (Tab)

**How it works:**
1. Start typing or write a comment
2. Cursor suggests completion (ghost text)
3. Press `Tab` to accept
4. Press `Esc` to reject

**Example:**

```python
# Write a comment
# Function to calculate fibonacci sequence

# Press Enter, Cursor generates:
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

**Tips:**
- Write clear comments (AI uses them as instructions)
- Accept partial suggestions (Tab once, edit, Tab again)
- Reject bad suggestions immediately (Esc)

---

### 3. AI Edit (Cmd+K)

**Open AI Edit:**
- **macOS:** `Cmd + K`
- **Linux/Windows:** `Ctrl + K`

**How it works:**
1. Select code (or place cursor)
2. Press `Cmd+K`
3. Type instruction
4. AI rewrites code in place

**Examples:**

**Add type hints:**
```python
# Select function, Cmd+K, type: "add type hints"

# Before
def process_data(data):
    return [x * 2 for x in data]

# After
def process_data(data: list[int]) -> list[int]:
    return [x * 2 for x in data]
```

**Convert to async:**
```python
# Select function, Cmd+K, type: "convert to async"

# Before
def fetch_data(url):
    response = requests.get(url)
    return response.json()

# After
async def fetch_data(url: str) -> dict:
    async with aiohttp.ClientSession() as session:
        async response = await session.get(url)
        return await response.json()
```

---

### 4. Multi-File Editing

**AI can edit multiple files at once.**

**How to use:**

1. Open AI Chat (`Cmd+L`)
2. Type instruction referencing multiple files
3. AI shows diff for each file
4. Accept or reject changes

**Example:**

```
You: Rename the User class to Account across all files

AI response:
I'll update the following files:
1. models/user.py (rename class)
2. controllers/auth.py (import change)
3. tests/test_user.py (class references)

[Shows diffs for each file]

[Accept All] [Reject] [Edit]
```

---

### 5. Codebase Indexing

**Cursor indexes your entire codebase for context-aware AI.**

**How to enable:**

1. Open project in Cursor
2. Settings → Cursor → Codebase Indexing
3. Click "Index Codebase"
4. Wait for indexing to complete (1-5 minutes)

**Benefits:**
- AI knows your project structure
- AI can find relevant files automatically
- AI understands your coding patterns

**Example:**

```
You: Where is the authentication logic implemented?

AI: Based on your codebase, authentication is in:
- src/auth/jwt_handler.py (JWT token generation)
- src/middleware/auth.py (authentication middleware)
- src/models/user.py (User model with password hashing)
```

---

### 6. Composer (Multi-File Agent)

**Composer** is Cursor's multi-file AI agent.

**How to use:**

1. Open Composer: `Cmd + I` (macOS) or `Ctrl + I` (Linux/Windows)
2. Describe what you want to build
3. AI creates/edits multiple files
4. Review and accept changes

**Example:**

```
You: Create a REST API endpoint for user registration with validation

AI creates:
1. routes/users.py (endpoint)
2. schemas/user.py (validation schema)
3. models/user.py (database model)
4. tests/test_users.py (unit tests)

[Shows all files in diff view]
[Accept All] [Reject] [Edit Individual Files]
```

**When to use Composer:**
- Creating new features (multiple files)
- Large refactorings
- Scaffolding new projects

---

## Keyboard Shortcuts

### Essential Shortcuts

| Action | macOS | Linux/Windows |
|--------|-------|---------------|
| **AI Chat** | `Cmd + L` | `Ctrl + L` |
| **AI Edit** | `Cmd + K` | `Ctrl + K` |
| **Composer** | `Cmd + I` | `Ctrl + I` |
| **Accept Suggestion** | `Tab` | `Tab` |
| **Reject Suggestion** | `Esc` | `Esc` |
| **Next Suggestion** | `Cmd + ]` | `Ctrl + ]` |
| **Command Palette** | `Cmd + Shift + P` | `Ctrl + Shift + P` |
| **Quick Open** | `Cmd + P` | `Ctrl + P` |
| **Toggle Terminal** | `` Ctrl + ` `` | `` Ctrl + ` `` |
| **Multi-cursor** | `Cmd + D` | `Ctrl + D` |
| **Comment Line** | `Cmd + /` | `Ctrl + /` |
| **Format Document** | `Shift + Option + F` | `Shift + Alt + F` |

### Advanced Shortcuts

| Action | macOS | Linux/Windows |
|--------|-------|---------------|
| **Go to Definition** | `F12` | `F12` |
| **Peek Definition** | `Option + F12` | `Alt + F12` |
| **Find References** | `Shift + F12` | `Shift + F12` |
| **Rename Symbol** | `F2` | `F2` |
| **Go to Line** | `Ctrl + G` | `Ctrl + G` |
| **Toggle Sidebar** | `Cmd + B` | `Ctrl + B` |
| **Zen Mode** | `Cmd + K Z` | `Ctrl + K Z` |

---

## AI Chat & Commands

### Chat Interface Features

**@ Mentions:**
- `@filename.py` - Include specific file in context
- `@foldername` - Include entire folder
- `@codebase` - Search entire codebase
- `@docs` - Reference documentation (if indexed)
- `@web` - Search web (Pro only)

**Example:**

```
You: @auth/jwt_handler.py explain how token generation works

AI: In jwt_handler.py, token generation happens in the `create_token()` function:
[Detailed explanation with code references]
```

---

### Common AI Commands

**Code Explanation:**
```
Explain this function
What does this code do?
How does [concept] work in this file?
```

**Code Generation:**
```
Write a function that [description]
Add a method to [class] that [action]
Create a [component/module] for [purpose]
```

**Refactoring:**
```
Refactor this to use [pattern/style]
Extract this into a separate function
Simplify this logic
Add error handling
Add type hints
Add docstrings
```

**Testing:**
```
Write unit tests for this function
Generate test cases for edge cases
Create a mock for this API call
```

**Debugging:**
```
Why is this code failing?
Fix this bug: [error message]
Add logging to this function
```

**Documentation:**
```
Write a docstring for this function
Explain this code for a README
Add comments explaining the logic
```

---

## Code Generation Patterns

### Pattern 1: Comment-Driven Development

**Write comments describing what you want, AI generates code.**

```python
# Example: Generate Fibonacci function

# Function to calculate nth Fibonacci number
# Uses memoization for efficiency
# Returns the Fibonacci number at position n

# [Cursor generates:]
def fibonacci(n: int, memo: dict = None) -> int:
    if memo is None:
        memo = {}
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fibonacci(n-1, memo) + fibonacci(n-2, memo)
    return memo[n]
```

---

### Pattern 2: Example-Driven Generation

**Provide one example, AI generates similar code.**

```python
# Example: REST API endpoints

# Existing code:
@app.get("/users/{user_id}")
async def get_user(user_id: int):
    return {"user_id": user_id}

# Type comment for next endpoint:
# GET /posts/{post_id}

# [Cursor generates:]
@app.get("/posts/{post_id}")
async def get_post(post_id: int):
    return {"post_id": post_id}
```

---

### Pattern 3: Test-Driven Development

**Write test first, AI generates implementation.**

```python
# Write test
def test_calculate_discount():
    assert calculate_discount(100, 0.1) == 90
    assert calculate_discount(50, 0.2) == 40
    assert calculate_discount(200, 0) == 200

# Place cursor below, type comment:
# Implement calculate_discount function

# [Cursor generates:]
def calculate_discount(price: float, discount: float) -> float:
    """Calculate price after applying discount."""
    return price * (1 - discount)
```

---

### Pattern 4: Incremental Refinement

**Generate basic version, then refine with AI Edit.**

```python
# Step 1: Generate basic function
# Function to fetch user data from API

def fetch_user(user_id):
    response = requests.get(f"/api/users/{user_id}")
    return response.json()

# Step 2: Select function, Cmd+K: "add error handling"
def fetch_user(user_id):
    try:
        response = requests.get(f"/api/users/{user_id}")
        response.raise_for_status()
        return response.json()
    except requests.RequestException as e:
        logger.error(f"Failed to fetch user {user_id}: {e}")
        return None

# Step 3: Select function, Cmd+K: "make it async"
async def fetch_user(user_id: int) -> dict | None:
    try:
        async with aiohttp.ClientSession() as session:
            async with session.get(f"/api/users/{user_id}") as response:
                response.raise_for_status()
                return await response.json()
    except aiohttp.ClientError as e:
        logger.error(f"Failed to fetch user {user_id}: {e}")
        return None
```

---

## Best Practices

### 1. Provide Clear Context

**Bad:**
```
Fix this
```

**Good:**
```
This function fails when input is None. Add null checking and return empty list.
```

---

### 2. Review AI-Generated Code

**Always review:**
-  Logic correctness
-  Edge cases handled
-  Security (no SQL injection, XSS, etc.)
-  Performance (no N+1 queries, infinite loops)
-  Imports are correct

**Don't blindly accept AI code!**

---

### 3. Use AI for Repetitive Tasks

**Good use cases:**
- Writing boilerplate (constructors, getters, setters)
- Adding type hints to existing code
- Writing test cases
- Converting code between styles (camelCase → snake_case)
- Generating documentation

**Bad use cases:**
- Complex algorithms (verify carefully!)
- Security-critical code (validate thoroughly)
- Domain-specific business logic (AI may not understand context)

---

### 4. Iterate with AI

**Don't expect perfect code on first try.**

**Workflow:**
1. Generate initial version
2. Review and test
3. Refine with specific instructions
4. Repeat until satisfied

---

### 5. Leverage Codebase Context

**Enable codebase indexing for better results.**

```
# Without indexing:
You: Create a user service

AI: [Generates generic code]

# With indexing:
You: Create a user service following our existing service pattern

AI: [Generates code matching your project's style, imports, patterns]
```

---

### 6. Use @ Mentions Effectively

**Reference specific files:**

```
You: @models/user.py create a migration to add email_verified field

AI: Based on your User model, here's the migration:
[Generates migration matching your model structure]
```

---

## MCP Integration

**Cursor supports MCP (Model Context Protocol) servers natively.**

### Setup MCP in Cursor

**1. Configure MCP Servers:**

Create `~/.cursor/mcp_servers.json`:

```json
{
  "mcpServers": {
    "jira": {
      "command": "uv",
      "args": [
        "--directory",
        "/home/mgreczi/repos/jira-mcp-server",
        "run",
        "python",
        "main.py"
      ],
      "env": {
        "JIRA_URL": "https://your-company.atlassian.net",
        "JIRA_EMAIL": "your.email@company.com",
        "JIRA_TOKEN": "${JIRA_API_TOKEN}"
      }
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**2. Restart Cursor**

**3. Verify MCP Tools Available:**

Open AI Chat (`Cmd+L`):

```
You: List my Jira tickets

AI: [Uses Jira MCP server to fetch tickets]
```

---

### MCP Use Cases in Cursor

**Jira Integration:**
```
You: Summarize JIRA-123 and update the code to fix the issue

AI: [Fetches ticket details via MCP, generates code fix]
```

**GitHub Integration:**
```
You: Create a PR for this branch with summary of changes

AI: [Uses GitHub MCP to create PR]
```

**Custom MCP Servers:**
- Database queries (query staging DB for test data)
- Internal APIs (fetch config from internal service)
- Documentation (search internal wikis)

---

## Troubleshooting

### Issue 1: AI Suggestions Not Appearing

**Symptoms:** No ghost text when typing

**Solutions:**
1. Check AI model is selected: Settings → Cursor → Models
2. Verify internet connection
3. Check free tier limits (50 requests/month)
4. Restart Cursor
5. Clear cache: `Cmd+Shift+P` → "Clear Cache"

---

### Issue 2: "Rate Limited" Error

**Symptoms:** "You've reached your usage limit"

**Solutions:**
1. Free tier: Wait until next month OR upgrade to Pro
2. Pro tier: Check billing status
3. Use alternative model (switch Claude ↔ GPT-4)

---

### Issue 3: AI Gives Wrong Answers

**Symptoms:** Generated code doesn't work or is incorrect

**Solutions:**
1. Provide more context (select more code, use @mentions)
2. Be more specific in instructions
3. Break task into smaller steps
4. Enable codebase indexing (Settings → Cursor)
5. Try different AI model (Opus for complex tasks)

---

### Issue 4: Slow Performance

**Symptoms:** AI takes >10 seconds to respond

**Solutions:**
1. Large codebase: Disable indexing temporarily
2. Network issues: Check internet speed
3. Too much context: Reduce selected code
4. Switch to faster model (Haiku instead of Opus)

---

### Issue 5: Privacy Concerns

**Symptoms:** Worried about code being sent to AI

**Solutions:**
1. Enable Private Mode (Pro only): Settings → Privacy
2. Disable telemetry: Settings → Privacy → Telemetry
3. Disable codebase indexing: Settings → Cursor
4. Use on-premise models (Enterprise only)
5. Review Cursor privacy policy

---

## Quick Reference Card

**Print this section and keep it visible!**

---

### Most Used Shortcuts

| Action | Shortcut (Mac) | Shortcut (Linux) |
|--------|---------------|------------------|
| AI Chat | `Cmd + L` | `Ctrl + L` |
| AI Edit | `Cmd + K` | `Ctrl + K` |
| Composer | `Cmd + I` | `Ctrl + I` |
| Accept AI | `Tab` | `Tab` |
| Reject AI | `Esc` | `Esc` |

---

### Common Commands

**In AI Chat (`Cmd+L`):**

```
Explain this code
Add error handling
Convert to async
Write tests for this
Add type hints
Refactor using [pattern]
Fix bug: [error message]
```

**With @Mentions:**

```
@filename.py explain how [feature] works
@folder create a new [component] following existing patterns
@codebase where is [functionality] implemented?
```

---

### Workflow Patterns

**1. Quick Fix:**
```
Select code → Cmd+K → Type fix → Accept
```

**2. Explain Code:**
```
Select code → Cmd+L → "Explain this" → Read
```

**3. Generate New Code:**
```
Write comment → Press Enter → Tab to accept
```

**4. Multi-File Edit:**
```
Cmd+I → Describe feature → Review diffs → Accept
```

---

### Tips for Better Results

1. **Be specific** - "Add null checking" not "Fix this"
2. **Provide context** - Select relevant code, use @mentions
3. **Iterate** - Generate → Review → Refine
4. **Review always** - Never blindly accept AI code
5. **Use examples** - Show AI what you want (pattern matching)

---

### Settings Quick Access

```
Privacy: Cmd+, → Privacy
Models: Cmd+, → Cursor → Models
Keybindings: Cmd+K Cmd+S
Extensions: Cmd+Shift+X
```

---

### Pricing Tiers

| Tier | Price | Features |
|------|-------|----------|
| **Free** | $0 | 50 AI requests/month, basic models |
| **Pro (Personal)** | $20/month | Unlimited requests, all models, private mode |
| **Pro** | $20/month | Claude Sonnet 3.5, GPT-4, unlimited requests |
| **Business** | Custom | Team features, admin controls |


---

## Advanced Tips

### Tip 1: Custom AI Instructions

**Create project-specific AI rules.**

Create `.cursorrules` file in project root:

```
# .cursorrules

When writing Python code:
- Use type hints for all functions
- Follow PEP 8 style guide
- Add docstrings in Google format
- Use async/await for I/O operations
- Add error handling with proper logging

When writing tests:
- Use pytest framework
- Follow Arrange-Act-Assert pattern
- Add descriptive test names
- Mock external dependencies
```

AI will follow these rules for this project!

---

### Tip 2: Keyboard-Driven Workflow

**Minimize mouse usage for speed.**

```
Open file: Cmd+P → type filename → Enter
Navigate code: Cmd+Shift+O → type symbol → Enter
AI edit: Cmd+K → type instruction → Enter
Switch tabs: Cmd+1, Cmd+2, etc.
Terminal: Ctrl+`
```

---

### Tip 3: Multi-Cursor Editing

**Select multiple instances, AI edits all at once.**

```
1. Select variable name
2. Cmd+D (select next occurrence)
3. Repeat Cmd+D for all occurrences
4. Cmd+K → "rename to snake_case"
5. All instances renamed!
```

---

### Tip 4: Inline AI Documentation

**Generate docs while coding.**

```python
# Select function, Cmd+K: "add Google-style docstring"

def calculate_discount(price: float, discount: float) -> float:
    """Calculate the discounted price.

    Args:
        price: Original price before discount.
        discount: Discount rate (0.0 to 1.0).

    Returns:
        The price after applying the discount.

    Raises:
        ValueError: If discount is not between 0 and 1.
    """
    if not 0 <= discount <= 1:
        raise ValueError("Discount must be between 0 and 1")
    return price * (1 - discount)
```

---

### Tip 5: AI-Assisted Code Review

**Use AI to review your own code before PR.**

```
You: Review this code for bugs, security issues, and best practices

AI: I found several issues:
1. Line 45: Potential SQL injection (use parameterized queries)
2. Line 78: Missing null check (could cause NoneType error)
3. Line 102: Infinite loop risk (no exit condition)
4. Consider adding error handling for network calls
```

---

## Comparison: Cursor vs Alternatives

| Feature | Cursor Pro | Claude Code CLI | GitHub Copilot | VS Code |
|---------|-----------|-----------------|----------------|---------|
| **AI Chat** |  Built-in |  Terminal-based |  | Extension |
| **Code Completion** |  |  |  | Extensions |
| **Multi-file Editing** |  |  |  | Manual |
| **Codebase Context** |  Indexed |  MCP |  |  |
| **MCP Support** |  |  Native |  | Extensions |
| **Cost** | $20/month | Free (beta) | $10/month | Free |
| **Models** | Claude, GPT-4 | Claude only | GPT-4 | N/A |
| **Privacy Mode** |  (Pro) |  |  | N/A |

**Recommendation:**
- **Cursor Pro** - Best for daily coding with AI assistance
- **Claude Code CLI** - Best for automation, file operations, MCP workflows
- **Use both!** - Cursor for coding, Claude Code for investigations

---

## Related Documentation

**Internal:**
- [Effective Jira Workflow with MCP](Effective_Jira_Workflow_with_MCP_Servers.md)
- [Using Claude Effectively](Using_Claude_Effectively.md)

**External:**
- [Cursor Official Docs](https://docs.cursor.sh)
- [Cursor Changelog](https://changelog.cursor.sh)
- [Cursor Community](https://community.cursor.sh)

---

**Document Version:** 1.0
**Last Updated:** 2026-03-21
