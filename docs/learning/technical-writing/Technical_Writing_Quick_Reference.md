# Technical Writing Quick Reference

**Purpose:** Practical guidelines for writing clear, usable documentation (SOPs, guides, manuals)

**Target Audience:** Engineers, technical writers, documentation creators

**Last Updated:** 2026-03-21

---

## Table of Contents

1. [Core Principles](#core-principles)
2. [Document Structure](#document-structure)
3. [Visual Hierarchy](#visual-hierarchy)
4. [Writing Style](#writing-style)
5. [Lists and Tables](#lists-and-tables)
6. [Code and Commands](#code-and-commands)
7. [Common Mistakes](#common-mistakes)
8. [Web Ergonomics](#web-ergonomics)
9. [Quick Checklist](#quick-checklist)

---

## Core Principles

### 1. Write for Scanning, Not Reading

**Reality:** Users scan documentation looking for specific information. They rarely read word-by-word.

**Implication:** Structure matters more than prose quality.

### 2. One Concept Per Paragraph

**Bad:**
```
The server configuration file is located at /etc/server.conf and you
need to edit it with root privileges. The important settings are
timeout, max_connections, and log_level. Make sure to restart the
service after changes using systemctl restart server. Also check
the logs at /var/log/server.log if something goes wrong.
```

**Good:**
```
**Configuration File:** /etc/server.conf (requires root privileges)

**Key Settings:**
- timeout
- max_connections
- log_level

**After Changes:**
1. Restart service: `systemctl restart server`
2. Check logs: /var/log/server.log
```

### 3. Front-Load Critical Information

Put the most important information first. Users often stop reading after the first sentence.

**Bad:** "In order to configure the system, which is essential for proper operation, you must first understand that the configuration file, located in the system directory, needs to be edited with the appropriate permissions..."

**Good:** "Edit /etc/server.conf with root privileges."

---

## Document Structure

### Standard Template for SOPs and Guides

```markdown
# [Title: Action-Oriented, Clear]

**Purpose:** One sentence explaining what this document achieves

**Prerequisites:** (if applicable)
- Requirement 1
- Requirement 2

**Time Required:** X minutes (for procedures)

---

## Overview

Brief 2-3 sentence summary of the entire process.

## Step 1: [First Major Phase]

Detailed instructions...

### Step 1.1: [Sub-step if needed]

Details...

## Step 2: [Second Major Phase]

...

## Troubleshooting

Common issues and solutions.

## Related Documentation

Links to related resources.
```

### Essential Sections

Every technical document should have:

1. **Title** - Specific, not generic ("Configure NGINX Load Balancer" not "Server Setup")
2. **Purpose** - One sentence: what problem does this solve?
3. **Prerequisites** - What must be ready before starting?
4. **Numbered Steps** - For procedures (actions to take)
5. **Troubleshooting** - Common failure modes
6. **Related Links** - Where to go next

---

## Visual Hierarchy

### Use Headings Properly

```markdown
# H1: Document Title (only ONE per document)

## H2: Major Sections

### H3: Subsections

#### H4: Minor Subsections (use sparingly)
```

**Rule:** Never skip heading levels (don't go H2 → H4)

### White Space is Your Friend

**Bad:**
```
To configure the database you need to edit the config file.
The file is at /etc/db.conf. Open it with vim. Set the
max_connections parameter to 100. Set the timeout to 30.
Save and exit. Restart the database with systemctl restart db.
```

**Good:**
```
**Task:** Configure database settings

**Steps:**
1. Edit configuration file:
```bash
vim /etc/db.conf
```

2. Update parameters:
- max_connections = 100
- timeout = 30

3. Restart service:
```bash
systemctl restart db
```
```

### Visual Separators

Use horizontal rules to separate major sections:

```markdown
---
```

Use this between major conceptual shifts, not between every paragraph.

---

## Writing Style

### Use Active Voice

**Passive (Bad):** "The configuration file should be edited..."
**Active (Good):** "Edit the configuration file..."

### Use Imperative Mood for Instructions

**Descriptive (Bad):** "You should click the Save button."
**Imperative (Good):** "Click Save."

### Be Specific, Not Vague

| Vague | Specific |
|-------|----------|
| "Large number" | "More than 1000" or "10,000+" |
| "The file" | "/etc/nginx/nginx.conf" |
| "Soon" | "Within 5 minutes" |
| "The command" | "`systemctl restart nginx`" |
| "Might fail" | "Fails with error: 'Connection refused'" |

### Short Sentences Win

**Bad:** "In the event that the service fails to start, which can happen for a variety of reasons including misconfiguration or permission issues, you should check the system logs located at /var/log/service.log to identify the root cause before attempting to restart."

**Good:** "If the service fails to start, check /var/log/service.log for errors. Common causes: misconfiguration or permission issues."

**Guideline:** Aim for 15-20 words per sentence. Split sentences longer than 25 words.

### Avoid Jargon Without Definition

**First Use:** Define or link to definition
**Subsequent Uses:** Use freely

**Example:**
```markdown
Configure the **DaemonSet** (a Kubernetes resource that ensures
a pod runs on every node) to deploy the agent.

Later in document:
Update the DaemonSet configuration...
```

---

## Lists and Tables

### When to Use Lists

**Unordered Lists (bullets):**
- Items with no sequence/hierarchy
- Features, benefits, requirements
- Options or alternatives

**Ordered Lists (numbers):**
- Sequential steps (procedures)
- Prioritized items (1st, 2nd, 3rd...)
- Prerequisites that must be completed in order

**Wrong Use of Numbered List:**
```markdown
Features:
1. Fast performance
2. Secure authentication
3. Easy configuration
```

**Correct:**
```markdown
Features:
- Fast performance
- Secure authentication
- Easy configuration
```

### Parallel Construction

All list items should have the same grammatical structure.

**Bad:**
```
- Configure the server
- Installation of packages
- You should restart services
```

**Good:**
```
- Configure the server
- Install packages
- Restart services
```

### Tables vs. Lists

**Use Tables When:**
- Comparing items across multiple dimensions
- Showing relationships between concepts
- Displaying structured data

**Example:**

| Environment | Hostname | IP Address | Purpose |
|-------------|----------|------------|---------|
| Production | prod-db1 | 10.0.1.10 | Primary DB |
| Staging | stage-db1 | 10.0.2.10 | Testing |

**Use Lists When:**
- Single dimension of information
- Simple enumeration
- Steps in a process

---

## Code and Commands

### Use Code Blocks with Language Tags

**Bad:**
```
Run the command: git commit -m "Fix bug"
```

**Good:**
````markdown
Run the command:
```bash
git commit -m "Fix bug"
```
````

### Inline vs. Block Code

**Inline code** (backticks): For references within sentences
```markdown
Edit the `nginx.conf` file and set `worker_processes` to 4.
```

**Code blocks**: For commands to run or code to copy
````markdown
```bash
systemctl restart nginx
systemctl status nginx
```
````

### Show Expected Output

**Complete Example:**
````markdown
Check service status:
```bash
systemctl status myservice
```

Expected output:
```
myservice.service - My Service
Loaded: loaded (/etc/systemd/system/myservice.service; enabled)
Active: active (running) since...
```
````

### Command Prompts

**For single commands:**
```bash
systemctl restart nginx
```

**For command sequences (show prompt):**
```bash
$ cd /etc/nginx
$ sudo vim nginx.conf
$ sudo systemctl restart nginx
```

**For root commands:**
```bash
# systemctl restart nginx # Run as root
```

---

## Common Mistakes

### 1. Wall of Text

**Problem:** Large paragraphs with no breaks

**Solution:**
- Max 3-4 sentences per paragraph
- Use subheadings every 3-5 paragraphs
- Add lists, code blocks, tables to break up text

### 2. Burying the Lead

**Problem:** Important information appears late in the document

**Solution:**
- Put critical warnings at the top
- Use callout boxes for important notes
- Front-load each section with key points

### 3. Missing Context

**Problem:** Assumes reader knows background

**Solution:**
- Add "Prerequisites" section
- Link to background reading
- Define terms on first use

### 4. No Visual Hierarchy

**Problem:** Everything looks the same (no headings, no bold, no lists)

**Solution:**
- Use H2/H3 headings generously
- Bold key terms and values
- Convert sentence lists to bullet lists

### 5. Ambiguous Instructions

**Problem:** "Update the configuration as needed"

**Solution:** Be specific: "Set `max_connections` to 100 in /etc/db.conf"

### 6. Inconsistent Terminology

**Problem:** "service", "daemon", "process" used interchangeably

**Solution:** Pick one term and use it consistently throughout document

---

## Web Ergonomics

### Scannable Content

**F-Pattern Reading:**
Users scan in an F-shaped pattern:
- Horizontally across the top
- Down the left side
- Horizontally again partway down

**Optimize for this:**
- Put important keywords at the start of headings
- Use descriptive subheadings
- Left-align text (never center-align body text)

### Link Text Should Be Descriptive

**Bad:** "Click [here](url) for more information"
**Good:** "See the [installation guide](url) for details"

**Why:**
- "Here" is meaningless when scanned
- Screen readers read link text out of context

### Table of Contents for Long Documents

**Add TOC when:**
- Document > 5 pages
- Multiple major sections
- Users need to jump to specific sections

**Tools:**
- Markdown: Use anchor links
- Wiki/CMS: Auto-generate TOC
- HTML: `id` attributes + nav links

### Navigation Breadcrumbs

For documentation sites:
```
Home > Guides > Installation > Database Setup
```

Helps users understand context and navigate back.

---

## Quick Checklist

Before publishing any technical document, verify:

### Structure
- [ ] Clear, specific title
- [ ] Purpose statement in first paragraph
- [ ] Logical heading hierarchy (H2 → H3 → H4)
- [ ] Table of contents (if >5 pages)
- [ ] Prerequisites listed upfront

### Content
- [ ] Active voice in instructions
- [ ] Specific values/paths (not vague references)
- [ ] Code blocks use syntax highlighting
- [ ] Steps are numbered and sequential
- [ ] Commands show expected output

### Readability
- [ ] Sentences < 25 words
- [ ] Paragraphs < 5 sentences
- [ ] White space between sections
- [ ] Lists for 3+ related items
- [ ] Tables for comparing options

### Usability
- [ ] Troubleshooting section included
- [ ] Links to related documentation
- [ ] Screenshots/diagrams where helpful
- [ ] Tested by someone unfamiliar with topic
- [ ] Updated date visible

### Accessibility
- [ ] Descriptive link text (not "click here")
- [ ] Alt text for images
- [ ] Consistent heading structure
- [ ] High contrast text

---

## Examples: Before & After

### Example 1: SOP for Server Restart

#### Before (Bad)

```
Server Restart Procedure

Sometimes the server needs to be restarted. This happens when there
are updates or configuration changes. Before restarting you should
check if users are connected because they will be disconnected. The
command to check is netstat. After checking you can restart by using
the systemctl command. Make sure to check the logs afterwards to see
if everything is working. The logs are in /var/log.
```

#### After (Good)

```markdown
# Server Restart Procedure

**Purpose:** Safely restart the application server after updates or configuration changes

**Impact:** Active user sessions will be disconnected

**Time Required:** 5 minutes

---

## Prerequisites

- Root/sudo access
- Maintenance window scheduled
- Users notified of downtime

---

## Steps

### 1. Check Active Connections

Verify current user count:
```bash
netstat -an | grep :8080 | grep ESTABLISHED | wc -l
```

**If >10 users:** Delay restart until traffic decreases.

### 2. Restart Service

```bash
sudo systemctl restart appserver
```

### 3. Verify Service Status

```bash
sudo systemctl status appserver
```

**Expected Output:**
```
appserver.service - Application Server
Active: active (running) since...
```

### 4. Check Logs

```bash
sudo tail -f /var/log/appserver/error.log
```

**Look for:** "Server started successfully on port 8080"

---

## Troubleshooting

**Service fails to start:**
1. Check configuration: `sudo appserver configtest`
2. Review logs: `sudo journalctl -u appserver -n 50`
3. Common issue: Port 8080 already in use

**Related Documentation:**
- [Configuration Guide](link)
- [Log Analysis Guide](link)
```

---

### Example 2: Configuration Documentation

#### Before (Bad)

```
Configuration

The config file has various settings you can change. The timeout
setting controls how long to wait. Max connections is for limiting
users. Debug mode can be turned on for troubleshooting.
```

#### After (Good)

```markdown
# Configuration Reference

**File Location:** `/etc/myapp/config.yaml`

**Format:** YAML

---

## Core Settings

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `timeout` | integer | 30 | Connection timeout in seconds |
| `max_connections` | integer | 100 | Maximum simultaneous connections |
| `debug_mode` | boolean | false | Enable verbose logging |
| `log_file` | string | /var/log/myapp.log | Log file path |

---

## Configuration Examples

### Production Setup

```yaml
timeout: 60
max_connections: 500
debug_mode: false
log_file: /var/log/myapp/production.log
```

### Development Setup

```yaml
timeout: 10
max_connections: 10
debug_mode: true
log_file: /tmp/myapp-dev.log
```

---

## Apply Changes

After editing configuration:

```bash
# Validate configuration
myapp --config-test

# Restart service
sudo systemctl restart myapp
```

**Note:** Invalid configuration will prevent service startup. Always validate before restarting.
```

---

## Advanced Techniques

### Progressive Disclosure

For complex topics, layer information:

1. **Overview** - High-level summary (what & why)
2. **Quick Start** - Minimal steps to get working
3. **Detailed Guide** - Full explanation
4. **Reference** - Complete parameter list

**Example Structure:**
```markdown
# Database Backup Guide

## Quick Start (5 minutes)
Minimal steps for common case...

## Complete Guide
Detailed explanation with all options...

## Reference
Full parameter documentation...
```

### Callout Boxes

Use admonitions for important information:

```markdown
> **WARNING:** This operation is destructive and cannot be undone.

> **TIP:** Use the `--dry-run` flag to preview changes.

> **NOTE:** This feature requires version 2.0 or later.
```

**Standard Callout Types:**
- **WARNING** - Data loss, security risk, irreversible action
- **CAUTION** - Potential issues, proceed carefully
- **NOTE** - Additional information, context
- **TIP** - Best practice, optimization hint
- **EXAMPLE** - Practical demonstration

### Screenshots and Diagrams

**When to Include:**
- UI workflows (click here, then here...)
- Network architecture diagrams
- Complex decision trees
- Before/after comparisons

**Best Practices:**
- Annotate screenshots (arrows, highlights, labels)
- Keep diagrams simple (max 7±2 elements)
- Use consistent color coding
- Provide alt text: `![Screenshot of login page](image.png)`

### Version Information

For documentation that evolves:

```markdown
**Last Updated:** 2026-03-21
**Applies To:** MyApp v2.5.x
**Deprecated:** The `--old-flag` parameter (use `--new-flag` instead)
```

---

## Tools and Resources

### Linters and Validators

- **markdownlint** - Enforce Markdown style consistency
- **alex** - Catch insensitive, inconsiderate writing
- **write-good** - Detect passive voice, weasel words
- **proselint** - General writing quality

### Readability Checkers

- **Hemingway Editor** - Highlight complex sentences
- **Readable.com** - Calculate readability scores
- **Grammarly** - Grammar and clarity suggestions

### Diagramming Tools

- **Mermaid** - Text-to-diagram (flowcharts, sequences)
- **PlantUML** - UML diagrams from text
- **Draw.io / Excalidraw** - Visual diagramming

### Documentation Frameworks

- **MkDocs** - Static site generator for project docs
- **Docusaurus** - React-based documentation sites
- **ReadTheDocs** - Sphinx-based documentation hosting
- **GitBook** - Documentation platform

---

## Real-World Application Scenarios

### SOP for Incident Response

**Structure:**
```markdown
# [Severity Level] Incident Response: [Type]

## Immediate Actions (First 5 Minutes)
1. Acknowledge alert
2. Assess impact
3. Notify stakeholders

## Investigation (Next 15 Minutes)
...

## Resolution Steps
...

## Post-Incident
...
```

### API Documentation

**Structure:**
```markdown
# GET /api/v1/users

**Description:** Retrieve list of users

**Authentication:** Bearer token required

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| limit | integer | No | Max results (default: 100) |

**Example Request:**
```bash
curl -H "Authorization: Bearer TOKEN" \
https://api.example.com/v1/users?limit=10
```

**Example Response:**
```json
{
"users": [...],
"total": 1234
}
```

**Error Codes:**
| Code | Meaning |
|------|---------|
| 401 | Unauthorized |
| 429 | Rate limit exceeded |
```

### Runbook for Operations

**Structure:**
```markdown
# Runbook: [Service Name]

## Service Overview
- **Purpose:** ...
- **Criticality:** High/Medium/Low
- **On-Call Team:** ...

## Health Checks
- [ ] Service responds to /health
- [ ] Database connections < 80%
- [ ] Disk usage < 85%

## Common Alerts

### Alert: High Memory Usage
**Threshold:** >90%
**Impact:** Performance degradation
**Resolution:** Restart service
**Escalation:** After 2 failures

## Emergency Procedures
...
```

---

## Metrics for Documentation Quality

### Quantifiable Goals

| Metric | Good Target |
|--------|-------------|
| Time to first result | <30 seconds (user finds relevant section) |
| Task completion rate | >90% (user completes task without help) |
| Readability score | Grade 8-10 (Flesch-Kincaid) |
| Avg sentence length | 15-20 words |
| Avg paragraph length | 3-4 sentences |
| Page depth | <3 clicks to any page |

### User Feedback Signals

**Positive Indicators:**
- Low support ticket volume for documented procedures
- High doc page views, low bounce rate
- Positive feedback ("This was helpful")

**Negative Indicators:**
- Same questions repeatedly asked
- Users create workarounds instead of following docs
- Feedback: "Couldn't find what I needed"

---

## Summary: The Golden Rules

1. **Structure beats prose** - Organization matters more than writing style
2. **Scan-optimized** - Headings, lists, code blocks, white space
3. **Front-load** - Most important information first
4. **Be specific** - Real paths, real commands, real values
5. **One concept per paragraph** - Never mix multiple ideas
6. **Active voice** - "Edit the file" not "The file should be edited"
7. **Test it** - Have someone unfamiliar try to follow it
8. **Update regularly** - Outdated docs are worse than no docs

---

## Further Reading

**Books:**
- *Docs for Developers* by Jared Bhatti et al.
- *The Product is Docs* by Christopher Gales, Splunk
- *Modern Technical Writing* by Andrew Etter

**Style Guides:**
- Google Developer Documentation Style Guide
- Microsoft Writing Style Guide
- Company Style Guide
- GitLab Documentation Style Guide

**Online Resources:**
- [Write the Docs](https://www.writethedocs.org/)
- [The Good Docs Project](https://thegooddocsproject.dev/)
- [Divio Documentation System](https://documentation.divio.com/)

---

**Document Status:** Reference Guide
**Maintenance:** Review quarterly for updates
**Feedback:** Submit improvements via issues/PRs
