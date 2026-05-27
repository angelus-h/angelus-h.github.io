---
description: GitLab learning plan - 3-week course covering merge requests, CI/CD pipelines, webhooks, API automation, and advanced collaboration workflows.
---

# GitLab Learning Plan - 3 Weeks

## Context

**Your Background:**
- Already used GitLab at Company (configuration repository)
- Created MRs (INFRA-123)
- Familiar with basic workflow
- Need deeper understanding of CI/CD, webhooks, automation

**Goal:**
- Master GitLab for internal Company work
- Understand CI/CD pipelines
- Learn advanced MR workflows
- Automate with webhooks & APIs

---

## 3-WEEK PLAN

### [Week 1: GitLab Fundamentals + MR Workflow](./week-01/)

**Focus:** Master the basics + advanced MR features

**Topics:**
- GitLab architecture (CE vs EE)
- Projects, groups, namespaces
- Merge Request advanced features
- Code review best practices
- Branch protection rules

**[Go to Week 1 →](./week-01/README.md)**

---

### [Week 2: GitLab CI/CD](./week-02/)

**Focus:** CI/CD pipelines, runners, automation

**Topics:**
- `.gitlab-ci.yml` syntax
- Pipelines, jobs, stages
- GitLab Runners (shared vs specific)
- Variables, secrets, artifacts
- Pipeline strategies (DAG, parent-child)

**[Go to Week 2 →](./week-02/README.md)**

---

### [Week 3: Advanced GitLab + API](./week-03/)

**Focus:** Webhooks, API, automation, security

**Topics:**
- GitLab API (REST)
- Webhooks & integrations
- Security scanning (SAST, DAST, dependency scanning)
- Container registry
- GitLab Pages

**[Go to Week 3 →](./week-03/README.md)**

---

## Learning Approach

**This is a REFERENCE guide!**

- **Use on-the-job:** Learn while working on real MRs
- **Hands-on first:** Try features in configuration repository
- **Just-in-time:** Read sections when you need them

---

## Quick Start

### Immediate Actions (Day 1):

1. **Check your GitLab version:**
```bash
# Company GitLab
curl https://gitlab.company.com/api/v4/version
```

2. **Create personal access token:**
- Settings → Access Tokens
- Scopes: api, read_user, write_repository

3. **Clone app-interface locally:**
```bash
git clone https://gitlab.company.com/service/app-interface.git
```

---

## Key Skills to Master

### Week 1:
- Create MRs with templates
- Use labels, milestones, assignees
- Review code effectively
- Understand approval rules

### Week 2:
- Write `.gitlab-ci.yml`
- Trigger pipelines manually
- Debug failed jobs
- Use artifacts & caching

### Week 3:
- Call GitLab API with curl/Python
- Create webhooks
- Enable security scanning
- Automate workflows with API

---

## Resources

### Official Docs:
- **GitLab Docs:** https://docs.gitlab.com/
- **CI/CD Examples:** https://docs.gitlab.com/ee/ci/examples/
- **API Reference:** https://docs.gitlab.com/ee/api/

### Company Internal:
- **GitLab CE:** https://gitlab.company.com
- **App-Interface:** https://gitlab.company.com/service/app-interface

---

## Tips for Company GitLab

1. **Use MR templates:**
- App-interface has `.gitlab/merge_request_templates/`
- Always fill out: What, Why, Tickets, Validation

2. **Watch CI/CD jobs:**
- Jenkins integration (not GitLab CI in app-interface)
- Understand webhook → Jenkins flow

3. **API automation:**
- Automate repetitive MR tasks
- Use GitLab API for bulk operations

4. **Security first:**
- Never commit secrets
- Use protected branches
- Enable approval rules for critical paths

---

**Ready to start?** → [Week 1: GitLab Fundamentals →](./week-01/README.md)
