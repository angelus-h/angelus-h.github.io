---
description: GitHub learning plan - 3-week structured course covering pull requests, GitHub Actions, code review, API automation, and open source workflows.
---

# GitHub Learning Plan - 3 Weeks

## Context

**Your Background:**
- Familiar with Git basics
- Used GitLab at Company
- Need GitHub for Platform community work
- Open source contribution workflow

**Goal:**
- Master GitHub for open source work
- Understand GitHub Actions (CI/CD)
- Learn community contribution workflow
- Contribute to Platform projects

---

## 3-WEEK PLAN

### [Week 1: GitHub Fundamentals + PR Workflow](./week-01/)

**Focus:** GitHub basics + Pull Request workflow

**Topics:**
- GitHub vs GitLab (key differences)
- Repositories, Organizations, Teams
- Pull Request workflow
- Fork & clone workflow
- Code review on GitHub
- Issues & Projects

 **[Go to Week 1 →](./week-01/README.md)**

---

### [Week 2: GitHub Actions (CI/CD)](./week-02/)

**Focus:** GitHub Actions, workflows, automation

**Topics:**
- GitHub Actions syntax
- Workflows, jobs, steps
- GitHub-hosted vs self-hosted runners
- Secrets, environment variables
- Marketplace actions
- Matrix builds
- Artifact storage

 **[Go to Week 2 →](./week-02/README.md)**

---

### [Week 3: Open Source + Advanced GitHub](./week-03/)

**Focus:** Contributing to Platform, advanced features

**Topics:**
- Open source contribution workflow
- GitHub Apps vs Actions
- GitHub API (REST & GraphQL)
- Security features (Dependabot, Code scanning)
- GitHub Packages (container registry)
- GitHub Pages & Wikis

 **[Go to Week 3 →](./week-03/README.md)**

---

## Learning Approach

**This is a REFERENCE guide!**

- **Use on-the-job:** Learn while contributing to Platform
- **Hands-on first:** Fork repos, create PRs
- **Just-in-time:** Read when you need it

---

## Quick Start

### Immediate Actions (Day 1):

1. **Create GitHub account** (if you don't have):
 - Sign up: https://github.com/signup
 - Enable 2FA!

2. **Configure SSH keys:**
 ```bash
 # Generate SSH key
 ssh-keygen -t ed25519 -C "your.email@company.com"

 # Add to ssh-agent
 eval "$(ssh-agent -s)"
 ssh-add ~/.ssh/id_ed25519

 # Copy public key
 cat ~/.ssh/id_ed25519.pub
 # Add to GitHub: Settings → SSH Keys
 ```

3. **Configure Git:**
 ```bash
 git config --global user.name "Your Name"
 git config --global user.email "your.email@company.com"
 ```

4. **Fork a Platform repo:**
 ```bash
 # Browser: https://github.com/platform-ci/architecture → Fork

 # Clone your fork
 git clone git@github.com:YOUR_USERNAME/architecture.git
 cd architecture

 # Add upstream remote
 git remote add upstream https://github.com/platform-ci/architecture.git
 ```

---

## Key Skills to Master

### Week 1:
- Create Pull Requests
- Review code with comments
- Use GitHub Issues
- Understand fork workflow
- Sync fork with upstream

### Week 2:
- Write GitHub Actions workflows
- Trigger workflows manually
- Use marketplace actions
- Debug failed workflows
- Cache dependencies

### Week 3:
- Contribute to Platform repos
- Call GitHub API
- Enable security scanning
- Publish packages
- Use GitHub CLI (`gh`)

---

## GitHub vs GitLab (Quick Comparison)

| Feature | GitLab | GitHub |
|---------|--------|--------|
| **MR/PR** | Merge Request | Pull Request |
| **CI/CD** | `.gitlab-ci.yml` | `.github/workflows/*.yml` |
| **Runners** | GitLab Runners | GitHub Actions runners |
| **API** | REST only | REST + GraphQL |
| **Groups** | Groups & Subgroups | Organizations |
| **Container Registry** | Built-in | GitHub Packages |
| **Issues** | Issues + Epics | Issues + Projects |
| **Webhooks** | Project/Group webhooks | Repo webhooks + GitHub Apps |

---

## Resources

### Official Docs:
- **GitHub Docs:** https://docs.github.com/
- **GitHub Actions:** https://docs.github.com/en/actions
- **API Reference:** https://docs.github.com/en/rest
- **GitHub CLI:** https://cli.github.com/

### Platform Repos:
- **Architecture:** https://github.com/platform-ci/architecture
- **Docs:** https://github.com/platform-ci/docs
- **Build Service:** https://github.com/platform-ci/build-service

### Community:
- **Platform Slack:** #platform-users
- **GitHub Community:** https://github.community/

---

## Tips for Platform Contributors

1. **Read CONTRIBUTING.md first:**
 - Every Platform repo has one
 - Follow the guidelines!

2. **Use GitHub CLI:**
 ```bash
 # Install gh
 brew install gh # macOS
 # or
 sudo dnf install gh # Fedora/RHEL

 # Authenticate
 gh auth login

 # Create PR from CLI
 gh pr create --title "Fix: bug description" --body "Details..."
 ```

3. **Sign your commits:**
 ```bash
 # GPG setup (for verified commits)
 git config --global commit.gpgsign true
 ```

4. **Watch repos:**
 - Watch Platform repos you care about
 - Get notified of issues, PRs, releases

5. **Use Draft PRs:**
 - Mark PR as draft when WIP
 - Convert to ready when done

---

## Learning Path for Platform Contributions

```
Week 1: Understand GitHub basics
 ↓
Week 2: Master GitHub Actions (Platform uses it!)
 ↓
Week 3: Start contributing!
 ↓
First contribution:
 1. Pick a "good first issue"
 2. Fork repo
 3. Create branch
 4. Make changes
 5. Create PR
 6. Respond to review feedback
 7. Get merged! 
```

---

## GitHub Features You'll Love

1. **GitHub CLI (`gh`):**
 - Manage everything from terminal
 - Fast PR creation
 - View issues, PRs without browser

2. **Code Spaces:**
 - Cloud dev environment
 - One-click setup

3. **GitHub Actions Marketplace:**
 - 13,000+ ready-to-use actions
 - Build, test, deploy anything

4. **Advanced Search:**
 - Search across all repos
 - Powerful filters

5. **Notifications:**
 - Customizable per-repo
 - Never miss important updates

---

**Ready to start?** → [Week 1: GitHub Fundamentals →](./week-01/README.md)
