# GitHub CLI (gh) Quick Reference

**GitHub CLI** is the official command-line tool for GitHub, enabling repository management, issue tracking, PR workflows, and Actions automation directly from the terminal.

**Installation:** [https://cli.github.com/](https://cli.github.com/)

---

## Authentication

### Initial Setup

```bash
# Interactive login (browser-based OAuth)
gh auth login

# Login with token (non-interactive)
gh auth login --with-token < token.txt
export GH_TOKEN="ghp_xxxxxxxxxxxxx"

# Check authentication status
gh auth status

# List authenticated accounts
gh auth status --show-token

# Switch between accounts
gh auth switch

# Logout
gh auth logout
```

### Token Scopes

When creating a personal access token (PAT):
- **repo** - Full repository access
- **workflow** - Update GitHub Actions workflows
- **read:org** - Read organization data
- **admin:public_key** - Manage deploy keys

---

## Repository Operations

### Cloning and Creation

```bash
# Clone repository (uses SSH/HTTPS based on auth)
gh repo clone owner/repo
gh repo clone owner/repo target-directory

# Create new repository
gh repo create my-new-repo --public
gh repo create my-new-repo --private --description "Description"

# Create from current directory
gh repo create --source . --private --push

# Fork repository
gh repo fork owner/repo
gh repo fork owner/repo --clone
```

### Repository Management

```bash
# View repository details
gh repo view
gh repo view owner/repo
gh repo view owner/repo --web  # Open in browser

# List repositories
gh repo list              # Your repos
gh repo list owner        # User/org repos
gh repo list --limit 50 --visibility public

# Archive repository
gh repo archive owner/repo

# Delete repository (requires confirmation)
gh repo delete owner/repo

# Edit repository settings
gh repo edit --description "New description"
gh repo edit --homepage https://example.com
gh repo edit --visibility private
gh repo edit --enable-issues=false
gh repo edit --enable-wiki=false
```

### Repository Sync

```bash
# Sync fork with upstream
gh repo sync owner/repo

# Sync specific branch
gh repo sync owner/repo --branch main
```

---

## Pull Requests

### Creating Pull Requests

```bash
# Create PR interactively
gh pr create

# Create PR with details
gh pr create --title "Fix bug" --body "Description of fix"
gh pr create --base main --head feature-branch

# Create draft PR
gh pr create --draft

# Create PR from issue
gh pr create --issue 123

# Create PR and open in browser
gh pr create --web

# Create PR with reviewers and labels
gh pr create --reviewer user1,user2 --label bug,priority
```

### Listing Pull Requests

```bash
# List open PRs
gh pr list

# List all PRs (open, closed, merged)
gh pr list --state all

# Filter by author
gh pr list --author @me
gh pr list --author username

# Filter by label
gh pr list --label bug
gh pr list --label "needs review"

# Search PRs
gh pr list --search "is:pr is:open review:required"

# Limit results
gh pr list --limit 50
```

### Viewing Pull Requests

```bash
# View PR details
gh pr view 123
gh pr view https://github.com/owner/repo/pull/123

# View PR diff
gh pr diff 123

# View PR in browser
gh pr view 123 --web

# View PR comments
gh pr view 123 --comments

# Check PR status (CI checks, reviews)
gh pr status
gh pr checks 123
```

### Managing Pull Requests

```bash
# Checkout PR locally
gh pr checkout 123

# Review PR
gh pr review 123
gh pr review 123 --approve
gh pr review 123 --request-changes --body "Please fix X"
gh pr review 123 --comment --body "LGTM"

# Merge PR
gh pr merge 123
gh pr merge 123 --merge      # Create merge commit
gh pr merge 123 --squash     # Squash commits
gh pr merge 123 --rebase     # Rebase and merge
gh pr merge 123 --auto       # Auto-merge when checks pass
gh pr merge 123 --delete-branch  # Delete branch after merge

# Close PR without merging
gh pr close 123
gh pr close 123 --comment "Closing because..."

# Reopen PR
gh pr reopen 123

# Mark PR as ready (from draft)
gh pr ready 123

# Convert to draft
gh pr ready 123 --undo

# Edit PR
gh pr edit 123 --title "New title"
gh pr edit 123 --body "Updated description"
gh pr edit 123 --add-label bug
gh pr edit 123 --remove-label wip
gh pr edit 123 --add-reviewer user1
```

### PR Comments

```bash
# Add comment to PR
gh pr comment 123 --body "Great work!"

# Comment from file
gh pr comment 123 --body-file comment.txt

# Edit your last comment
gh pr comment 123 --edit-last
```

---

## Issues

### Creating Issues

```bash
# Create issue interactively
gh issue create

# Create with details
gh issue create --title "Bug: Login fails" --body "Steps to reproduce..."
gh issue create --label bug,priority --assignee @me

# Create from template
gh issue create --template bug_report.md

# Create and open in browser
gh issue create --web
```

### Listing Issues

```bash
# List open issues
gh issue list

# Filter by state
gh issue list --state all
gh issue list --state closed

# Filter by assignee
gh issue list --assignee @me
gh issue list --assignee username

# Filter by label
gh issue list --label bug
gh issue list --label "needs triage"

# Search issues
gh issue list --search "is:issue is:open label:bug"

# Limit results
gh issue list --limit 100
```

### Managing Issues

```bash
# View issue details
gh issue view 456
gh issue view 456 --web
gh issue view 456 --comments

# Close issue
gh issue close 456
gh issue close 456 --comment "Fixed in #789"

# Reopen issue
gh issue reopen 456

# Edit issue
gh issue edit 456 --title "Updated title"
gh issue edit 456 --body "New description"
gh issue edit 456 --add-label enhancement
gh issue edit 456 --remove-label bug
gh issue edit 456 --add-assignee user1

# Pin issue
gh issue pin 456

# Unpin issue
gh issue unpin 456

# Lock issue conversation
gh issue lock 456
gh issue unlock 456

# Transfer issue to another repo
gh issue transfer 456 owner/other-repo
```

### Issue Comments

```bash
# Add comment
gh issue comment 456 --body "Working on this"

# Comment from file
gh issue comment 456 --body-file update.txt

# Edit your last comment
gh issue comment 456 --edit-last
```

---

## GitHub Actions

### Workflow Runs

```bash
# List workflow runs
gh run list
gh run list --workflow deploy.yml
gh run list --branch main
gh run list --event push
gh run list --status failure

# View run details
gh run view 26512825533
gh run view 26512825533 --web
gh run view 26512825533 --log

# View specific job
gh run view --job 78081522658

# Download run artifacts
gh run download 26512825533
gh run download 26512825533 --name artifact-name
gh run download 26512825533 --dir ./downloads

# Re-run workflow
gh run rerun 26512825533
gh run rerun 26512825533 --failed  # Re-run only failed jobs

# Cancel run
gh run cancel 26512825533

# Delete run
gh run delete 26512825533

# Watch run in real-time
gh run watch 26512825533
```

### Workflow Management

```bash
# List workflows
gh workflow list

# View workflow details
gh workflow view deploy.yml
gh workflow view deploy.yml --web

# Run workflow manually (workflow_dispatch)
gh workflow run deploy.yml
gh workflow run deploy.yml --ref feature-branch
gh workflow run deploy.yml --field environment=production

# Enable workflow
gh workflow enable deploy.yml

# Disable workflow
gh workflow disable deploy.yml
```

### Workflow Logs

```bash
# View run logs
gh run view 26512825533 --log

# View specific job logs
gh run view --job 78081522658 --log

# Download logs
gh run view 26512825533 --log > workflow.log
```

---

## Releases

### Creating Releases

```bash
# Create release interactively
gh release create v1.0.0

# Create with details
gh release create v1.0.0 --title "Version 1.0.0" --notes "Release notes"
gh release create v1.0.0 --notes-file CHANGELOG.md

# Create from tag
gh release create v1.0.0 --target main

# Create draft release
gh release create v1.0.0 --draft

# Create pre-release
gh release create v1.0.0-beta --prerelease

# Upload assets
gh release create v1.0.0 app.zip checksums.txt
```

### Managing Releases

```bash
# List releases
gh release list
gh release list --limit 20

# View release
gh release view v1.0.0
gh release view latest
gh release view v1.0.0 --web

# Download release assets
gh release download v1.0.0
gh release download v1.0.0 --pattern "*.zip"
gh release download v1.0.0 --dir ./downloads

# Upload additional assets
gh release upload v1.0.0 new-asset.tar.gz

# Edit release
gh release edit v1.0.0 --title "Updated title"
gh release edit v1.0.0 --notes "New release notes"
gh release edit v1.0.0 --draft=false  # Publish draft

# Delete release
gh release delete v1.0.0
gh release delete v1.0.0 --yes  # Skip confirmation
```

---

## Gists

### Creating Gists

```bash
# Create gist from file
gh gist create file.txt

# Create gist from stdin
echo "Hello World" | gh gist create

# Create public gist
gh gist create file.txt --public

# Create with description
gh gist create file.txt --desc "My useful script"

# Create gist from multiple files
gh gist create file1.txt file2.py file3.md

# Create and open in browser
gh gist create file.txt --web
```

### Managing Gists

```bash
# List your gists
gh gist list
gh gist list --limit 50

# View gist
gh gist view GIST_ID
gh gist view GIST_ID --web

# Edit gist
gh gist edit GIST_ID

# Delete gist
gh gist delete GIST_ID

# Clone gist
gh gist clone GIST_ID
```

---

## Search

### Search Repositories

```bash
# Search repositories
gh search repos "kubernetes monitoring"
gh search repos "language:python stars:>1000"
gh search repos "org:kubernetes topic:observability"

# Search with filters
gh search repos "prometheus" --language Go --stars ">500"
gh search repos "flask" --created ">2024-01-01"

# Limit and sort
gh search repos "docker" --limit 50 --sort stars --order desc
```

### Search Code

```bash
# Search code across GitHub
gh search code "func parseConfig"
gh search code "class AuthManager" --language python

# Search in specific repo
gh search code "import kubernetes" --repo owner/repo

# Search with filters
gh search code "TODO" --extension py --path "src/"
```

### Search Issues and PRs

```bash
# Search issues
gh search issues "label:bug is:open"
gh search issues "author:@me is:closed"

# Search PRs
gh search prs "is:pr is:merged label:security"
gh search prs "review:required"
```

---

## API Access

### Making API Calls

```bash
# GET request
gh api /repos/owner/repo

# GET with query parameters
gh api /repos/owner/repo/issues --field state=closed

# POST request
gh api /repos/owner/repo/issues --method POST \
  --field title="Bug report" \
  --field body="Description"

# PATCH request
gh api /repos/owner/repo --method PATCH \
  --field description="Updated description"

# DELETE request
gh api /repos/owner/repo/issues/123 --method DELETE

# Pretty-print JSON
gh api /repos/owner/repo | jq .

# Pagination
gh api --paginate /repos/owner/repo/issues

# Include HTTP headers
gh api --include /repos/owner/repo
```

### GraphQL API

```bash
# Execute GraphQL query
gh api graphql -f query='
  query {
    viewer {
      login
      name
    }
  }
'

# Query from file
gh api graphql --paginate -f query=@query.graphql

# GraphQL with variables
gh api graphql -f query='
  query($owner: String!, $repo: String!) {
    repository(owner: $owner, name: $repo) {
      name
      stargazerCount
    }
  }
' -f owner=kubernetes -f repo=kubernetes
```

---

## Aliases

### Creating Custom Commands

```bash
# Set alias
gh alias set pv 'pr view'
gh alias set issues 'issue list --assignee @me'

# Multi-command alias
gh alias set bugs 'issue list --label bug'
gh alias set mypr 'pr list --author @me'

# Shell alias (starts with !)
gh alias set clone-cd '!gh repo clone "$1" && cd "$(basename "$1" .git)"'

# List aliases
gh alias list

# Delete alias
gh alias delete pv
```

### Example Aliases

```bash
# Productivity aliases
gh alias set co 'pr checkout'
gh alias set prs 'pr status'
gh alias set issues-me 'issue list --assignee @me'
gh alias set prs-me 'pr list --author @me'
gh alias set reviews 'pr list --search "review-requested:@me"'

# Workflow aliases
gh alias set runs 'run list --limit 10'
gh alias set failed 'run list --status failure'
gh alias set latest 'run view --log $(gh run list --limit 1 --json databaseId --jq ".[0].databaseId")'

# Repo management
gh alias set stars 'repo list --limit 100 --json nameWithOwner,stargazerCount --jq "sort_by(.stargazerCount) | reverse"'
```

---

## Configuration

### Global Settings

```bash
# Set default editor
gh config set editor vim
gh config set editor "code --wait"

# Set default protocol
gh config set git_protocol ssh
gh config set git_protocol https

# Set default browser
gh config set browser firefox

# Set prompt behavior
gh config set prompt enabled

# View configuration
gh config list
gh config get editor

# Reset to default
gh config set --default editor
```

### Repository-Specific Settings

```bash
# Set config for current repo
cd /path/to/repo
gh repo set-default owner/repo
```

---

## Extensions

### Installing Extensions

```bash
# Browse extensions
gh extension list

# Install extension
gh extension install owner/gh-extension-name

# Popular extensions
gh extension install dlvhdr/gh-dash      # Interactive dashboard
gh extension install mislav/gh-branch    # Branch utilities
gh extension install yusukebe/gh-markdown-preview  # Markdown preview

# Update extensions
gh extension upgrade --all
gh extension upgrade gh-dash

# Remove extension
gh extension remove gh-dash
```

### Creating Extensions

```bash
# Create new extension
gh extension create my-extension

# Extension structure
my-extension/
├── gh-my-extension  # Executable script
└── README.md
```

---

## Advanced Workflows

### CI/CD Integration

```bash
# Trigger workflow and wait for completion
gh workflow run deploy.yml --ref main
gh run watch $(gh run list --workflow deploy.yml --limit 1 --json databaseId --jq ".[0].databaseId")

# Check if latest run passed
gh run list --workflow test.yml --limit 1 --json conclusion --jq ".[0].conclusion == \"success\""

# Download test artifacts
latest_run=$(gh run list --workflow test.yml --limit 1 --json databaseId --jq ".[0].databaseId")
gh run download $latest_run --name test-results
```

### Automated PR Workflow

```bash
# Create feature branch, make changes, open PR
git checkout -b feature/new-feature
# ... make changes ...
git commit -am "Add new feature"
git push -u origin feature/new-feature
gh pr create --title "Add new feature" --body "Implements X, Y, Z" --reviewer team-leads

# Auto-merge when checks pass
gh pr merge --auto --squash --delete-branch
```

### Bulk Operations

```bash
# Close all stale issues
gh issue list --label stale --json number --jq ".[].number" | xargs -I {} gh issue close {}

# List all open PRs with failed checks
gh pr list --json number,url,statusCheckRollup --jq '.[] | select(.statusCheckRollup[0].conclusion == "FAILURE") | .url'

# Add label to all open issues
gh issue list --json number --jq ".[].number" | xargs -I {} gh issue edit {} --add-label needs-triage
```

### JSON Output and Processing

```bash
# Get PR details as JSON
gh pr view 123 --json number,title,author,labels,reviews

# Custom JSON fields
gh pr list --json number,title,createdAt,author --jq '.[] | {number, title, author: .author.login}'

# Export to CSV
gh issue list --json number,title,state,createdAt --jq -r '.[] | [.number, .title, .state, .createdAt] | @csv' > issues.csv
```

---

## Troubleshooting

### Common Issues

**Authentication Problems:**
```bash
# Check token scopes
gh auth status --show-token

# Refresh authentication
gh auth refresh --scopes repo,workflow

# Use different token
gh auth login --with-token < new_token.txt
```

**API Rate Limiting:**
```bash
# Check rate limit status
gh api /rate_limit

# Use GraphQL to reduce API calls
gh api graphql -f query='{...}'
```

**Network Issues:**
```bash
# Check connectivity
gh api /

# Use verbose mode
GH_DEBUG=api gh pr list
```

**Debugging:**
```bash
# Enable debug logging
GH_DEBUG=1 gh pr create
GH_DEBUG=api gh issue list

# Check version
gh version

# Update gh
gh upgrade  # On some systems
# Or use package manager: brew upgrade gh, apt update && apt upgrade gh-cli
```

---

## Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| `GH_TOKEN` | Authentication token | `export GH_TOKEN="ghp_xxxxx"` |
| `GH_ENTERPRISE_TOKEN` | GitHub Enterprise token | `export GH_ENTERPRISE_TOKEN="ghp_xxxxx"` |
| `GH_HOST` | GitHub hostname (for Enterprise) | `export GH_HOST="github.company.com"` |
| `GH_EDITOR` | Default editor | `export GH_EDITOR="vim"` |
| `GH_BROWSER` | Default browser | `export GH_BROWSER="firefox"` |
| `GH_PAGER` | Paging program | `export GH_PAGER="less -R"` |
| `GH_DEBUG` | Enable debug mode | `export GH_DEBUG=1` |
| `GH_CONFIG_DIR` | Config directory | `export GH_CONFIG_DIR="~/.config/gh"` |
| `NO_COLOR` | Disable color output | `export NO_COLOR=1` |

---

## Best Practices

### Security

- **Use fine-grained tokens** with minimal scopes
- **Rotate tokens regularly** (90-day cycle recommended)
- **Never commit tokens** to repositories
- **Use environment variables** for CI/CD authentication
- **Enable 2FA** on GitHub account

### Efficiency

- **Create aliases** for frequently used commands
- **Use JSON output** (`--json`) for scripting
- **Leverage tab completion** (install completions: `gh completion -s bash`)
- **Use `--web`** flag to open resources in browser
- **Combine with `jq`** for JSON parsing

### Automation

- **Use `gh` in scripts** instead of `curl` for GitHub API
- **Check exit codes** for error handling
- **Use `--repo` flag** to avoid changing directories
- **Cache repository data** when running multiple queries
- **Use GraphQL** for complex queries to reduce API calls

---

## Comparison: gh vs git

| Task | git | gh |
|------|-----|-----|
| Clone repo | `git clone git@github.com:owner/repo.git` | `gh repo clone owner/repo` |
| Create PR | N/A (requires browser) | `gh pr create` |
| Merge PR | N/A (requires browser) | `gh pr merge 123` |
| View issues | N/A | `gh issue list` |
| Trigger workflow | N/A | `gh workflow run deploy.yml` |
| View Actions logs | N/A | `gh run view 123 --log` |

**Use Together:**
```bash
# Create branch and PR workflow
git checkout -b feature/new-feature
# ... make changes ...
git commit -am "Add feature"
git push -u origin feature/new-feature
gh pr create --fill  # Uses commit messages
```

---

## Resources

- **Official Documentation:** [https://cli.github.com/manual/](https://cli.github.com/manual/)
- **GitHub Repository:** [https://github.com/cli/cli](https://github.com/cli/cli)
- **Extensions Directory:** [https://github.com/topics/gh-extension](https://github.com/topics/gh-extension)
- **Changelog:** [https://github.com/cli/cli/releases](https://github.com/cli/cli/releases)

---

**Last Updated:** 2026-05-27  
**gh Version:** 2.x+
