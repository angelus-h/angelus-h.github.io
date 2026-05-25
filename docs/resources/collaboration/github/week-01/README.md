# GitHub Week 1: Fundamentals + PR Workflow

## Goal

Master GitHub basics and Pull Request workflow for open source contributions.

---

## Topics

### Day 1-2: GitHub Basics

**What you'll learn:**
- GitHub account setup
- Repositories (public vs private)
- Organizations & Teams
- GitHub vs GitLab differences
- Platform GitHub organization

**Hands-on:**
```bash
# 1. Create GitHub account (if needed)
https://github.com/signup

# 2. Enable 2FA
Settings → Password and authentication → Two-factor authentication

# 3. Add SSH key
Settings → SSH and GPG keys → New SSH key

# Test SSH
ssh -T git@github.com
# Should see: "Hi USERNAME! You've successfully authenticated"
```

**Explore Platform:**
```bash
# Browse Platform organization
https://github.com/platform-ci

# Key repos:
- architecture # Technical docs & ADRs
- docs # User documentation
- build-service # Build component
- integration-service
- release-service
```

---

### Day 3-4: Fork & Pull Request Workflow

**What you'll learn:**
- Fork vs Clone
- Upstream vs Origin
- Creating Pull Requests
- PR templates (PULL_REQUEST_TEMPLATE.md)
- Draft PRs

**Fork workflow:**

```bash
# 1. Fork repo on GitHub UI
https://github.com/platform-ci/architecture → Fork button

# 2. Clone YOUR fork
git clone git@github.com:YOUR_USERNAME/architecture.git
cd architecture

# 3. Add upstream remote
git remote add upstream https://github.com/platform-ci/architecture.git

# 4. Verify remotes
git remote -v
# origin git@github.com:YOUR_USERNAME/architecture.git (fetch)
# origin git@github.com:YOUR_USERNAME/architecture.git (push)
# upstream https://github.com/platform-ci/architecture.git (fetch)
# upstream https://github.com/platform-ci/architecture.git (push)
```

**Keep fork in sync:**

```bash
# Fetch upstream changes
git fetch upstream

# Merge upstream/main into your main
git checkout main
git merge upstream/main

# Push to your fork
git push origin main
```

**Create PR:**

```bash
# 1. Create feature branch
git checkout -b fix/typo-in-readme

# 2. Make changes
echo "Fix typo" >> README.md

# 3. Commit
git add README.md
git commit -m "Fix typo in README"

# 4. Push to YOUR fork
git push origin fix/typo-in-readme

# 5. Create PR on GitHub UI
- Go to your fork on GitHub
- Click "Compare & pull request"
- Base: platform-ci/architecture main
- Head: YOUR_USERNAME/architecture fix/typo-in-readme
- Fill in PR template
- Create pull request
```

---

### Day 5-6: Code Review on GitHub

**What you'll learn:**
- Reviewing PRs
- Suggesting changes
- Requesting changes vs Approving
- Resolving conversations
- Re-requesting review

**How to review:**

1. **Go to PR:**
 ```
 https://github.com/platform-ci/architecture/pulls
 ```

2. **Files changed tab:**
 - See all code changes
 - Add comments on specific lines

3. **Comment types:**

 **General comment:**
 ```
 This looks good overall, but please add tests.
 ```

 **Line-specific comment:**
 ```
 Click "+" on line number → Add comment
 ```

 **Suggested change:**
 ```suggestion
 name: platform-pipeline
 ```
 Contributor can click "Commit suggestion" to apply!

4. **Review actions:**
 - **Comment:** Just add feedback
 - **Approve:** Changes look good 
 - **Request changes:** Needs fixes before merge 

---

### Day 7: Issues & Project Boards

**What you'll learn:**
- GitHub Issues
- Issue templates
- Labels, milestones, assignees
- Projects (Kanban boards)
- Linking PRs to issues

**Working with issues:**

```bash
# Create issue via UI
https://github.com/platform-ci/architecture/issues/new

# Or via GitHub CLI
gh issue create \
 --title "Add documentation for XYZ" \
 --body "We need docs for feature XYZ"
```

**Link PR to issue:**

```markdown
# In PR description:
Closes #123
Fixes #456
Resolves platform-ci/docs#789
```

**When PR merges → Issues automatically close!**

---

## GitHub vs GitLab Quick Reference

| Feature | GitLab | GitHub |
|---------|--------|--------|
| **Change request** | Merge Request (MR) | Pull Request (PR) |
| **Fork update** | Sync fork button | `git fetch upstream` |
| **Review** | Start review + Approve | Review changes + Approve |
| **Suggest code** | ```suggestion | ```suggestion (same!) |
| **Auto-close issues** | `Closes: JIRA-123` | `Closes #123` |
| **Draft** | Draft MR | Draft PR |
| **CLI tool** | `glab` | `gh` |

---

## Exercises

### Exercise 1: Fork Platform Architecture Repo

```bash
# 1. Fork via UI
https://github.com/platform-ci/architecture → Fork

# 2. Clone & setup
git clone git@github.com:YOUR_USERNAME/architecture.git
cd architecture
git remote add upstream https://github.com/platform-ci/architecture.git

# 3. Verify
git remote -v
```

---

### Exercise 2: Create Your First PR

```bash
# 1. Sync fork
git fetch upstream
git checkout main
git merge upstream/main
git push origin main

# 2. Create branch
git checkout -b docs/add-learning-notes

# 3. Make change (e.g., add note to README)
echo "\n## Learning Notes\n- Studying Platform architecture" >> README.md

# 4. Commit
git add README.md
git commit -m "docs: add learning notes to README"

# 5. Push
git push origin docs/add-learning-notes

# 6. Create PR (GitHub UI)
- Compare & pull request
- Mark as DRAFT (WIP)
- Close without merging (just practice!)
```

---

### Exercise 3: Install GitHub CLI

```bash
# Install gh
# macOS
brew install gh

# Fedora/RHEL
sudo dnf install gh

# Ubuntu
sudo apt install gh

# Authenticate
gh auth login

# Test
gh repo view platform-ci/architecture
gh issue list --repo platform-ci/architecture
gh pr list --repo platform-ci/architecture
```

---

### Exercise 4: Review a Platform PR

```bash
# Find open PRs
https://github.com/platform-ci/architecture/pulls

# Pick one, review:
1. Read description
2. Check "Files changed"
3. Look for:
 - Clear commit messages?
 - Tests included?
 - Docs updated?
4. Leave a practice comment (optional, be nice!)
```

---

## Week 1 Checklist

- [ ] GitHub account with 2FA enabled
- [ ] SSH key configured
- [ ] Forked a Platform repo
- [ ] Configured upstream remote
- [ ] Created at least 1 PR (can be draft)
- [ ] Reviewed someone else's PR
- [ ] Installed GitHub CLI (`gh`)
- [ ] Understand fork workflow
- [ ] Know how to link PR to issue

---

## Resources

- **GitHub Docs:** https://docs.github.com/
- **Forking Workflow:** https://docs.github.com/en/get-started/quickstart/fork-a-repo
- **Pull Requests:** https://docs.github.com/en/pull-requests
- **GitHub CLI:** https://cli.github.com/manual/

### Platform Resources:
- **GitHub Org:** https://github.com/platform-ci
- **Contributing Guide:** https://github.com/platform-ci/architecture/blob/main/CONTRIBUTING.md
- **Architecture Repo:** https://github.com/platform-ci/architecture

---

**Next:** [Week 2: GitHub Actions →](../week-02/README.md)
