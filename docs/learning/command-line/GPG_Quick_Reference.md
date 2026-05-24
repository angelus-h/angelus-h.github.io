# GPG (GnuPG) Quick Reference

## Overview

**GnuPG (GPG)** is an open-source PGP (Pretty Good Privacy) implementation providing asymmetric encryption, digital signatures, and key management.

**Main Use Cases:**
- Email encryption and signing
- File encryption/decryption
- Git commit signing
- Software package verification (RPM, APT)
- Secure storage of secrets (passwords, API keys)

**Check Version:**
```bash
gpg --version
```

---

## 1. Key Pair Generation and Management

### Generate New Key Pair

```bash
# Full interactive generation (recommended for beginners)
gpg --full-generate-key

# Quick generation with default settings
gpg --gen-key

# Batch mode (for automation)
gpg --batch --gen-key keygen-params.txt
```

**Interactive Generation Steps:**
1. **Key type:** (1) RSA and RSA (recommended)
2. **Key size:** 4096 bits (stronger than 2048)
3. **Expiration:** 1y (1 year) or 0 (never expires)
4. **Name:** John Doe
5. **Email:** john.doe@example.com
6. **Passphrase:** Strong passphrase (12+ characters)

### List Keys

```bash
# Public keys
gpg --list-keys
# or
gpg -k

# Private keys
gpg --list-secret-keys
# or
gpg -K

# Detailed info for specific key
gpg --list-keys --keyid-format LONG john.doe@example.com

# Display fingerprint
gpg --fingerprint john.doe@example.com
```

### Export Keys

```bash
# Export public key (ASCII armor format)
gpg --armor --export john.doe@example.com > public-key.asc

# Export private key (STORE SECURELY!)
gpg --armor --export-secret-keys john.doe@example.com > private-key.asc

# Export private key + subkeys
gpg --armor --export-secret-keys --export-options export-backup john.doe@example.com > backup.asc
```

### Import Keys

```bash
# Import public key
gpg --import public-key.asc

# Import private key
gpg --import private-key.asc

# Import from keyserver
gpg --keyserver keyserver.ubuntu.com --recv-keys KEY_ID

# Import from URL
curl https://example.com/key.asc | gpg --import
```

### Delete Keys

```bash
# Delete public key (must delete private first!)
gpg --delete-keys KEY_ID

# Delete private key
gpg --delete-secret-keys KEY_ID

# Delete both
gpg --delete-secret-and-public-keys KEY_ID
```

---

## 2. File Encryption and Decryption

### Symmetric Encryption (Password-Based)

```bash
# Encrypt file with password (no key pair needed)
gpg --symmetric file.txt
# Creates: file.txt.gpg

# ASCII armor format (email-friendly)
gpg --armor --symmetric file.txt
# Creates: file.txt.asc

# Decrypt (prompts for password)
gpg --decrypt file.txt.gpg > file.txt
# or
gpg -d file.txt.gpg > file.txt
```

### Asymmetric Encryption (Public Key)

```bash
# Encrypt for one recipient
gpg --encrypt --recipient john.doe@example.com file.txt
# Creates: file.txt.gpg

# ASCII format
gpg --armor --encrypt --recipient john.doe@example.com file.txt

# Multiple recipients (all can decrypt)
gpg --encrypt -r alice@example.com -r bob@example.com file.txt

# Encrypt + sign together
gpg --encrypt --sign --recipient john.doe@example.com file.txt
```

### Decryption

```bash
# Decrypt (uses private key automatically)
gpg --decrypt file.txt.gpg > file.txt

# Provide passphrase from command line (DANGEROUS! shell history!)
echo "MyPassphrase" | gpg --batch --yes --passphrase-fd 0 --decrypt file.txt.gpg > file.txt

# Non-interactive mode (disable GUI passphrase prompt)
gpg --pinentry-mode loopback --passphrase "MyPassphrase" --decrypt file.txt.gpg
```

---

## 3. Digital Signatures

### Sign Files

```bash
# Sign (creates file.txt.sig)
gpg --sign file.txt

# Detached signature (signature in separate file, original unchanged)
gpg --detach-sign file.txt
# Creates: file.txt.sig

# ASCII armor format
gpg --armor --detach-sign file.txt
# Creates: file.txt.asc

# Clear-sign (signature + original text in one readable file)
gpg --clearsign file.txt
# Useful for emails
```

### Verify Signatures

```bash
# Verify detached signature
gpg --verify file.txt.sig file.txt

# Verify embedded signature
gpg --verify file.txt.gpg

# Verify clear-signed file
gpg --verify file.txt.asc
```

---

## 4. Key Management and Trust

### Sign Keys (Web of Trust)

```bash
# Authenticate key (after verifying fingerprint)
gpg --sign-key alice@example.com

# Verify key fingerprint (phone, in-person)
gpg --fingerprint alice@example.com

# Set trust level for key
gpg --edit-key alice@example.com
gpg> trust
# 1 = I don't know
# 2 = I do NOT trust
# 3 = I trust marginally
# 4 = I trust fully
# 5 = I trust ultimately
gpg> quit
```

### Extend Key Expiration

```bash
gpg --edit-key john.doe@example.com
gpg> expire
# Enter new expiration time (e.g., 1y)
gpg> save

# Re-publish public key (so others see the update)
gpg --keyserver keyserver.ubuntu.com --send-keys KEY_ID
```

### Revoke Key

```bash
# Generate revocation certificate (do this in advance!)
gpg --output revoke.asc --gen-revoke john.doe@example.com

# Revoke key (if compromised)
gpg --import revoke.asc
gpg --keyserver keyserver.ubuntu.com --send-keys KEY_ID
```

---

## 5. Keyservers

### Publish Key

```bash
# Upload key to keyserver
gpg --keyserver keyserver.ubuntu.com --send-keys KEY_ID

# Publish to multiple servers
gpg --keyserver keys.openpgp.org --send-keys KEY_ID
gpg --keyserver pgp.mit.edu --send-keys KEY_ID
```

### Search Keys

```bash
# Search by email address
gpg --keyserver keyserver.ubuntu.com --search-keys john.doe@example.com

# Download key by KEY_ID
gpg --keyserver keyserver.ubuntu.com --recv-keys 1234ABCD5678EFGH
```

### Refresh Keys (Sync from Keyserver)

```bash
# Refresh all keys
gpg --keyserver keyserver.ubuntu.com --refresh-keys

# Refresh specific key
gpg --keyserver keyserver.ubuntu.com --refresh-keys KEY_ID
```

---

## 6. Git Commit Signing

### Configure GPG for Git

```bash
# Get key ID
gpg --list-secret-keys --keyid-format LONG
# Output example:
# sec rsa4096/1234ABCD5678EFGH 2024-01-01 [SC]
# ^^^^^^^^^^^^^^^^^^^^^^^ <- This is the KEY_ID

# Configure Git
git config --global user.signingkey 1234ABCD5678EFGH
git config --global commit.gpgSign true # Auto-sign all commits
git config --global tag.gpgSign true # Auto-sign all tags
```

### Create Signed Commit

```bash
# One-time signed commit
git commit -S -m "Commit message"

# Automatic (if gpgSign = true)
git commit -m "Commit message"

# Signed tag
git tag -s v1.0.0 -m "Version 1.0.0"
```

### Verify Signatures

```bash
# Verify last commit signature
git verify-commit HEAD

# Verify tag signature
git verify-tag v1.0.0

# Show commit log with signatures
git log --show-signature
```

### GitHub/GitLab Integration

```bash
# Export public key (paste into GitHub/GitLab Settings)
gpg --armor --export john.doe@example.com

# GitHub: Settings → SSH and GPG keys → New GPG key
# GitLab: Preferences → GPG Keys → Add new key
```

---

## 7. Software Package Verification

### Verify RPM Package Signature (Red Hat/Fedora)

```bash
# Import RPM public key
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-41-x86_64

# Verify RPM signature
rpm --checksig package.rpm
# OK output: package.rpm: digests signatures OK

# Verify with GPG directly
gpg --verify package.rpm.asc package.rpm
```

### Verify Debian/Ubuntu Package

```bash
# Import APT key
wget -qO- https://example.com/gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/example.gpg

# Verify Release file signature
gpg --verify Release.gpg Release
```

---

## 8. GPG Agent and PIN Management

### Start GPG Agent

```bash
# Agent info
gpg-agent --daemon

# Restart agent (if stuck)
gpgconf --kill gpg-agent
gpgconf --launch gpg-agent
```

### Passphrase Cache Settings

```bash
# Edit ~/.gnupg/gpg-agent.conf
default-cache-ttl 3600 # 1 hour (default: 10 minutes)
max-cache-ttl 86400 # Max 24 hours
pinentry-program /usr/bin/pinentry-gtk-2 # GUI passphrase prompt
```

```bash
# Reload config
gpg-connect-agent reloadagent /bye
```

### Clear Passphrase Cache

```bash
# Clear all cached passphrases
gpgconf --reload gpg-agent
```

---

## 9. Useful One-Liners

```bash
# Get KEY_ID from email
gpg --list-keys --keyid-format LONG john.doe@example.com | grep pub | awk '{print $2}' | cut -d'/' -f2

# Encrypt file to yourself (your own key)
gpg --encrypt --recipient $(gpg --list-keys --keyid-format LONG | grep uid | head -1 | awk '{print $NF}') file.txt

# Encrypt all .txt files in a directory
for file in *.txt; do gpg --encrypt --recipient john.doe@example.com "$file"; done

# Decrypt all .gpg files
for file in *.gpg; do gpg --decrypt "$file" > "${file%.gpg}"; done

# Get key fingerprint in one line
gpg --fingerprint john.doe@example.com | grep -oP '[0-9A-F ]{40}' | tr -d ' '

# Check expiring keys (expiring within 30 days)
gpg --list-keys --with-colons | grep ^pub | awk -F: '{if ($7 != "" && $7 < systime() + 2592000) print $5, $7}'

# Encrypt from stdin to stdout (for pipelines)
echo "Secret message" | gpg --encrypt --armor --recipient john.doe@example.com

# Encrypt password (secure storage)
echo "MyDatabasePassword" | gpg --symmetric --armor > db-password.asc
# Decrypt: gpg --decrypt db-password.asc
```

---

## 10. Kubernetes/OpenShift Context

### Sealed Secrets (Bitnami) with GPG

```bash
# Fetch Sealed Secrets public key
kubeseal --fetch-cert > sealed-secrets-pub.pem

# Encrypt secret
echo -n "my-secret-password" | kubectl create secret generic my-secret \
 --dry-run=client --from-file=password=/dev/stdin -o yaml | \
 kubeseal -o yaml > sealed-secret.yaml

# Apply
kubectl apply -f sealed-secret.yaml
```

### SOPS (Mozilla) - Encrypt Secrets with GPG

```bash
# Install SOPS
# https://github.com/mozilla/sops

# Configure GPG key in .sops.yaml
# creation_rules:
# - pgp: '1234ABCD5678EFGH'

# Encrypt secret
sops --encrypt --pgp 1234ABCD5678EFGH secrets.yaml > secrets.enc.yaml

# Decrypt
sops --decrypt secrets.enc.yaml

# Create Kubernetes Secret from SOPS
sops --decrypt secrets.enc.yaml | kubectl apply -f -
```

### Git-crypt (Full Repo Encryption)

```bash
# Install git-crypt
sudo dnf install git-crypt

# Initialize
git-crypt init

# Add GPG user
git-crypt add-gpg-user john.doe@example.com

# Configure .gitattributes (what to encrypt)
secretfile.txt filter=git-crypt diff=git-crypt
.env filter=git-crypt diff=git-crypt

# Unlock (after cloning)
git-crypt unlock
```

---

## 11. Security and Best Practices

### DO

- **Use strong passphrase:** Minimum 12 characters, numbers/symbols
- **Backup private key:** Store securely (offline USB, password manager)
- **Generate revocation certificate in advance:** If you lose the key, you can revoke it
- **Use expiration time:** 1-2 years, then extend
- **Verify fingerprints:** Before signing/importing keys
- **Use 4096 bit keys:** Stronger than 2048 bit

### DON'T

- **Never share private key:** NEVER send via email/chat
- **Don't store passphrase in plaintext:** Don't write down, don't put in environment variables
- **Don't use weak keys:** 1024 bit RSA is obsolete
- **Don't ignore expiration:** Refresh regularly
- **Don't blindly trust keyservers:** Verify fingerprints through another channel

### Secure Passphrase Handling

```bash
# DON'T DO THIS:
export GPG_PASSPHRASE="MySecretPassword" # Stays in shell history!
gpg --passphrase "$GPG_PASSPHRASE" --decrypt file.gpg

# Use gpg-agent instead (caches passphrase)
gpg --decrypt file.gpg # Prompts once, then cached
```

---

## 12. Troubleshooting

### "gpg: decryption failed: No secret key"

**Cause:** Private key not available or not imported.

**Solution:**
```bash
# Check if you have private key
gpg --list-secret-keys

# Import private key
gpg --import private-key.asc
```

### "gpg: signing failed: Inappropriate ioctl for device"

**Cause:** GPG cannot prompt for PIN (headless environment, SSH session).

**Solution:**
```bash
# Add to .bashrc or .zshrc:
export GPG_TTY=$(tty)

# Or in gpg-agent.conf:
pinentry-program /usr/bin/pinentry-curses # CLI-friendly
```

### "gpg: WARNING: unsafe permissions on homedir"

**Cause:** ~/.gnupg directory permissions too permissive.

**Solution:**
```bash
chmod 700 ~/.gnupg
chmod 600 ~/.gnupg/*
```

### Git Commit Signing Not Working

```bash
# Check if Git knows GPG program
git config --global gpg.program gpg

# Verify key ID
git config --global user.signingkey

# Test GPG Agent
echo "test" | gpg --clearsign
```

---

## References

**Official Documentation:**
- https://gnupg.org/documentation/
- `man gpg` - GPG man pages
- `man gpg-agent` - GPG agent

**Useful Links:**
- GPG Best Practices: https://riseup.net/en/security/message-security/openpgp/gpg-best-practices
- Git Commit Signing: https://docs.github.com/en/authentication/managing-commit-signature-verification
- SOPS (Secret Management): https://github.com/mozilla/sops

**Red Hat/Fedora Specific:**
- RPM GPG Keys: `/etc/pki/rpm-gpg/`
- GPG config: `~/.gnupg/`

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** Security, DevOps, Software Engineering
