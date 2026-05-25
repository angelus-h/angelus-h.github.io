---
description: GitLab fundamentals week 1 - architecture, projects, merge requests, code review, labels, milestones, and advanced MR features.
---

# GitLab Week 1: Fundamentals + MR Workflow

## Goal

Master GitLab basics and advanced Merge Request features.

---

## Topics

### Day 1-2: GitLab Architecture

**What you'll learn:**
- GitLab CE vs EE (Community vs Enterprise)
- Projects, Groups, Namespaces
- Permissions model (Guest, Reporter, Developer, Maintainer, Owner)
- Company GitLab structure

**Hands-on:**
```bash
# Explore app-interface structure
https://gitlab.company.com/service/app-interface

Group: service
Project: app-interface
Namespace: service/app-interface
```

**Key concepts:**
- **Project:** Single repo (e.g., app-interface)
- **Group:** Collection of projects (e.g., service, app-sre)
- **Namespace:** Unique path (group/project)

---

### Day 3-4: Merge Request Workflow

**What you'll learn:**
- Creating MRs (draft vs ready)
- MR templates
- Labels, milestones, assignees
- Approval rules
- Merge strategies (merge commit, squash, rebase)

**Hands-on:**
```bash
# Create an MR (review your previous one)
git checkout -b feature/test-mr
# Make changes
git add .
git commit -m "Test MR"
git push origin feature/test-mr

# In GitLab UI:
1. Create MR
2. Use template (.gitlab/merge_request_templates/Default.md)
3. Fill: What, Why, Tickets, Validation
4. Add labels
5. Assign reviewers
```

**MR Templates (app-interface):**
```markdown
#### What
- Bullet points of changes

#### Why
- Context and justification

#### Tickets
- JIRA links (Closes: INFRA-XXXX)

#### Validation
- How did you test?

#### Dependency
- Dependent MRs (optional)
```

---

### Day 5-6: Code Review Best Practices

**What you'll learn:**
- Reviewing MRs effectively
- Comment types (general, specific line, suggestion)
- Resolving discussions
- Approval workflow

**How to review:**

1. **Check Description:**
 - What/Why clear?
 - Tickets linked?
 - Validation done?

2. **Review Code:**
 - Click "Changes" tab
 - Add comments on specific lines
 - Use "Start a review" (batch comments)

3. **Use Suggestions:**
 ```suggestion
 # Suggest code change (reviewer can apply directly)
 cluster:
 $ref: /openshift/appsres09ue1/cluster.yml
 ```

4. **Resolve Discussions:**
 - Address all comments
 - Mark as resolved when fixed

5. **Approve:**
 - Click "Approve" button
 - Or request changes

---

### Day 7: Branch Protection & Merge Strategies

**What you'll learn:**
- Protected branches (master)
- Merge checks (CI must pass)
- Merge strategies
- Squash vs merge commit

**Protected branches:**
```
In app-interface:
- master branch is protected
- Required: Approvals from code owners
- Required: CI pipeline must pass
- No force push allowed
```

**Merge strategies:**

| Strategy | Result | When to use |
|----------|--------|-------------|
| **Merge commit** | Preserves all commits | Default, full history |
| **Squash** | Single commit | Clean history, small MRs |
| **Rebase** | Linear history | Advanced, no merge commits |

**App-interface uses:** Merge commit (preserves full history)

---

## Exercises

### Exercise 1: Explore app-interface

```bash
# 1. Browse the project
https://gitlab.company.com/service/app-interface

# 2. Check settings
Settings → General → Visibility (Internal)
Settings → Repository → Protected branches

# 3. Check members
Settings → Members
- See who has access
- Understand roles
```

---

### Exercise 2: Create a Test MR

```bash
# 1. Create branch
git checkout -b test/my-first-gitlab-mr

# 2. Make a small change (e.g., add comment to README)
echo "# Test comment" >> README.md

# 3. Commit
git add README.md
git commit -m "Test: my first GitLab MR"

# 4. Push
git push origin test/my-first-gitlab-mr

# 5. Create MR in UI
- Use template
- Add label: test
- Mark as Draft
- Close without merging
```

---

### Exercise 3: Review an Existing MR

```bash
# Find a merged MR in app-interface
https://gitlab.company.com/service/app-interface/-/merge_requests?scope=all&state=merged

# Pick one, review:
1. Read description
2. Check "Changes" tab
3. Look for patterns:
 - How did they structure commits?
 - What kind of validation?
 - How many approvals needed?
```

---

## Week 1 Checklist

- [ ] Understand GitLab project structure
- [ ] Know the difference between CE and EE
- [ ] Created at least 1 MR
- [ ] Used MR template correctly
- [ ] Reviewed code with comments
- [ ] Understand approval workflow
- [ ] Know merge strategies

---

## Resources

- **GitLab MR Docs:** https://docs.gitlab.com/ee/user/project/merge_requests/
- **Code Review Guidelines:** https://docs.gitlab.com/ee/development/code_review.html
- **Protected Branches:** https://docs.gitlab.com/ee/user/project/protected_branches.html

---

**Next:** [Week 2: GitLab CI/CD →](../week-02/README.md)
