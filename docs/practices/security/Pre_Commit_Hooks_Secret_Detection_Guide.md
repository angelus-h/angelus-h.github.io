# Pre-Commit Hooks for Secret Detection

**Category:** Security / Development Tools  
**Status:** Production Ready  
**Last Updated:** 2026-05-27

---

## Overview

Pre-commit hooks are scripts that run automatically **before** each git commit. They validate your changes and **block commits** if they detect problems like:

- API keys, tokens, passwords
- AWS/Cloud credentials
- Private SSH/GPG keys
- Internal hostnames, IP addresses
- Sensitive configuration data

**The goal:** Catch secrets **before** they enter git history, preventing leaks entirely.

---

## Why Pre-Commit Hooks?

### The Problem Without Hooks

**Typical leak scenario:**

```bash
# Day 1: You accidentally commit a secret
echo "API_KEY=abc123xyz789" > config.env
git add config.env
git commit -m "Add config"
git push origin main

# Day 2: You realize the mistake
❌ Secret is now in git history FOREVER
❌ Accessible to anyone with repo access
❌ Potentially exposed on GitHub/GitLab
❌ Requires complex git history rewrite to fix
```

---

### The Solution With Pre-Commit Hooks

**Protected workflow:**

```bash
# You try to commit a secret
echo "API_KEY=abc123xyz789" > config.env
git add config.env
git commit -m "Add config"

# Pre-commit hook runs automatically:
🛡️ Scanning config.env for secrets...
❌ SECRET DETECTED!
   File: config.env
   Line 1: API_KEY=abc123xyz789
   Pattern: High entropy string (possible API key)

COMMIT BLOCKED. Remove the secret and try again.

# Your commit is REJECTED - secret never enters git history
```

**Result:**
- ✅ Leak prevented automatically
- ✅ No git history contamination
- ✅ No complex cleanup needed
- ✅ You're forced to fix it immediately

---

## How Pre-Commit Hooks Work

### Git Hook Lifecycle

```
┌─────────────────────────────────────────────────┐
│ 1. You: git add file.txt                       │
│ 2. You: git commit -m "message"                │
│ 3. Git: Running .git/hooks/pre-commit...       │
│    ├─> Hook scans staged files                 │
│    ├─> If OK: ✅ Commit proceeds               │
│    └─> If FAIL: ❌ Commit blocked              │
│ 4. Result: Commit created OR rejected          │
└─────────────────────────────────────────────────┘
```

---

### Where Hooks Live

**Git hooks directory:**
```bash
your-repo/
└── .git/
    └── hooks/
        ├── pre-commit       # Runs before commit
        ├── pre-push         # Runs before push
        ├── commit-msg       # Validates commit message
        └── ...
```

**Pre-commit hook is just a script:**
```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Scanning for secrets..."
detect-secrets scan --baseline .secrets.baseline

if [ $? -ne 0 ]; then
  echo "❌ Secrets detected! Commit blocked."
  exit 1  # Non-zero exit = block commit
fi

exit 0  # Zero exit = allow commit
```

---

## Available Tools

### 1. rh-pre-commit (Red Hat Internal)

**Source:** Red Hat internal tool  
**Best for:** Red Hat employees working with Red Hat systems  
**License:** Proprietary (Red Hat internal)

#### Installation

```bash
# Install from Red Hat internal PyPI
pip3 install --user rh-pre-commit

# Activate in a repository
cd ~/projects/your-repo
rh-pre-commit install

# Verify
ls -la .git/hooks/pre-commit
```

#### What It Detects

- ✅ Red Hat specific API keys
- ✅ BREW credentials
- ✅ Datadog API keys
- ✅ AWS credentials
- ✅ GitHub/GitLab tokens
- ✅ Private SSH/GPG keys
- ✅ Internal Red Hat hostnames
- ✅ High entropy strings (generic secrets)

#### Configuration

**Default behavior:** Scans all staged files

**Custom configuration (optional):**
```bash
# ~/.config/rh-pre-commit/config.yaml
exclude_patterns:
  - "*.md"      # Skip markdown files
  - "test/*"    # Skip test directory

sensitivity: high  # high, medium, low
```

#### Bypassing (Emergency Only)

```bash
# Skip pre-commit hook (USE CAREFULLY!)
git commit --no-verify -m "Emergency fix"
```

**⚠️ WARNING:** Only use `--no-verify` if you're 100% sure no secrets are present.

---

### 2. detect-secrets (Yelp - Open Source)

**Source:** https://github.com/Yelp/detect-secrets  
**Best for:** General purpose secret detection  
**License:** Apache 2.0 (Open Source)

#### Installation

```bash
# Install
pip3 install --user detect-secrets

# Initialize in repository
cd ~/projects/your-repo
detect-secrets scan > .secrets.baseline

# Add baseline to git
git add .secrets.baseline
git commit -m "Add secrets baseline"
```

#### Setup Pre-Commit Hook

**Method 1: Using pre-commit framework**

```bash
# Install pre-commit framework
pip3 install --user pre-commit

# Create .pre-commit-config.yaml
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
EOF

# Install hooks
pre-commit install
```

**Method 2: Manual hook script**

```bash
# Create pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
detect-secrets scan --baseline .secrets.baseline
if [ $? -ne 0 ]; then
  echo "❌ Secrets detected! Run: detect-secrets scan --update .secrets.baseline"
  exit 1
fi
EOF

chmod +x .git/hooks/pre-commit
```

#### Auditing Detected Secrets

```bash
# Review potential secrets
detect-secrets audit .secrets.baseline

# Interactive audit interface:
# - Mark as real secret (will block commits)
# - Mark as false positive (will ignore)
```

#### What It Detects

- ✅ AWS keys
- ✅ Azure keys
- ✅ GCP keys
- ✅ Slack tokens
- ✅ Stripe keys
- ✅ Private keys (PEM, SSH)
- ✅ High entropy strings
- ✅ Base64 encoded strings

#### Custom Plugins

```python
# my_custom_detector.py
from detect_secrets.plugins.base import RegexBasedDetector

class MyAPIKeyDetector(RegexBasedDetector):
    secret_type = 'My API Key'
    
    denylist = [
        re.compile(r'my_api_key_[a-zA-Z0-9]{32}'),
    ]
```

---

### 3. git-secrets (AWS - Open Source)

**Source:** https://github.com/awslabs/git-secrets  
**Best for:** AWS-focused projects  
**License:** Apache 2.0 (Open Source)

#### Installation

```bash
# macOS
brew install git-secrets

# Linux (manual)
git clone https://github.com/awslabs/git-secrets.git
cd git-secrets
sudo make install
```

#### Setup

```bash
cd ~/projects/your-repo

# Install hooks
git secrets --install

# Register AWS patterns
git secrets --register-aws

# Add custom patterns
git secrets --add 'api_key.*=.*[A-Za-z0-9]{32}'
```

#### What It Detects

- ✅ AWS Access Key ID
- ✅ AWS Secret Access Key
- ✅ AWS Session Token
- ✅ Custom regex patterns

#### Scanning Existing History

```bash
# Scan entire git history
git secrets --scan-history

# Scan specific branch
git secrets --scan-history origin/main
```

---

### 4. gitleaks (Open Source)

**Source:** https://github.com/gitleaks/gitleaks  
**Best for:** Comprehensive scanning, CI/CD integration  
**License:** MIT (Open Source)

#### Installation

```bash
# macOS
brew install gitleaks

# Linux
wget https://github.com/gitleaks/gitleaks/releases/download/v8.18.0/gitleaks_8.18.0_linux_x64.tar.gz
tar -xzf gitleaks_8.18.0_linux_x64.tar.gz
sudo mv gitleaks /usr/local/bin/
```

#### Pre-Commit Integration

```bash
# Using pre-commit framework
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.0
    hooks:
      - id: gitleaks
EOF

pre-commit install
```

#### Standalone Usage

```bash
# Scan current changes
gitleaks detect --source . --verbose

# Scan git history
gitleaks detect --source . --log-opts="--all"

# Generate report
gitleaks detect --source . --report-format json --report-path report.json
```

#### Custom Rules

```toml
# .gitleaks.toml
title = "Custom gitleaks config"

[[rules]]
id = "my-api-key"
description = "My Company API Key"
regex = '''mycompany_api_key_[a-zA-Z0-9]{40}'''

[[rules]]
id = "internal-hostname"
description = "Internal hostname"
regex = '''\.internal\.mycompany\.com'''
```

---

## Comparison Matrix

| Feature | rh-pre-commit | detect-secrets | git-secrets | gitleaks |
|---------|---------------|----------------|-------------|----------|
| **License** | Proprietary (RH) | Apache 2.0 | Apache 2.0 | MIT |
| **Maintenance** | Red Hat | Yelp | AWS | Gitleaks org |
| **Install** | `pip install` | `pip install` | Manual/brew | Binary/brew |
| **Speed** | Fast | Fast | Fast | Very Fast (Go) |
| **Accuracy** | High (RH tuned) | High | Medium | Very High |
| **False Positives** | Low | Medium | High | Low |
| **Custom Rules** | Limited | Yes (Python) | Yes (regex) | Yes (TOML) |
| **Baseline** | No | Yes | No | Yes |
| **History Scan** | No | No | Yes | Yes |
| **CI/CD** | No | Yes | Yes | Yes |
| **AWS Focus** | No | No | Yes | No |
| **Red Hat Focus** | Yes | No | No | No |

---

## Recommended Setup by Use Case

### Use Case 1: Red Hat Employee, Red Hat Projects

**Recommended:** `rh-pre-commit`

```bash
pip3 install --user rh-pre-commit
cd ~/work/red-hat-project
rh-pre-commit install
```

**Why:**
- Optimized for Red Hat secrets
- Knows Red Hat internal patterns
- Lower false positive rate for RH work
- Officially supported

---

### Use Case 2: Personal Projects (No Red Hat Work)

**Recommended:** `detect-secrets` + `pre-commit framework`

```bash
pip3 install --user pre-commit detect-secrets
cd ~/projects/personal-project

# Initialize
detect-secrets scan > .secrets.baseline

# Setup pre-commit
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
EOF

pre-commit install
git add .secrets.baseline .pre-commit-config.yaml
git commit -m "Add secret detection"
```

**Why:**
- Open source
- Flexible baseline system
- Good accuracy
- Widely used

---

### Use Case 3: AWS-Heavy Projects

**Recommended:** `git-secrets`

```bash
brew install git-secrets  # or manual install
cd ~/projects/aws-project
git secrets --install
git secrets --register-aws
```

**Why:**
- AWS-specific patterns
- Built by AWS team
- Comprehensive AWS coverage

---

### Use Case 4: CI/CD Pipeline Integration

**Recommended:** `gitleaks`

```bash
# In CI/CD pipeline
docker run --rm -v $(pwd):/repo zricethezav/gitleaks:latest \
  detect --source /repo --verbose --report-format json
```

**Why:**
- Fast (written in Go)
- Standalone binary
- JSON output for automation
- Excellent CI/CD integration

---

### Use Case 5: Mixed (Red Hat + Personal)

**Recommended:** Both `rh-pre-commit` AND `detect-secrets`

```bash
# Red Hat repos
cd ~/work/red-hat-project
rh-pre-commit install

# Personal repos
cd ~/projects/personal-project
detect-secrets scan > .secrets.baseline
pre-commit install  # with detect-secrets config
```

**Why:**
- Best of both worlds
- Use right tool for each project
- No conflicts between tools

---

## Best Practices

### 1. Never Bypass Unless Absolutely Necessary

```bash
# ❌ BAD - Routine bypass
git commit --no-verify -m "Quick fix"

# ✅ GOOD - Only in emergencies
# Emergency production fix, secret verified absent
git commit --no-verify -m "Hotfix: Fix critical bug"
```

---

### 2. Audit Baselines Regularly

```bash
# Review baseline quarterly
detect-secrets audit .secrets.baseline

# Remove outdated entries
# Mark new false positives
```

---

### 3. Combine Multiple Layers

**Defense in depth:**

```
Layer 1: Pre-commit hook (local)
Layer 2: CI/CD scan (server)
Layer 3: GitHub Secret Scanning (remote)
Layer 4: Periodic repo audits
```

---

### 4. Document Exceptions

```bash
# .secrets.baseline or README.md
# Known false positives:
# - test/fixtures/sample_api_key.txt (test data, not real)
# - docs/examples/config.yaml (placeholder values)
```

---

### 5. Use Environment Variables

**Never commit secrets, even in examples:**

```yaml
# ❌ BAD - config.yaml
api_key: abc123xyz789

# ✅ GOOD - config.yaml
api_key: ${API_KEY}
```

```bash
# ✅ GOOD - .env (in .gitignore)
export API_KEY="abc123xyz789"
```

---

### 6. Template Files, Not Real Configs

```bash
# Commit: config.yaml.example
api_key: YOUR_API_KEY_HERE
database_password: YOUR_PASSWORD_HERE

# .gitignore: config.yaml (actual file)
```

**Setup instructions:**
```bash
cp config.yaml.example config.yaml
# Edit config.yaml with real values
```

---

### 7. Enable Hook in All Repos

**Global template for new repos:**

```bash
# Create global git template
mkdir -p ~/.git-template/hooks

# Add pre-commit hook
cat > ~/.git-template/hooks/pre-commit << 'EOF'
#!/bin/bash
if command -v detect-secrets &> /dev/null; then
  detect-secrets scan --baseline .secrets.baseline 2>/dev/null
fi
EOF

chmod +x ~/.git-template/hooks/pre-commit

# Configure git to use template
git config --global init.templateDir '~/.git-template'
```

**Now all new repos get pre-commit hook automatically!**

---

## Troubleshooting

### Issue: Hook Not Running

**Symptom:** Commit succeeds even with secrets

**Check:**
```bash
# 1. Verify hook exists
ls -la .git/hooks/pre-commit

# 2. Check if executable
file .git/hooks/pre-commit

# 3. Test manually
.git/hooks/pre-commit
```

**Fix:**
```bash
# Make executable
chmod +x .git/hooks/pre-commit

# Reinstall
rh-pre-commit install
# or
pre-commit install
```

---

### Issue: Too Many False Positives

**Symptom:** Hook blocks legitimate commits

**Solution 1: Update baseline (detect-secrets)**
```bash
detect-secrets scan --update .secrets.baseline
detect-secrets audit .secrets.baseline
# Mark false positives as "No"
```

**Solution 2: Add exclusions**
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        exclude: 'test/.*|docs/examples/.*'
```

---

### Issue: Hook Slows Down Commits

**Symptom:** `git commit` takes 10+ seconds

**Solution: Limit scan scope**
```bash
# Only scan staged files, not entire repo
detect-secrets scan --staged-files-only
```

**Or use faster tool:**
```bash
# gitleaks is faster for large repos
gitleaks detect --source . --staged
```

---

### Issue: Need Emergency Bypass

**Scenario:** Production down, need immediate fix, hook blocking

**Safe bypass:**
```bash
# 1. Verify NO secrets in your changes
git diff --staged

# 2. Bypass hook
git commit --no-verify -m "Emergency: Fix production outage"

# 3. IMMEDIATELY after: Scan manually
detect-secrets scan
```

**Document why:**
```bash
# In commit message or follow-up
git commit --amend -m "Emergency: Fix production outage

Hook bypassed due to P1 incident.
Manual scan conducted post-commit - no secrets present.
Incident: INC-12345"
```

---

## Integration Examples

### GitHub Actions

```yaml
# .github/workflows/secret-scan.yml
name: Secret Scan

on: [push, pull_request]

jobs:
  gitleaks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - name: Run Gitleaks
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

### GitLab CI

```yaml
# .gitlab-ci.yml
secret_scan:
  stage: test
  image: zricethezav/gitleaks:latest
  script:
    - gitleaks detect --source . --verbose --report-format json
  artifacts:
    reports:
      gitleaks: gitleaks-report.json
  only:
    - merge_requests
    - main
```

---

### Pre-Commit Framework (Multi-Tool)

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
  
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.0
    hooks:
      - id: gitleaks
  
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: check-added-large-files
      - id: check-yaml
      - id: end-of-file-fixer
```

---

## Testing Your Setup

### Test 1: Detect API Key

```bash
# Create test file with fake API key
echo "api_key: test_abc123def456ghi789jkl012" > test-secret.txt

# Stage file
git add test-secret.txt

# Try to commit (should FAIL)
git commit -m "Test secret detection"

# Expected: ❌ Commit blocked
# Clean up
git reset HEAD test-secret.txt
rm test-secret.txt
```

---

### Test 2: Detect AWS Credentials

```bash
# Create test AWS config
cat > test-aws.txt << 'EOF'
AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
EOF

# Try to commit (should FAIL)
git add test-aws.txt
git commit -m "Test AWS detection"

# Clean up
git reset HEAD test-aws.txt
rm test-aws.txt
```

---

### Test 3: Verify Bypass Works

```bash
# Create secret
echo "secret: abc123" > bypass-test.txt
git add bypass-test.txt

# Bypass hook
git commit --no-verify -m "Test bypass"

# Expected: ✅ Commit succeeds

# Clean up
git reset --hard HEAD~1
rm bypass-test.txt
```

---

## Real-World Incident: Case Study

**Context:** Datadog API key leaked to GitHub

**Timeline:**
1. Developer documented Datadog Agent config
2. Accidentally included real API key instead of placeholder
3. Committed and pushed to GitHub
4. InfoSec detected leak via Datadog notification
5. API key revoked
6. Git history cleaned (8 hours of work)

**Cost:**
- 8 hours remediation time
- InfoSec investigation overhead
- Potential security exposure
- Team trust impact

**Prevention if pre-commit hook was active:**
```bash
# Developer tries to commit
git add Datadog-Agent-Configuration.md
git commit -m "Add Datadog config"

# Pre-commit hook runs
🛡️ Scanning for secrets...
❌ SECRET DETECTED!
   File: Datadog-Agent-Configuration.md
   Line 159: api_key: ad1c08df3ba84bb8a68f2ff06a85a4dd
   Pattern: Datadog API key

COMMIT BLOCKED.
Replace with placeholder: api_key: ${DATADOG_API_KEY}

# Developer fixes immediately
# TOTAL TIME: 30 seconds
# NO LEAK, NO CLEANUP, NO INCIDENT
```

**Lesson:** Pre-commit hooks prevent 99% of secret leaks **before** they happen.

---

## Maintenance Checklist

### Weekly
- [ ] Review any bypassed commits (`git log --grep="no-verify"`)

### Monthly
- [ ] Update tool versions
  ```bash
  pip install --upgrade detect-secrets rh-pre-commit
  pre-commit autoupdate
  ```

### Quarterly
- [ ] Audit baseline for false positives
  ```bash
  detect-secrets audit .secrets.baseline
  ```
- [ ] Review and update custom patterns
- [ ] Scan git history for historical leaks
  ```bash
  gitleaks detect --source . --log-opts="--all"
  ```

### Annually
- [ ] Full repository secret audit
- [ ] Review and update security policies
- [ ] Team training on secret management

---

## Resources

**Official Documentation:**
- rh-pre-commit: https://source.redhat.com (internal)
- detect-secrets: https://github.com/Yelp/detect-secrets
- git-secrets: https://github.com/awslabs/git-secrets
- gitleaks: https://github.com/gitleaks/gitleaks
- pre-commit framework: https://pre-commit.com

**Related Guides:**
- [Git Secret Leak Remediation Guide](Git_Secret_Leak_Remediation_Guide.md)
- GitHub Secret Scanning: https://docs.github.com/en/code-security/secret-scanning
- OWASP Secret Management: https://owasp.org/www-community/vulnerabilities/Use_of_hard-coded_password

---

## Summary

**Pre-commit hooks are your first line of defense against secret leaks.**

**Key Takeaways:**
1. ✅ Install a pre-commit hook in EVERY repository
2. ✅ Use rh-pre-commit for Red Hat work, detect-secrets for personal projects
3. ✅ Never bypass unless absolutely necessary (`--no-verify`)
4. ✅ Combine with CI/CD scanning for defense in depth
5. ✅ Use environment variables and templates, not hardcoded secrets
6. ✅ Test your setup regularly
7. ✅ Update tools quarterly

**Bottom line:** 5 minutes of setup prevents hours of incident response.

---

**Last Updated:** 2026-05-27  
**Next Review:** 2026-08-27 (quarterly)  
**Status:** Production Ready - Battle-tested in real incident
