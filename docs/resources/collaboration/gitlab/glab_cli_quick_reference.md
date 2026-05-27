# GitLab CLI (glab) Quick Reference

**GitLab CLI** is the official command-line tool for GitLab, enabling repository management, issue tracking, MR workflows, and CI/CD pipeline automation directly from the terminal.

**Installation:** [https://gitlab.com/gitlab-org/cli](https://gitlab.com/gitlab-org/cli)

---

## Installation

### Package Managers

```bash
# macOS (Homebrew)
brew install glab

# Linux (Homebrew)
brew install glab

# Debian/Ubuntu (apt)
sudo apt install glab

# Fedora/RHEL (dnf)
sudo dnf install glab

# Arch Linux (pacman)
sudo pacman -S glab

# Windows (Scoop)
scoop install glab

# Windows (Chocolatey)
choco install glab
```

### Manual Installation

```bash
# Download latest release
VERSION=$(curl -s https://gitlab.com/api/v4/projects/gitlab-org%2Fcli/releases | jq -r '.[0].tag_name')
wget https://gitlab.com/gitlab-org/cli/-/releases/${VERSION}/downloads/glab_${VERSION}_Linux_x86_64.tar.gz
tar -xvf glab_${VERSION}_Linux_x86_64.tar.gz
sudo mv glab /usr/local/bin/
```

---

## Authentication

### Initial Setup

```bash
# Interactive login
glab auth login

# Login to specific GitLab instance
glab auth login --hostname gitlab.company.com

# Login with token (non-interactive)
glab auth login --hostname gitlab.com --token glpat-xxxxxxxxxxxxx
export GITLAB_TOKEN="glpat-xxxxxxxxxxxxx"

# Check authentication status
glab auth status

# Logout
glab auth logout
```

### Token Scopes

When creating a personal access token (PAT):
- **api** - Full API access (required for most operations)
- **read_api** - Read-only API access
- **read_repository** - Clone and read repositories
- **write_repository** - Push to repositories
- **read_registry** - Pull container images
- **write_registry** - Push container images

---

## Repository Operations

### Cloning and Creation

```bash
# Clone repository
glab repo clone group/project
glab repo clone gitlab.com:group/project target-directory

# Create new repository
glab repo create my-new-project
glab repo create my-new-project --visibility private
glab repo create my-new-project --description "Project description"

# Create in specific group
glab repo create group/my-new-project

# Fork repository
glab repo fork group/project
glab repo fork group/project --clone
```

### Repository Management

```bash
# View repository details
glab repo view
glab repo view group/project

# Archive repository
glab repo archive group/project

# Delete repository (requires confirmation)
glab repo delete group/project

# List repositories
glab repo list                    # Current user
glab repo list group              # Specific group
glab repo list --mine             # Your projects
glab repo list --member           # Member of
```

### Repository Mirroring

```bash
# View repository mirrors
glab repo mirror list

# Create pull mirror
glab repo mirror create --url https://github.com/user/repo.git --direction pull

# Trigger mirror sync
glab repo mirror update <mirror-id>
```

---

## Merge Requests (MRs)

### Creating Merge Requests

```bash
# Create MR interactively
glab mr create

# Create with details
glab mr create --title "Fix bug" --description "Description of fix"
glab mr create --source-branch feature --target-branch main

# Create draft MR
glab mr create --draft

# Create MR from issue
glab mr create --related-issue 123

# Create MR with labels and assignees
glab mr create --label bug,priority --assignee user1,user2

# Create MR and remove source branch on merge
glab mr create --remove-source-branch

# Fill from commit messages
glab mr create --fill

# Squash commits on merge
glab mr create --squash-before-merge
```

### Listing Merge Requests

```bash
# List open MRs
glab mr list

# List all MRs
glab mr list --all

# Filter by state
glab mr list --state merged
glab mr list --state closed

# Filter by author
glab mr list --author @me
glab mr list --author username

# Filter by label
glab mr list --label bug
glab mr list --label "needs review"

# Filter by milestone
glab mr list --milestone v1.0

# Search MRs
glab mr list --search "authentication"

# Limit results
glab mr list --per-page 50
```

### Viewing Merge Requests

```bash
# View MR details
glab mr view 123
glab mr view https://gitlab.com/group/project/-/merge_requests/123

# View MR diff
glab mr diff 123

# View in browser
glab mr view 123 --web

# View MR notes
glab mr note list 123

# Check MR pipeline status
glab mr view 123 --pipeline
glab ci view --branch feature-branch
```

### Managing Merge Requests

```bash
# Checkout MR locally
glab mr checkout 123

# Approve MR
glab mr approve 123

# Revoke approval
glab mr unapprove 123

# Merge MR
glab mr merge 123
glab mr merge 123 --when-pipeline-succeeds
glab mr merge 123 --squash
glab mr merge 123 --remove-source-branch
glab mr merge 123 --rebase

# Close MR
glab mr close 123

# Reopen MR
glab mr reopen 123

# Mark as ready (from draft)
glab mr update 123 --ready

# Convert to draft
glab mr update 123 --draft

# Update MR
glab mr update 123 --title "New title"
glab mr update 123 --description "New description"
glab mr update 123 --label bug
glab mr update 123 --unlabel wip
glab mr update 123 --assignee user1
glab mr update 123 --unassign user2
glab mr update 123 --milestone v2.0

# Rebase MR
glab mr rebase 123

# Delete MR (and source branch)
glab mr delete 123
```

### MR Notes (Comments)

```bash
# Add note to MR
glab mr note 123 --message "Great work!"

# List notes
glab mr note list 123
```

---

## Issues

### Creating Issues

```bash
# Create issue interactively
glab issue create

# Create with details
glab issue create --title "Bug: Login fails" --description "Steps to reproduce..."
glab issue create --label bug,priority --assignee @me

# Create confidential issue
glab issue create --confidential

# Create with due date
glab issue create --due-date 2026-06-30

# Link to MR
glab issue create --linked-mr 456

# Create and open in browser
glab issue create --web
```

### Listing Issues

```bash
# List open issues
glab issue list

# List all issues
glab issue list --all

# Filter by state
glab issue list --state closed

# Filter by assignee
glab issue list --assignee @me
glab issue list --assignee username

# Filter by label
glab issue list --label bug
glab issue list --label "needs triage"

# Filter by milestone
glab issue list --milestone v1.0

# Search issues
glab issue list --search "authentication"

# Mine (created by you)
glab issue list --mine

# Show confidential issues
glab issue list --confidential
```

### Managing Issues

```bash
# View issue details
glab issue view 456
glab issue view 456 --web
glab issue view 456 --comments

# Close issue
glab issue close 456
glab issue close 456 --note "Fixed in !789"

# Reopen issue
glab issue reopen 456

# Update issue
glab issue update 456 --title "Updated title"
glab issue update 456 --description "New description"
glab issue update 456 --label enhancement
glab issue update 456 --unlabel bug
glab issue update 456 --assignee user1
glab issue update 456 --milestone v2.0
glab issue update 456 --due-date 2026-07-15

# Delete issue (requires permissions)
glab issue delete 456

# Subscribe to issue notifications
glab issue subscribe 456

# Unsubscribe
glab issue unsubscribe 456

# Link issue to MR
glab issue update 456 --linked-mr 789
```

### Issue Notes

```bash
# Add note to issue
glab issue note 456 --message "Working on this"

# List notes
glab issue note list 456
```

### Issue Boards

```bash
# List issue boards
glab issue board list

# View board
glab issue board view <board-id>
```

---

## CI/CD Pipelines

### Pipeline Operations

```bash
# List pipelines
glab ci list
glab ci list --status failed
glab ci list --status running

# View pipeline details
glab ci view 123456
glab ci view --branch main
glab ci view --web

# Get pipeline status
glab ci status
glab ci status --branch develop

# Trigger new pipeline
glab ci run
glab ci run --branch feature-branch

# Retry pipeline
glab ci retry 123456

# Cancel pipeline
glab ci cancel 123456

# Delete pipeline
glab ci delete 123456
```

### Job Management

```bash
# List jobs
glab ci jobs
glab ci jobs --pipeline 123456

# View job details
glab ci view --job 987654
glab ci view --job 987654 --web

# View job logs
glab ci trace 987654
glab ci trace --branch main --job build

# Download job artifacts
glab ci artifact 987654
glab ci artifact 987654 --path build/
glab ci artifact --branch main --job deploy

# Retry job
glab ci retry --job 987654

# Cancel job
glab ci cancel --job 987654

# Play manual job
glab ci play 987654
```

### CI Lint

```bash
# Validate .gitlab-ci.yml
glab ci lint
glab ci lint --path custom-ci.yml
```

### CI Variables

```bash
# List CI/CD variables
glab variable list

# Export variables to .env file
glab variable export

# Get specific variable
glab variable get DATABASE_URL

# Set variable
glab variable set DATABASE_URL "postgresql://..."
glab variable set API_KEY "secret123" --masked
glab variable set DEPLOY_KEY "..." --protected

# Update variable
glab variable update DATABASE_URL "new_value"

# Delete variable
glab variable delete OLD_VAR
```

---

## Releases

### Creating Releases

```bash
# Create release
glab release create v1.0.0 --name "Version 1.0.0" --notes "Release notes"
glab release create v1.0.0 --notes-file CHANGELOG.md

# Create release with assets
glab release create v1.0.0 --asset-link name=app.zip,url=https://...

# Create release from tag
glab release create v1.0.0 --ref main
```

### Managing Releases

```bash
# List releases
glab release list
glab release list --per-page 20

# View release
glab release view v1.0.0
glab release view v1.0.0 --web

# Download release assets
glab release download v1.0.0
glab release download v1.0.0 --asset-name app.zip

# Upload assets
glab release upload v1.0.0 --assets app.zip,checksums.txt

# Delete release
glab release delete v1.0.0
```

---

## Labels

### Managing Labels

```bash
# List labels
glab label list

# Create label
glab label create bug --color "#FF0000" --description "Bug reports"

# Update label
glab label update bug --new-name bug-report
glab label update bug --color "#FF6600"

# Delete label
glab label delete old-label
```

---

## Milestones

### Managing Milestones

```bash
# List milestones
glab milestone list
glab milestone list --state active

# Create milestone
glab milestone create v1.0 --title "Version 1.0" --due-date 2026-06-30

# View milestone
glab milestone view v1.0

# Update milestone
glab milestone update v1.0 --title "Updated title"
glab milestone update v1.0 --due-date 2026-07-15

# Close milestone
glab milestone close v1.0

# Delete milestone
glab milestone delete old-milestone
```

---

## Snippets

### Creating Snippets

```bash
# Create snippet from file
glab snippet create --file script.sh --title "Useful script"
glab snippet create --file script.sh --visibility private

# Create snippet from stdin
cat script.py | glab snippet create --title "Python script"

# Create snippet with description
glab snippet create --file config.yml --title "Config" --description "Production config"
```

### Managing Snippets

```bash
# List snippets
glab snippet list
glab snippet list --mine

# View snippet
glab snippet get <snippet-id>
glab snippet get <snippet-id> --web

# Update snippet
glab snippet update <snippet-id> --file updated-script.sh

# Delete snippet
glab snippet delete <snippet-id>
```

---

## Users and Groups

### User Information

```bash
# View current user
glab user

# View specific user
glab user view username

# List users
glab user list
```

### Group Management

```bash
# List groups
glab group list

# View group
glab group view group-name
```

---

## API Access

### Making API Calls

```bash
# GET request
glab api projects/:id

# GET with query parameters
glab api projects/:id/issues?state=opened

# POST request
glab api projects/:id/issues --method POST \
  --field title="Bug report" \
  --field description="Description"

# PATCH request
glab api projects/:id --method PATCH \
  --field description="Updated description"

# DELETE request
glab api projects/:id/issues/123 --method DELETE

# Pretty-print JSON
glab api projects/:id | jq .

# Include HTTP headers
glab api --include projects/:id
```

### GraphQL API

```bash
# Execute GraphQL query
glab api graphql --paginate -f query='
  query {
    currentUser {
      username
      name
    }
  }
'

# Query from file
glab api graphql -f query=@query.graphql
```

---

## Aliases

### Creating Custom Commands

```bash
# Set alias
glab alias set co 'mr checkout'
glab alias set issues 'issue list --assignee @me'

# List aliases
glab alias list

# Delete alias
glab alias delete co
```

### Example Aliases

```bash
# Productivity aliases
glab alias set myissues 'issue list --assignee @me'
glab alias set mymrs 'mr list --author @me'
glab alias set reviews 'mr list --reviewer @me --state opened'
glab alias set drafts 'mr list --author @me --draft'

# Pipeline aliases
glab alias set pipelines 'ci list --per-page 10'
glab alias set failed 'ci list --status failed'
glab alias set running 'ci list --status running'

# Quick views
glab alias set bugs 'issue list --label bug'
glab alias set todos 'issue list --label todo --assignee @me'
```

---

## Configuration

### Global Settings

```bash
# Set default GitLab host
glab config set host gitlab.company.com

# Set default editor
glab config set editor vim
glab config set editor "code --wait"

# Set default protocol
glab config set git_protocol ssh
glab config set git_protocol https

# Set default browser
glab config set browser firefox

# View configuration
glab config get editor

# List all config
glab config
```

### Repository-Specific Settings

```bash
# Set default remote
glab repo set-default group/project
```

---

## Advanced Workflows

### CI/CD Integration

```bash
# Trigger pipeline and monitor
glab ci run --branch main
pipeline_id=$(glab ci list --per-page 1 --format json | jq -r '.[0].id')
glab ci view $pipeline_id

# Wait for pipeline to complete
while [ "$(glab ci status --format json | jq -r '.status')" == "running" ]; do
  sleep 10
done

# Download artifacts if successful
if [ "$(glab ci status --format json | jq -r '.status')" == "success" ]; then
  glab ci artifact $pipeline_id
fi
```

### Automated MR Workflow

```bash
# Create feature branch, make changes, open MR
git checkout -b feature/new-feature
# ... make changes ...
git commit -am "Add new feature"
git push -u origin feature/new-feature
glab mr create --fill --remove-source-branch --squash-before-merge

# Auto-merge when pipeline passes
glab mr merge --when-pipeline-succeeds --remove-source-branch
```

### Bulk Operations

```bash
# Close all stale issues
glab issue list --label stale --format json | jq -r '.[].iid' | xargs -I {} glab issue close {}

# Approve all your assigned MRs
glab mr list --assignee @me --format json | jq -r '.[].iid' | xargs -I {} glab mr approve {}

# Add label to all open issues
glab issue list --state opened --format json | jq -r '.[].iid' | xargs -I {} glab issue update {} --label needs-triage
```

### JSON Output and Processing

```bash
# Get MR details as JSON
glab mr view 123 --format json

# Custom JSON fields with jq
glab mr list --format json | jq '.[] | {iid, title, author: .author.username}'

# Export to CSV
glab issue list --all --format json | jq -r '.[] | [.iid, .title, .state, .created_at] | @csv' > issues.csv
```

---

## GitLab-Specific Features

### Container Registry

```bash
# List container repositories
glab registry list

# View repository tags
glab registry list --tags

# Delete tag
glab registry delete <repository-id> --tag v1.0.0
```

### Wiki Management

```bash
# List wiki pages
glab wiki list

# View wiki page
glab wiki view Home

# Create wiki page
glab wiki create "New Page" --message "Initial content"

# Update wiki page
glab wiki update "Page Title" --message "Updated content"
```

### Project Access Tokens

```bash
# Create project access token
glab project-token create --name ci-token --scopes api,read_repository --expires-at 2027-01-01

# List project tokens
glab project-token list

# Revoke token
glab project-token revoke <token-id>
```

---

## Troubleshooting

### Common Issues

**Authentication Problems:**
```bash
# Check token scopes
glab auth status

# Refresh authentication
glab auth login --hostname gitlab.com

# Use different token
glab auth login --hostname gitlab.com --token new_token
```

**API Rate Limiting:**
```bash
# GitLab has generous rate limits (typically 2000 requests/min for authenticated users)
# Check headers for rate limit info
glab api --include projects/:id | grep -i ratelimit
```

**Network Issues:**
```bash
# Check connectivity
glab api projects

# Use verbose mode
GLAB_DEBUG=1 glab mr list
```

**Debugging:**
```bash
# Enable debug logging
export GLAB_DEBUG=1
glab mr create

# Check version
glab version

# Update glab
brew upgrade glab  # macOS/Linux (Homebrew)
# Or download latest from GitLab releases
```

---

## Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| `GITLAB_TOKEN` | Authentication token | `export GITLAB_TOKEN="glpat-xxxxx"` |
| `GITLAB_URI` | GitLab instance URL | `export GITLAB_URI="https://gitlab.company.com"` |
| `GITLAB_API_HOST` | API hostname | `export GITLAB_API_HOST="gitlab.company.com"` |
| `VISUAL` / `EDITOR` | Default editor | `export EDITOR="vim"` |
| `BROWSER` | Default browser | `export BROWSER="firefox"` |
| `GLAB_DEBUG` | Enable debug mode | `export GLAB_DEBUG=1` |
| `NO_COLOR` | Disable color output | `export NO_COLOR=1` |

---

## Best Practices

### Security

- **Use project access tokens** for CI/CD instead of personal tokens
- **Rotate tokens regularly** (set expiration dates)
- **Use minimal scopes** (api vs read_api)
- **Never commit tokens** to repositories
- **Enable 2FA** on GitLab account
- **Use protected variables** for sensitive CI/CD data

### Efficiency

- **Create aliases** for frequently used commands
- **Use JSON output** (`--format json`) for scripting
- **Leverage tab completion** (install completions: `glab completion bash`)
- **Use `--web`** flag to open resources in browser
- **Combine with `jq`** for JSON parsing

### Automation

- **Use `glab` in CI/CD pipelines** for GitLab API operations
- **Check exit codes** for error handling
- **Use project-scoped tokens** for automation
- **Cache artifacts** to avoid re-running expensive jobs
- **Use variables** for configuration management

---

## Comparison: glab vs gh

| Feature | glab (GitLab) | gh (GitHub) |
|---------|---------------|-------------|
| Create PR/MR | `glab mr create` | `gh pr create` |
| List issues | `glab issue list` | `gh issue list` |
| Clone repo | `glab repo clone` | `gh repo clone` |
| View pipeline | `glab ci view` | `gh run view` |
| Trigger workflow | `glab ci run` | `gh workflow run` |
| Container registry | `glab registry list` | N/A (separate tool) |
| Wiki management | `glab wiki list` | N/A |
| Snippets | `glab snippet create` | `gh gist create` |
| Milestones | `glab milestone list` | N/A |

**Key Differences:**
- GitLab has integrated **Container Registry** and **Wiki** management
- GitLab uses **MR (Merge Request)** instead of PR (Pull Request)
- GitLab has **Project Access Tokens** separate from personal tokens
- GitLab has built-in **Milestones** management
- Both support GraphQL and REST APIs

---

## Comparison: glab vs git

| Task | git | glab |
|------|-----|------|
| Clone repo | `git clone git@gitlab.com:group/project.git` | `glab repo clone group/project` |
| Create MR | N/A (requires browser) | `glab mr create` |
| Merge MR | N/A (requires browser) | `glab mr merge 123` |
| View issues | N/A | `glab issue list` |
| Trigger pipeline | N/A | `glab ci run` |
| View pipeline logs | N/A | `glab ci trace 123` |

**Use Together:**
```bash
# Create branch and MR workflow
git checkout -b feature/new-feature
# ... make changes ...
git commit -am "Add feature"
git push -u origin feature/new-feature
glab mr create --fill --remove-source-branch
```

---

## Resources

- **Official Documentation:** [https://gitlab.com/gitlab-org/cli/-/tree/main/docs](https://gitlab.com/gitlab-org/cli/-/tree/main/docs)
- **GitLab Repository:** [https://gitlab.com/gitlab-org/cli](https://gitlab.com/gitlab-org/cli)
- **Issue Tracker:** [https://gitlab.com/gitlab-org/cli/-/issues](https://gitlab.com/gitlab-org/cli/-/issues)
- **Changelog:** [https://gitlab.com/gitlab-org/cli/-/releases](https://gitlab.com/gitlab-org/cli/-/releases)
- **GitLab API Documentation:** [https://docs.gitlab.com/ee/api/](https://docs.gitlab.com/ee/api/)

---

**Last Updated:** 2026-05-27  
**glab Version:** 1.x+
