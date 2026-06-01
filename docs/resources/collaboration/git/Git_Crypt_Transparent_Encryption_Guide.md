# Git-Crypt: Transparent Repository Encryption Guide

## Overview

**Git-crypt** enables transparent encryption of files in a Git repository. Files are encrypted when committed and decrypted when checked out, making it seamless to work with sensitive data while keeping it secure in the repository.

## Key Concept: Transparent Encryption

```
┌─────────────────────────────────────────────────────┐
│  Your Workflow (No Manual Steps!)                  │
├─────────────────────────────────────────────────────┤
│  1. Edit file (plaintext)                          │
│  2. git add file                                    │
│  3. git commit  ← Git-crypt encrypts automatically │
│  4. git push    ← Encrypted version goes to remote │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│  What Git-crypt Does Automatically                  │
├─────────────────────────────────────────────────────┤
│  • Commit:   Encrypts files before storing         │
│  • Checkout: Decrypts files after fetching         │
│  • Local:    Always plaintext (if unlocked)        │
│  • Remote:   Always encrypted                      │
└─────────────────────────────────────────────────────┘
```

## Installation

### Fedora/RHEL
```bash
sudo dnf install git-crypt
```

### Verification
```bash
git-crypt --version
# Expected: git-crypt 0.7.0 or newer
```

## Repository Setup (One-Time)

### 1. Initialize Git-crypt in Repository

```bash
cd /path/to/your/repo
git-crypt init
```

This creates:
- `.git-crypt/` directory (encryption keys)
- Default symmetric key for encryption

### 2. Configure Which Files to Encrypt

Create `.gitattributes` file:

```bash
# .gitattributes
# Encrypt everything in secrets/ directory
secrets/** filter=git-crypt diff=git-crypt

# Encrypt specific file types
*.key filter=git-crypt diff=git-crypt
*.pem filter=git-crypt diff=git-crypt
*.env filter=git-crypt diff=git-crypt

# Encrypt specific files
config/production.yaml filter=git-crypt diff=git-crypt
database/credentials.json filter=git-crypt diff=git-crypt
```

### 3. Add GPG Users (Recommended for Teams)

```bash
# Add user by GPG key ID
git-crypt add-gpg-user <GPG_KEY_ID>

# Add multiple users
git-crypt add-gpg-user USER1_KEY_ID
git-crypt add-gpg-user USER2_KEY_ID

# List trusted keys
ls .git-crypt/keys/default/0/
```

**Why GPG?**
- Each team member uses their own GPG key
- No shared symmetric key to distribute
- Revocable access (remove key from repo)

### 4. Commit Configuration

```bash
git add .gitattributes .git-crypt/
git commit -m "feat: Add git-crypt encryption for secrets/"
git push
```

## Daily Usage (After Setup)

### Unlock Repository (First Time on New Machine)

```bash
cd /path/to/repo
git-crypt unlock

# Or with GPG (automatic if your key is added)
# Git-crypt detects your GPG key and unlocks automatically
```

### Check Encryption Status

```bash
# See which files are encrypted
git-crypt status

# Output example:
#     encrypted: secrets/node_known_hosts
#     encrypted: secrets/node_ssh_config
# not encrypted: README.md
# not encrypted: .gitattributes
```

### Working with Encrypted Files

**THE MAGIC: You don't do anything different!**

```bash
# 1. Edit file (it's plaintext locally)
vim secrets/database.yaml

# 2. Normal git workflow
git add secrets/database.yaml
git commit -m "Update database credentials"
git push

# Git-crypt automatically encrypts during commit!
# No manual encryption step needed!
```

### Verify File is Encrypted in Repository

```bash
# Check raw file in .git (shows encrypted)
git show HEAD:secrets/database.yaml
# Output: binary garbage (encrypted)

# Check working tree (shows plaintext if unlocked)
cat secrets/database.yaml
# Output: readable YAML
```

## Real-World Example: brew-jenkins-csb

### Repository Structure
```
brew-jenkins-csb/
├── .gitattributes          # Defines encryption rules
├── .git-crypt/
│   └── keys/
│       └── default/
│           └── 0/
│               ├── <GPG_KEY_1>.gpg
│               └── <GPG_KEY_2>.gpg
├── secrets/
│   ├── node_known_hosts    # Encrypted (SSH host keys)
│   ├── node_ssh_config     # Encrypted (SSH client config)
│   └── id_rsa_rcm_auto_compose  # Encrypted (SSH private key)
└── casc.yaml              # Not encrypted (references encrypted files)
```

### .gitattributes Configuration
```bash
# From brew-jenkins-csb
secrets/** filter=git-crypt diff=git-crypt
```

### Workflow Example (SPRE-5388 Fix)

```bash
# 1. Repository already unlocked (automatic via GPG)
cd ~/repos/brew-jenkins-csb

# 2. Edit encrypted file (plaintext locally!)
vim secrets/node_known_hosts
# Update SSH host keys...

# 3. Normal git workflow
git add secrets/node_known_hosts
git commit -m "fix: Update SSH host keys for rcm-compose agents"
git push origin master

# Behind the scenes:
# - git add:    File staged (still plaintext in index)
# - git commit: Git-crypt encrypts before storing in object DB
# - git push:   Encrypted blobs sent to GitLab
# - GitLab UI:  Shows encrypted binary (✓ secure!)
# - CI/CD:      Decrypts with GPG key, deploys to Jenkins
```

## Common Operations

### Lock Repository (Encrypt Working Tree)

```bash
git-crypt lock

# After locking:
# - Encrypted files show as binary in working tree
# - Cannot read secrets without unlocking
# - Useful before leaving machine
```

### Unlock Repository

```bash
# With GPG (if your key is added)
git-crypt unlock

# With symmetric key (legacy/emergency)
git-crypt unlock /path/to/keyfile
```

### Export Symmetric Key (Backup/Sharing)

```bash
# Export key for emergency access
git-crypt export-key /path/to/git-crypt-key

# Store securely (password manager, vault)
# Can unlock with: git-crypt unlock /path/to/git-crypt-key
```

### Add New Team Member

```bash
# 1. Get their GPG public key ID
gpg --list-keys user@example.com
# pub   rsa4096 2024-01-01 [SC]
#       ABCD1234ABCD1234ABCD1234ABCD1234ABCD1234

# 2. Add to git-crypt
git-crypt add-gpg-user ABCD1234ABCD1234ABCD1234ABCD1234ABCD1234

# 3. Commit and push
git add .git-crypt/
git commit -m "feat: Add user@example.com to git-crypt"
git push

# 4. New user clones and unlocks automatically!
```

### Remove Team Member

```bash
# 1. Remove their key
rm .git-crypt/keys/default/0/<THEIR_KEY_ID>.gpg

# 2. Re-encrypt everything with new key set
git-crypt lock
git-crypt init  # Generates new symmetric key
git-crypt add-gpg-user <REMAINING_USER_1>
git-crypt add-gpg-user <REMAINING_USER_2>

# 3. Commit changes
git add .git-crypt/
git commit -m "security: Revoke access for departed user"
git push

# Old key can no longer decrypt new commits!
```

## Security Best Practices

### ✅ DO

1. **Use GPG keys for team access** (not shared symmetric keys)
2. **Encrypt entire directories** (`secrets/**`) not individual files
3. **Keep .gitattributes in version control** (team knows what's encrypted)
4. **Back up symmetric key securely** (password manager, vault)
5. **Lock repository when leaving workstation** (`git-crypt lock`)
6. **Verify files are encrypted** (`git-crypt status`)

### ❌ DON'T

1. **Don't commit .git-crypt/keys/ to public repos** (keep repo private!)
2. **Don't share symmetric key via email/Slack** (use GPG instead)
3. **Don't decrypt and commit plaintext** (defeats purpose!)
4. **Don't mix encrypted/unencrypted versions** (confusing, dangerous)
5. **Don't assume files are encrypted** (always check `git-crypt status`)

## Troubleshooting

### Files Not Encrypting

**Symptom:** `git-crypt status` shows "not encrypted" for files in `secrets/`

**Solution:**
```bash
# 1. Check .gitattributes pattern
cat .gitattributes
# Should have: secrets/** filter=git-crypt diff=git-crypt

# 2. Force re-encryption
git rm --cached secrets/file.txt
git add secrets/file.txt
git commit -m "Re-encrypt file"

# 3. Verify
git-crypt status
```

### Repository Won't Unlock

**Symptom:** `git-crypt unlock` fails with "GPG key not found"

**Solution:**
```bash
# 1. Check GPG keys
gpg --list-secret-keys

# 2. Ensure your key is added to repo
ls .git-crypt/keys/default/0/
# Should contain your key ID

# 3. Try with symmetric key
git-crypt unlock /path/to/backup-key

# 4. Ask admin to add your GPG key
```

### Accidentally Committed Plaintext

**Symptom:** Secret was committed without encryption

**Solution:**
```bash
# CRITICAL: Follow Git_Secret_Leak_Remediation_Guide.md
# This requires rewriting history!

# 1. Remove from current commit
git rm --cached secrets/leaked-file
echo "secrets/leaked-file filter=git-crypt diff=git-crypt" >> .gitattributes
git add .gitattributes
git commit --amend

# 2. Rewrite history (if already pushed)
# See: Git_Secret_Leak_Remediation_Guide.md
# This invalidates all clones!

# 3. Rotate leaked credentials immediately
```

### Files Show as Binary Locally

**Symptom:** Working tree shows encrypted files (binary)

**Solution:**
```bash
# Repository is locked - unlock it
git-crypt unlock

# Files should now show as plaintext
cat secrets/file.txt
```

## Integration with CI/CD

### GitLab CI/CD Example

```yaml
# .gitlab-ci.yml
variables:
  GIT_CRYPT_KEY_BASE64: "$CI_GIT_CRYPT_KEY"  # Set in GitLab CI/CD variables

before_script:
  - dnf install -y git-crypt
  - echo "$GIT_CRYPT_KEY_BASE64" | base64 -d > /tmp/git-crypt-key
  - git-crypt unlock /tmp/git-crypt-key
  - rm /tmp/git-crypt-key

deploy:
  script:
    - cat secrets/database.yaml  # Now plaintext!
    - ./deploy.sh
```

### Jenkins Pipeline Example

```groovy
// Jenkinsfile
pipeline {
    agent any
    stages {
        stage('Unlock Secrets') {
            steps {
                withCredentials([file(credentialsId: 'git-crypt-key', variable: 'KEY')]) {
                    sh 'git-crypt unlock $KEY'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'kubectl create secret generic app-secrets --from-file=secrets/'
            }
        }
    }
}
```

## Comparison with Alternatives

| Feature | git-crypt | git-secret | SOPS | Vault |
|---------|-----------|------------|------|-------|
| Transparent encryption | ✅ | ❌ | ❌ | ❌ |
| Per-file encryption | ✅ | ✅ | ✅ | N/A |
| GPG support | ✅ | ✅ | ✅ | ❌ |
| Cloud KMS support | ❌ | ❌ | ✅ | ✅ |
| Works offline | ✅ | ✅ | Partial | ❌ |
| Learning curve | Low | Medium | Medium | High |
| Red Hat usage | Common | Less | Growing | Common |

**When to use git-crypt:**
- ✅ Small number of secrets (config files, keys)
- ✅ Secrets tied to specific git commits
- ✅ Team needs transparent workflow
- ✅ No external infrastructure needed

**When NOT to use git-crypt:**
- ❌ Secrets need rotation without git commits
- ❌ Secrets shared across many repos
- ❌ Need cloud-based key management (AWS KMS, etc.)
- ❌ Very large binary secrets (use external vault)

## Real-World Use Cases at Red Hat

### 1. Jenkins Configuration as Code (JCasC)
```
brew-jenkins-csb/
├── casc.yaml                    # Public (references secrets)
├── secrets/
│   ├── node_known_hosts         # Git-crypt encrypted
│   ├── node_ssh_config          # Git-crypt encrypted
│   └── id_rsa_rcm_auto_compose  # Git-crypt encrypted
```

**Deployment:**
- GitLab CI/CD unlocks repo
- Mounts secrets into OpenShift pod
- Jenkins reads plaintext at `/run/secrets/casc-secret/`

### 2. Ansible Inventories with Vault
```
ansible-playbooks/
├── inventory/
│   ├── production.yml           # Git-crypt encrypted
│   └── staging.yml              # Git-crypt encrypted
├── group_vars/
│   └── all/
│       └── vault.yml            # Git-crypt encrypted
```

### 3. Kubernetes Secrets in Git
```
k8s-manifests/
├── base/
│   └── deployment.yaml          # Public
├── overlays/
│   └── production/
│       └── secrets.yaml         # Git-crypt encrypted
```

## Advanced: Selective Encryption

### Pattern-Based Encryption

```bash
# .gitattributes
# Encrypt all .env files
**/.env filter=git-crypt diff=git-crypt

# Encrypt production configs only
config/production/** filter=git-crypt diff=git-crypt

# Except specific files
config/production/README.md !filter !diff

# Encrypt by extension
*.key filter=git-crypt diff=git-crypt
*.pem filter=git-crypt diff=git-crypt
*.crt filter=git-crypt diff=git-crypt
```

### Per-Directory Keys (Advanced)

```bash
# Use different keys for different teams/directories
# .gitattributes
secrets/team-a/** filter=git-crypt-team-a diff=git-crypt-team-a
secrets/team-b/** filter=git-crypt-team-b diff=git-crypt-team-b

# Requires custom git-crypt configuration
# See: https://github.com/AGWA/git-crypt#multiple-keys
```

## Quick Reference

### Essential Commands

```bash
# Setup
git-crypt init                           # Initialize in repo
git-crypt add-gpg-user <KEY_ID>         # Add team member
git-crypt export-key /path/to/key       # Backup symmetric key

# Daily use
git-crypt unlock                         # Decrypt working tree
git-crypt lock                           # Encrypt working tree
git-crypt status                         # Check encryption status

# Verification
git show HEAD:secrets/file               # View encrypted (binary)
cat secrets/file                         # View decrypted (plaintext)
```

### Status Indicators

```bash
git-crypt status

    encrypted: secrets/passwords.txt     # ✅ Encrypted correctly
not encrypted: README.md                 # ✅ Public file (expected)
not encrypted: secrets/api-key.txt       # ⚠️  Should be encrypted!
```

## Resources

- **Official Documentation:** https://github.com/AGWA/git-crypt
- **GPG Key Management:** https://www.gnupg.org/gph/en/manual.html
- **Git Attributes:** https://git-scm.com/docs/gitattributes
- **Secret Leak Remediation:** `Git_Secret_Leak_Remediation_Guide.md` (this compendium)
- **Pre-commit Hooks:** `Pre_Commit_Hooks_Secret_Detection_Guide.md` (this compendium)

## Summary

**Git-crypt provides transparent encryption** - you work with plaintext locally, git-crypt handles encryption automatically during commit/push. Perfect for:

✅ **Simplicity** - No manual encryption steps  
✅ **Security** - Files encrypted at rest in repository  
✅ **Team-friendly** - GPG key-based access control  
✅ **Git-native** - Integrates seamlessly with git workflow  

**Key takeaway:** After initial setup, you literally don't think about encryption - it just works! 🔒✨
