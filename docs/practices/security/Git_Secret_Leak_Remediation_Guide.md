# Git Secret Leak Remediation Guide

**Category:** Security Incident Response  
**Status:** Production Ready  
**Last Updated:** 2026-05-27

---

## Overview

What to do when you accidentally commit sensitive data (API keys, passwords, tokens) to a git repository and push it to GitHub/GitLab.

**CRITICAL:** Simply deleting the file in a new commit **DOES NOT REMOVE IT FROM GIT HISTORY**. The secret remains accessible in old commits forever.

This guide shows how to **completely erase** secrets from git history using `git-filter-repo`.

---

## When This Guide Applies

**Use this guide if:**
- ✅ You committed API keys, tokens, passwords, or credentials
- ✅ You pushed to GitHub/GitLab (public or private)
- ✅ The secret is in git history (even if deleted in latest commit)
- ✅ You want to completely remove all traces

**Don't use this guide if:**
- ❌ Secret never reached remote (still local only) → just `git reset --hard`
- ❌ Repository is shared with many people → coordinate first
- ❌ Secret is in someone else's fork → contact them, revoke the secret

---

## The Problem: Why Deleting Files Isn't Enough

**Scenario:**
```bash
# Day 1: You commit a config file with API key
git add config.yaml
git commit -m "Add Datadog config"
git push origin main

# Day 2: You realize the mistake and delete it
git rm config.yaml
git commit -m "Remove config file"
git push origin main
```

**What you think:** "The file is gone from GitHub."

**Reality:** The file is **still accessible** at commit `abc123`:
```bash
# Anyone can still access it:
git checkout abc123
cat config.yaml  # API key is still here!

# Or view it on GitHub:
https://github.com/user/repo/blob/abc123/config.yaml
```

**The secret is in EVERY commit between Day 1 and Day 2.**

---

## The Solution: Rewrite Git History

**Tool:** `git-filter-repo` (faster, safer than `git filter-branch`)

**What it does:**
- Removes the file from **ALL commits** in history
- Rewrites commit hashes
- Makes it as if the file **never existed**

**Warning:** This rewrites history. Anyone who cloned the repo will need to re-clone.

---

## Step-by-Step Remediation

### Phase 1: IMMEDIATE - Revoke the Secret

**BEFORE touching git, revoke the compromised credential:**

1. **API Keys:** Revoke in service dashboard (Datadog, AWS, etc.)
2. **Passwords:** Change immediately
3. **OAuth tokens:** Revoke and regenerate
4. **SSH keys:** Delete from authorized keys, generate new

**Why first:** Even after cleaning git, the secret was exposed. Assume compromised.

---

### Phase 2: Prepare for History Rewrite

#### 1. Create Full Backup

```bash
cd ~/projects/your-repo
cp -r .git .git.backup
echo "Backup created: .git.backup"
```

**Why:** If something goes wrong, you can restore:
```bash
rm -rf .git
mv .git.backup .git
```

#### 2. Install git-filter-repo

```bash
pip3 install --user git-filter-repo
```

**Verify installation:**
```bash
git-filter-repo --version
```

---

### Phase 3: Remove File from Git History

#### 1. Remove the file from ALL commits

```bash
cd ~/projects/your-repo

git filter-repo \
  --path path/to/secret-file.yaml \
  --invert-paths \
  --force
```

**What this does:**
- `--path` - which file to remove
- `--invert-paths` - **reverse logic**: remove this file (not "keep only this file")
- `--force` - required because this is destructive

**Example:**
```bash
git filter-repo \
  --path docs/config/datadog-agent.yaml \
  --invert-paths \
  --force
```

**Output:**
```
NOTICE: Removing 'origin' remote; see 'Why is my origin removed?'
Parsed 21 commits
New history written in 0.10 seconds; now repacking/cleaning...
Repacking your repo and cleaning out old unneeded objects
Completely finished after 0.34 seconds.
```

**Note:** `git-filter-repo` automatically removes the `origin` remote for safety.

---

#### 2. Verify the file is gone from history

```bash
# Search for the file in ALL commits
git log --all --full-history -- path/to/secret-file.yaml

# This should return NOTHING (empty output)
```

**If you see commits:** The file is still in history. Check your command.

**If empty:** ✅ File successfully removed from all commits.

---

### Phase 4: Force Push to GitHub/GitLab

#### 1. Add back the remote

```bash
# Check current remotes (should be empty)
git remote -v

# Add origin back (get URL from backup or GitHub)
git remote add origin git@github.com:user/repo.git

# Verify
git remote -v
```

**Get original URL from backup:**
```bash
cat .git.backup/config | grep -A2 "\[remote"
```

---

#### 2. Force push to overwrite remote history

```bash
# Force push ALL branches
git push origin --force --all

# Force push ALL tags
git push origin --force --tags
```

**Example output:**
```
Enumerating objects: 1003, done.
Counting objects: 100% (1003/1003), done.
Writing objects: 100% (1003/1003), 18.46 MiB | 2.52 MiB/s, done.
To github.com:user/repo.git
 + a6617de...b1a94c5 main -> main (forced update)
```

**What this does:**
- Overwrites GitHub/GitLab with your cleaned history
- Old commits with secrets are **permanently gone** from GitHub

---

### Phase 5: Verify Success

#### 1. Check GitHub/GitLab

Browse to old commit URL where secret existed:
```
https://github.com/user/repo/blob/OLD_COMMIT_HASH/path/to/secret-file.yaml
```

**Expected:** `404 Not Found` or "This commit does not exist"

---

#### 2. Clone fresh and verify

```bash
cd /tmp
git clone git@github.com:user/repo.git test-clone
cd test-clone

# Search for the secret in ALL history
git log --all --full-history --source -- path/to/secret-file.yaml

# Should return: NOTHING
```

---

## Advanced: Remove Multiple Files

**Remove multiple files at once:**
```bash
git filter-repo \
  --path path/to/secret1.yaml \
  --path config/secret2.json \
  --path credentials/api-keys.txt \
  --invert-paths \
  --force
```

---

## Advanced: Remove by Content (Not File Path)

**If secret appears in MULTIPLE files, use replace:**

```bash
# Create a file with replacements
cat > replacements.txt << 'EOF'
old_api_key_abc123==>[REDACTED]
secret_password_xyz789==>[REDACTED]
EOF

git filter-repo --replace-text replacements.txt --force
```

**This replaces the text in ALL commits, ALL files.**

---

## What Happens to Collaborators?

**If others have cloned the repo:**

Their local clones still contain the old history with secrets.

**They must:**
1. Delete their local clone
2. Re-clone from GitHub/GitLab

**OR:**
```bash
cd their-local-clone
git fetch origin
git reset --hard origin/main
```

**Warning:** This discards their local uncommitted changes.

---

## When Force Push Isn't Allowed

**GitHub/GitLab protected branches may block force push.**

**Solution:**

1. **Temporarily disable branch protection:**
   - GitHub: Settings → Branches → Edit protection rules
   - Disable "Do not allow force pushes"

2. **Force push:**
   ```bash
   git push origin --force --all
   ```

3. **Re-enable branch protection.**

---

## Common Errors and Fixes

### Error: "origin does not appear to be a git repository"

**Cause:** `git-filter-repo` removes `origin` remote automatically.

**Fix:**
```bash
git remote add origin git@github.com:user/repo.git
```

---

### Error: "refusing to update checked out branch"

**Cause:** You're trying to push to a bare/non-bare repo mismatch.

**Fix:** This shouldn't happen with GitHub/GitLab. Check remote URL:
```bash
git remote -v
```

---

### Error: "failed to push some refs"

**Cause:** Remote has commits you don't have locally.

**Fix:** You're doing a force push, this is expected. Add `--force`:
```bash
git push origin --force --all
```

---

## Security Checklist

After cleaning git history:

- [ ] **Revoked compromised secret** (API key, password, token)
- [ ] **Removed file from git history** (verified with `git log`)
- [ ] **Force pushed to GitHub/GitLab** (old commits gone)
- [ ] **Verified on GitHub:** Old commit URLs return 404
- [ ] **Fresh clone:** Secret not findable in any commit
- [ ] **Notified collaborators** to delete and re-clone
- [ ] **Rotated ALL secrets** in the affected file (not just leaked one)
- [ ] **Audited other files** for similar leaks
- [ ] **Updated .gitignore** to prevent future leaks
- [ ] **Implemented pre-commit hooks** for secret detection (optional)

---

## Prevention: Never Leak Secrets Again

### 1. Use .gitignore

**Always ignore sensitive files:**
```bash
# .gitignore
*.env
*.pem
*.key
secrets/
config/production.yaml
.venv/
```

**Verify before commit:**
```bash
git status
# Check that no sensitive files are staged
```

---

### 2. Use Environment Variables

**Bad:**
```yaml
# config.yaml (COMMITTED TO GIT)
api_key: abc123xyz789
```

**Good:**
```yaml
# config.yaml (COMMITTED TO GIT)
api_key: ${DATADOG_API_KEY}
```

```bash
# .env (NOT COMMITTED - in .gitignore)
export DATADOG_API_KEY="abc123xyz789"
```

---

### 3. Use Template Files

**Commit template, not actual config:**

```bash
# config.yaml.example (COMMITTED)
api_key: YOUR_API_KEY_HERE
database_password: YOUR_PASSWORD_HERE
```

```bash
# config.yaml (NOT COMMITTED - in .gitignore)
api_key: actual_key_abc123
database_password: actual_password_xyz789
```

**Setup instructions in README:**
```bash
cp config.yaml.example config.yaml
# Edit config.yaml with real values
```

---

### 4. Pre-commit Hooks (Advanced)

**Install secret detection tool:**
```bash
pip install detect-secrets
detect-secrets scan > .secrets.baseline
```

**Add to .git/hooks/pre-commit:**
```bash
#!/bin/bash
detect-secrets scan --baseline .secrets.baseline
if [ $? -ne 0 ]; then
  echo "❌ Secrets detected! Commit blocked."
  exit 1
fi
```

**Make executable:**
```bash
chmod +x .git/hooks/pre-commit
```

---

## Real-World Case Study

**Context:** Accidentally committed Datadog API keys to GitHub Pages repository.

**Incident Timeline:**

1. **Day 1:** Committed `Datadog-Agent-Configuration.md` with 3 real API keys
2. **Week 2:** File remained in git history across 20+ commits
3. **Month 3:** Red Hat Information Security detected the leak
4. **Day of discovery:** API keys revoked, git history cleaned

**Files affected:**
```
docs/learning/monitoring/Datadog-Agent-Configuration.md
  - Line 159: ad1c08df3ba84bb8a68f2ff06a85a4dd (Service-A)
  - Line 172: 5e8f9a2c1b3d4e5f6a7b8c9d0e1f2a3b (Service-B)
  - Line 185: 9bf5da6daf764350a4e2b95d0e670ab3 (Service-C)
```

**Remediation:**
```bash
# 1. Keys revoked by InfoSec team
# 2. Backup created
cp -r .git .git.backup

# 3. History cleaned
git filter-repo \
  --path docs/learning/monitoring/Datadog-Agent-Configuration.md \
  --invert-paths \
  --force

# 4. Remote re-added
git remote add origin git@github.com-angelus:angelus-h/angelus-h.github.io.git

# 5. Force push
git push origin --force --all
git push origin --force --tags

# 6. Verified
git log --all --full-history -- docs/learning/monitoring/Datadog-Agent-Configuration.md
# (empty output - success)
```

**Result:** File and all API keys permanently removed from GitHub history.

**Lesson learned:** 
- Never commit configuration files with real credentials
- Use environment variables and .gitignore
- Regular security audits of repositories

---

## Tools and Resources

**Secret Scanning Tools:**
- [detect-secrets](https://github.com/Yelp/detect-secrets) - Yelp's secret detector
- [TruffleHog](https://github.com/trufflesecurity/trufflehog) - High entropy string detector
- [GitGuardian](https://www.gitguardian.com/) - Commercial service
- [GitHub Secret Scanning](https://docs.github.com/en/code-security/secret-scanning) - Built-in for public repos

**Git History Rewriting:**
- [git-filter-repo](https://github.com/newren/git-filter-repo) - Recommended tool
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) - Alternative

**GitHub Documentation:**
- [Removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)

---

## FAQ

### Q: Can I just delete the file instead of rewriting history?

**A:** No. The file remains in old commits forever. Anyone can access it.

### Q: What if the secret is already on someone else's fork?

**A:** 
1. Contact them immediately
2. Ask them to delete their fork
3. Revoke the secret regardless
4. You cannot force-delete their fork

### Q: Will this affect open Pull Requests?

**A:** Yes. PRs based on old commits will break. Close them, rebase on new history, re-open.

### Q: How long does GitHub cache old commits?

**A:** Indefinitely. Even after force push, old commits may be cached for ~90 days. **Always revoke secrets immediately.**

### Q: Can I recover if I mess up?

**A:** Yes, if you created `.git.backup`:
```bash
rm -rf .git
mv .git.backup .git
```

### Q: What if I only want to remove the secret text, not the whole file?

**A:** Use `--replace-text` instead of `--path`:
```bash
git filter-repo --replace-text replacements.txt --force
```

---

**Last Updated:** 2026-05-27  
**Next Review:** When additional techniques or tools emerge  
**Status:** Production Ready - Tested in real incident response
