# Claude Code Installation Guide

**Last Updated:** 2026-03-20
**Audience:** the company Employees
**Platform:** Linux, macOS, Windows (WSL/PowerShell)

---

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Installation Steps](#installation-steps)
4. [Optional: Install Additional Components](#optional-install-additional-google-cloud-components)
5. [Configuration](#configuration)
6. [Verification](#verification)
7. [Troubleshooting](#troubleshooting)
8. [Advanced Topics](#advanced-topics)
9. [Getting Help](#getting-help)
10. [Quick Reference](#quick-reference)

---

## Installation at a Glance

!!! success "Quick Summary - 9 Steps"
1. **Read & Submit** compliance form (the company policies)
2. **Install** Google Cloud CLI (platform-specific)
3. **Initialize** gcloud with the company email
4. **Find** your GCP Project ID (team-specific)
5. **Authenticate** with GCP (application default)
6. **Set** environment variables (VERTEX, REGION, PROJECT_ID)
7. **Persist** config to ~/.bashrc or ~/.zshrc
8. **Install** Claude Code CLI
9. **Launch** from your project directory

**Total Time:** ~15-20 minutes

---

## Overview

**Claude Code** is a CLI tool powered by the Claude AI model that enables you to code directly within your terminal. It provides intelligent assistance for:

- Building features from natural language descriptions
- Debugging code and resolving issues
- Navigating complex codebases
- Automating repetitive development tasks

---

## Prerequisites

### Required Reading & Compliance

!!! warning "Mandatory Form Submission"
**Before installing Claude Code**, you MUST submit [this compliance form](https://docs.google.com/forms/d/e/1FAIpQLSdIphsk9TlTR-TPSsk9xiNLqmgSCJJ2BLTOWLMM667X1vmsMg/viewform) confirming you have reviewed the following materials:

**the company Policies:**
- [Guidelines for Responsible Use of AI Code Assistants](https://source.company.internal/projects_and_programs/ai/wiki/code_assistants_guidelines_for_responsible_use_of_ai_code_assistants)
- [Legal Guidelines for Using Code Assistant Tools](https://docs.google.com/presentation/d/1y_rgaL4lnXfrcxagc7UqAUeqvQr96CZ47lCJsOczX7k/edit?slide=id.g30e84f14e68_0_6149#slide=id.g30e84f14e68_0_6149)
- [the company Policy on the Use of AI Technology](https://source.company.internal/departments/legal/legal_compliance_ethics/compliance_folder/policy_on_the_use_of_ai_technologypdf)
- [Guidelines on Use of AI Generated Content](https://source.company.internal/departments/legal/legal_compliance_ethics/compliance_folder/appendix_1_to_policy_on_the_use_of_ai_technologypdf)

**Anthropic Documentation:**
- [Claude Code Best Practices for Agentic Coding](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Anthropic's Usage Policy](https://www.anthropic.com/legal/aup)
- [Claude Code User Guide](https://docs.anthropic.com/en/docs/claude-code/overview#what-claude-code-does-for-you)

### System Requirements

- **Operating System:** Linux (RHEL 7-10, Fedora 41-42), macOS, or Windows (WSL/PowerShell)
- **Python:** Version 3.10 to 3.14 (included in Google Cloud SDK installer for macOS)
- **Internet Connection:** Required for installation and authentication
- **the company Email:** Active the company email account
- **GCP Access:** Permission to the company's GCP projects

**Check Python Version:**

```bash
# Linux/macOS
python3 -V

# Should output: Python 3.10.x or higher (up to 3.14.x)
```

---

## Installation Steps

### Step 1: Install Google Cloud CLI

Install the Google Cloud SDK based on your operating system:

=== "Linux (RHEL 7/8/9)"

**Prerequisites:**
- the company Enterprise Linux 7, 8, or 9
- Python 3.10 to 3.14

**Add Repository:**

```bash
# Add the Cloud SDK distribution URI as a package source
sudo tee -a /etc/yum.repos.d/google-cloud-sdk.repo << EOM
[google-cloud-cli]
name=Google Cloud CLI
baseurl=https://packages.cloud.google.com/yum/repos/cloud-sdk-el9-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOM
```

**Install Cloud SDK:**

```bash
# Install required dependencies
sudo dnf install libxcrypt-compat.x86_64

# Install Google Cloud CLI
sudo dnf install google-cloud-cli
```

=== "Linux (RHEL 10)"

**Prerequisites:**
- the company Enterprise Linux 10
- Python 3.10 to 3.14

**Add Repository:**

```bash
# Add RHEL 10 specific repository
sudo tee -a /etc/yum.repos.d/google-cloud-sdk.repo << EOM
[google-cloud-cli]
name=Google Cloud CLI
baseurl=https://packages.cloud.google.com/yum/repos/cloud-sdk-el10-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key-v10.gpg
EOM
```

**Install Cloud SDK:**

```bash
# Install required dependencies
sudo dnf install libxcrypt-compat.x86_64

# Install Google Cloud CLI
sudo dnf install google-cloud-cli
```

=== "Fedora"

**Prerequisites:**
- Fedora 41 or 42
- Python 3.10 to 3.14

**Add Repository:**

```bash
# Use RHEL 9 repository (compatible with Fedora)
sudo tee -a /etc/yum.repos.d/google-cloud-sdk.repo << EOM
[google-cloud-cli]
name=Google Cloud CLI
baseurl=https://packages.cloud.google.com/yum/repos/cloud-sdk-el9-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOM
```

**Install Cloud SDK:**

```bash
# Install required dependencies
sudo dnf install libxcrypt-compat.x86_64

# Install Google Cloud CLI
sudo dnf install google-cloud-cli
```

=== "macOS (Intel x86_64)"

**Prerequisites:**
- Python 3.10 to 3.14 (check with `python3 -V`)
- macOS with Intel processor

**Download and Install:**

```bash
# Download Cloud SDK for x86_64
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-darwin-x86_64.tar.gz

# Extract archive
tar -xf google-cloud-cli-darwin-x86_64.tar.gz

# Run installation script
./google-cloud-sdk/install.sh

# Restart your shell
exec -l $SHELL
```

=== "macOS (Apple Silicon ARM64)"

**Prerequisites:**
- Python 3.10 to 3.14 (check with `python3 -V`)
- macOS with Apple Silicon (M1/M2/M3)

**Download and Install:**

```bash
# Download Cloud SDK for ARM64
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-darwin-arm.tar.gz

# Extract archive
tar -xf google-cloud-cli-darwin-arm.tar.gz

# Run installation script
./google-cloud-sdk/install.sh

# Restart your shell
exec -l $SHELL
```

=== "Windows (WSL)"

Use the **Linux (RHEL/Fedora)** instructions above within Windows Subsystem for Linux.

!!! tip "WSL Users"
If you're using WSL with Ubuntu/Debian, you may need to use `apt` instead of `dnf`. Check Google Cloud documentation for Debian-based instructions.

### Step 2: Initialize Google Cloud SDK

```bash
# Initialize gcloud
gcloud init
```

**During initialization:**

1. **Sign in:**
- The command opens a web browser for authorization
- Sign in with your **the company email account** (@company.com)
- Grant the requested permissions

2. **Select Configuration:**
- Choose to create a new configuration or reinitialize existing

3. **Select Project:**
- When prompted, enter `[1]` to configure a default project
- You'll set the actual project in Step 3

!!! tip "Command-Line Only Authentication"
If you prefer not to use a web browser, run:
```bash
gcloud init --console-only
```

**Verify Basic Installation:**

```bash
# List authenticated accounts
gcloud auth list

# View current configuration
gcloud config list

# Check gcloud version
gcloud version
```

### Step 3: Find Your GCP Project ID

!!! info "Finding Your Project ID"
Your project ID is based on your organizational reporting line.

**Steps:**

1. Open the [GCP Project ID Spreadsheet](https://docs.google.com/opsadsheets/d/1qWoCx3i5jZ-t6BUD-2AIdutk9sMmkytoXqjBXh2oi4U/edit?gid=0#gid=0)
2. Find the GCP name in **Column C** that aligns with your team
3. Look for the project associated with your **closest upline manager**
4. Note this project ID - you'll use it throughout the installation

!!! warning "Important"
Replace `GCP_PROJECT_ID` in all following commands with your actual project ID from the opsadsheet.

### Step 4: Set Up Default Authentication

```bash
# Set up application default login (may show an error - continue anyway)
gcloud auth application-default login

# Set quota project (use exactly as shown - same for everyone)
gcloud auth application-default set-quota-project cloudability-it-gemini
```

!!! note
Leave `cloudability-it-gemini` as-is in the second command - this is static for all the company users.

### Step 5: Set Environment Variables

**Replace `GCP_PROJECT_ID` with your actual project ID from Step 3:**

```bash
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=GCP_PROJECT_ID # Replace with your actual project ID
```

### Step 6: Make Configuration Persistent

Make these environment variables permanent by adding them to your shell configuration:

=== "Linux (Bash)"

```bash
# Add to ~/.bashrc
echo 'export CLAUDE_CODE_USE_VERTEX=1' >> ~/.bashrc
echo 'export CLOUD_ML_REGION=us-east5' >> ~/.bashrc
echo 'export ANTHROPIC_VERTEX_PROJECT_ID=GCP_PROJECT_ID' >> ~/.bashrc # Replace GCP_PROJECT_ID

# Reload configuration
source ~/.bashrc
```

=== "macOS (Zsh)"

```bash
# Add to ~/.zshrc (default on macOS)
echo 'export CLAUDE_CODE_USE_VERTEX=1' >> ~/.zshrc
echo 'export CLOUD_ML_REGION=us-east5' >> ~/.zshrc
echo 'export ANTHROPIC_VERTEX_PROJECT_ID=GCP_PROJECT_ID' >> ~/.zshrc # Replace GCP_PROJECT_ID

# Reload configuration
source ~/.zshrc
```

### Step 7: Install Claude Code

Install Claude Code using the appropriate script for your platform:

=== "Linux / macOS / WSL"

```bash
# Download installation script
curl -fsSL https://claude.ai/install.sh -o claude-install.sh

# Make executable
chmod +x claude-install.sh

# Run installer
./claude-install.sh
```

=== "Windows (PowerShell)"

```powershell
# Download and run installation script
irm https://claude.ai/install.ps1 | Out-File claude-install.ps1
.\claude-install.ps1
```

### Step 8: Set Working Directory

!!! danger "Important"
Claude Code can only access files within the directory where you launch it. Navigate to your preferred working directory before launching.

```bash
# Navigate to your project directory
cd /path/to/your/project

# Or create a new directory
mkdir ~/my-claude-workspace
cd ~/my-claude-workspace
```

### Step 9: Launch Claude Code

```bash
claude
```

You should see the Claude Code CLI interface with a welcome message.

---

## Optional: Install Additional Google Cloud Components

Google Cloud SDK supports additional components that may be useful:

### kubectl (Kubernetes CLI)

```bash
# Install kubectl component
gcloud components install kubectl

# Verify installation
kubectl version --client
```

### Other Useful Components

```bash
# List all available components
gcloud components list

# Install specific components
gcloud components install app-engine-python   # App Engine for Python
gcloud components install app-engine-go     # App Engine for Go
gcloud components install cloud-sql-proxy    # Cloud SQL Proxy
gcloud components install beta          # Beta commands
gcloud components install alpha         # Alpha commands

# Update all installed components
gcloud components update
```

!!! note "Component Installation"
Installing components requires write access to the Cloud SDK installation directory. On Linux with package manager installation, you may need `sudo`.

---

## Configuration

### Example Shell Configuration

Your `~/.bashrc` or `~/.zshrc` should include these entries at the end:

```bash
# Claude Code Setup

# Google Cloud SDK paths (if not using RHEL/Fedora packages)
if [ -f "$HOME/google-cloud-sdk/path.bash.inc" ]; then
. "$HOME/google-cloud-sdk/path.bash.inc"
fi

if [ -f "$HOME/google-cloud-sdk/completion.bash.inc" ]; then
. "$HOME/google-cloud-sdk/completion.bash.inc"
fi

# Claude Code environment variables
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=your-actual-gcp-project-id # Replace with your project ID
```

---

## Verification

### Check Installation Status

Run the status command to verify your setup:

```bash
claude
```

Inside Claude Code, run:

```
/status
```

**Expected output should show:**

| Setting | Expected Value | Status |
|---------|----------------|--------|
| Provider | Google Vertex AI | |
| GCP Project | your-project-id | |
| Region | us-east5 | |
| Model | claude-sonnet-4-5 or claude-opus-4-6 | |

If you see **"Anthropic API"** instead of "Google Vertex AI", check your environment variables in Step 5.

### Initialize Project Documentation

Generate a `CLAUDE.md` file for your codebase:

```
/init
```

This creates project-specific documentation that helps Claude understand your codebase better.

### Basic Commands

| Command | Description |
|---------|-------------|
| `/status` | Show configuration and connection status |
| `/init` | Generate CLAUDE.md project documentation |
| `/help` | Display all available commands |
| `/commit` | Create a git commit with AI-generated message |
| `/clear` | Clear conversation history |
| `/exit` | Exit Claude Code |

---

## Troubleshooting

### Quick Diagnostic Flowchart

```
Claude not working?
│
├─ "command not found: claude"
│ └─> Run ./claude-install.sh again
│ └─> Check PATH in ~/.bashrc or ~/.zshrc
│
├─ "command not found: gcloud"
│ └─> Reinstall Google Cloud SDK (Step 1)
│ └─> Check PATH settings
│
├─ "Authentication failed"
│ └─> Run: gcloud auth application-default login
│ └─> Check the company email is used
│
├─ "Permission denied" or "Invalid project"
│ └─> Verify ANTHROPIC_VERTEX_PROJECT_ID is correct
│ └─> Check project ID in opsadsheet
│
├─ Provider shows "Anthropic API" not "Google Vertex AI"
│ └─> Check environment variables are set
│ └─> Run: echo $CLAUDE_CODE_USE_VERTEX (should be 1)
│ └─> Reload shell: source ~/.bashrc
│
└─ Other issues
└─> Run /status inside Claude
└─> Check #help-rh-code-assist on Slack
```

### Issue: Authentication Errors

**Error:** `Application default credentials not found`

**Solution:**

```bash
# Re-run authentication
gcloud auth application-default login
gcloud auth application-default set-quota-project cloudability-it-gemini
```

### Issue: Wrong GCP Project

**Error:** `Permission denied` or `Project not found`

**Solution:**

1. Verify your project ID in the [opsadsheet](https://docs.google.com/opsadsheets/d/1qWoCx3i5jZ-t6BUD-2AIdutk9sMmkytoXqjBXh2oi4U/edit?gid=0#gid=0)
2. Update environment variable:

```bash
export ANTHROPIC_VERTEX_PROJECT_ID=correct-project-id
# Also update in ~/.bashrc or ~/.zshrc
```

### Issue: Claude Not in PATH

**Error:** `claude: command not found`

**Solution:**

Restart your terminal or reload shell configuration:

```bash
# Bash
source ~/.bashrc

# Zsh (macOS)
source ~/.zshrc
```

### Issue: Provider Shows "Anthropic API" Instead of "Google Vertex AI"

**Error:** Using wrong provider

**Solution:**

Check environment variables are set:

```bash
echo $CLAUDE_CODE_USE_VERTEX # Should output: 1
echo $CLOUD_ML_REGION     # Should output: us-east5
echo $ANTHROPIC_VERTEX_PROJECT_ID # Should output: your-project-id
```

If not set, re-run Step 6 and reload your shell.

### Issue: Unable to Access Files

**Error:** `Permission denied` or `File not found`

**Solution:**

Claude Code can only access files in the directory where you launched it. Launch from your project root:

```bash
cd /path/to/your/project
claude
```

### Issue: Python Version Incompatible

**Error:** `Python 3.x.x is not supported`

**Solution:**

Google Cloud SDK requires Python 3.10 to 3.14:

```bash
# Check version
python3 -V

# If too old, install newer Python (RHEL/Fedora)
sudo dnf install python3.11

# macOS (using Homeservice-c)
service-c install python@3.11
```

### Issue: Repository GPG Key Error

**Error:** `GPG check FAILED`

**Solution:**

```bash
# Re-import GPG key
sudo rpm --import https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

# RHEL 10 uses different key
sudo rpm --import https://packages.cloud.google.com/yum/doc/rpm-package-key-v10.gpg
```

### Issue: gcloud Command Not Found After Installation

**Error:** `bash: gcloud: command not found`

**Solution:**

**For macOS/manual installation:**

```bash
# Add to PATH (if using manual installation)
export PATH=$PATH:$HOME/google-cloud-sdk/bin

# Add to shell config permanently
echo 'export PATH=$PATH:$HOME/google-cloud-sdk/bin' >> ~/.bashrc
source ~/.bashrc
```

**For Linux package installation:**

```bash
# Package should install to /usr/bin/gcloud automatically
# If not found, reinstall
sudo dnf reinstall google-cloud-cli
```

---

## Getting Help

### the company Internal Support

If you encounter issues:

1. **Slack Channel:** [#help-rh-code-assist](https://redhat.enterprise.slack.com/archives/C077DM5ST6W)
2. **Documentation:** Check the [Claude Code User Guide](https://docs.anthropic.com/en/docs/claude-code/overview)
3. **GCP Issues:** Contact your team's GCP administrator

### Useful Resources

- **the company AI Hub:** [source.company.internal/projects_and_programs/ai](https://source.company.internal/projects_and_programs/ai)
- **Anthropic Best Practices:** [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)
- **Google Cloud SDK:** [cloud.google.com/sdk/docs](https://cloud.google.com/sdk/docs)

---

## Quick Reference

### Essential Commands (Inside Claude)

| Command | Description | Example Usage |
|---------|-------------|---------------|
| `/status` | Check configuration and connection | Verify GCP project and provider |
| `/init` | Initialize project documentation | Generate CLAUDE.md for codebase |
| `/commit` | Create git commit with AI message | Auto-generate commit from changes |
| `/help` | Show all available commands | List all / commands |
| `/clear` | Clear conversation history | Start fresh conversation |
| `/exit` | Exit Claude Code | Close CLI session |
| `/model` | Change AI model | Switch between Sonnet/Opus |
| `/config` | View/edit configuration | Change settings |

### Environment Variables

```bash
# Required for the company Claude Code via Vertex AI
export CLAUDE_CODE_USE_VERTEX=1
export CLOUD_ML_REGION=us-east5
export ANTHROPIC_VERTEX_PROJECT_ID=your-gcp-project-id

# Optional: Set default model
export CLAUDE_MODEL=claude-sonnet-4-5
```

### Launch Claude

```bash
# Navigate to project directory
cd /path/to/your/project

# Launch Claude Code
claude

# Or specify a different directory
claude --directory=/path/to/project
```

### Verify Installation Checklist

```bash
# 1. Check gcloud is installed
gcloud version

# 2. Check authentication
gcloud auth list

# 3. Check project is set
gcloud config get-value project

# 4. Check environment variables
echo $CLAUDE_CODE_USE_VERTEX      # Should be: 1
echo $CLOUD_ML_REGION         # Should be: us-east5
echo $ANTHROPIC_VERTEX_PROJECT_ID   # Should be: your-project-id

# 5. Launch Claude
claude
# Then inside Claude: /status
```

### Common File Locations

| File | Purpose | Location |
|------|---------|----------|
| gcloud config | Google Cloud SDK settings | `~/.config/gcloud/` |
| Shell config | Environment variables | `~/.bashrc` or `~/.zshrc` |
| Claude cache | Conversation history | `~/.claude/` |
| Project docs | Generated by /init | `CLAUDE.md` in project root |

---

## Advanced Topics

### Multiple GCP Projects

If you work with multiple GCP projects:

```bash
# List all configurations
gcloud config configurations list

# Create new configuration for different project
gcloud config configurations create project-two
gcloud config set project OTHER-PROJECT-ID

# Switch between configurations
gcloud config configurations activate default
gcloud config configurations activate project-two

# Set project for current configuration
gcloud config set project YOUR-PROJECT-ID
```

### Using Claude Code in Different Directories

Claude Code creates a separate conversation context per directory:

```bash
# Project 1
cd ~/projects/frontend
claude # Separate context

# Project 2 (different terminal)
cd ~/projects/backend
claude # Different context
```

### IDE Integration

Run Claude Code in your IDE's integrated terminal for seamless workflow:

**VS Code:**
1. Open integrated terminal: `` Ctrl+` ``
2. Navigate to project root
3. Run `claude`

**JetBrains IDEs (IntelliJ, PyCharm):**
1. Open terminal: `Alt+F12` (Linux) or `⌥F12` (Mac)
2. Navigate to project root
3. Run `claude`

### Accessibility Features

Enable screen reader support:

```bash
gcloud config set accessibility/screen_reader true
```

### Offline Documentation

Access gcloud help without internet:

```bash
# Get command help
gcloud help

# Get help for specific command
gcloud help auth

# List all commands
gcloud help --help
```

---

**Happy Coding with Claude!** 

For questions or issues, reach out on [#help-rh-code-assist](https://redhat.enterprise.slack.com/archives/C077DM5ST6W).
