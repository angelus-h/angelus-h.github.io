# Learning Plan: HashiCorp Vault Secrets Management

##  Learning Objectives

Understanding HashiCorp Vault operation and learning to securely manage secrets in modern infrastructure - with focus on Company/Platform environments.

---

## 1⃣ Module: What is Vault and Why Do We Need It?

### What you will learn:
- Secrets management problem
- Vault architecture basics
- Sealed vs. Unsealed states
- Backend storage types

### The Problem: Secrets Everywhere

**Bad practices (NEVER do this):**
```bash
#  In Git repository
export DB_PASSWORD="mypassword123"

#  In Kubernetes ConfigMap as plaintext
apiVersion: v1
kind: ConfigMap
data:
  database-password: "mypassword123"

#  Hardcoded in Terraform file
resource "aws_db_instance" "prod" {
  password = "mypassword123"  # In GIT!
}
```

**What's wrong with these?**
- Stays in Git history forever
- Visible to anyone with repo access
- Hard to rotate (change)
- No audit trail (who used it, when?)

### The Solution: HashiCorp Vault

**Vault = Centralized Secrets Management**

```
┌─────────────────────────────────────────┐
│         HashiCorp Vault                 │
│  ┌────────────────────────────────┐    │
│  │  Encrypted Storage Backend     │    │
│  │  (etcd, Consul, Filesystem)    │    │
│  └────────────────────────────────┘    │
│                                         │
│  Authentication:                        │
│  - Token                                │
│  - Kubernetes ServiceAccount            │
│  - AppRole (CI/CD)                      │
│  - LDAP/OIDC (users)                    │
└─────────────────────────────────────────┘
         ↓          ↓          ↓
    App Pods    Terraform   GitLab CI
```

### Vault Core Concepts

**Sealed vs. Unsealed:**
- **Sealed:** Vault is "locked", encryption key not in memory → cannot access secrets
- **Unsealed:** Unlocked with unseal keys → normal operation
- **Auto-unseal:** Automatic with Cloud KMS (AWS KMS, Azure Key Vault, Google Cloud KMS)

**Secret Engines:**
- **KV (Key-Value):** Static secrets (API tokens, passwords)
- **Database:** Dynamic database credentials (MySQL, PostgreSQL)
- **PKI:** TLS certificate generation
- **Transit:** Encryption as a Service (data encryption)

**Paths:**
```
secret/data/prod/database/password
  ↑     ↑    ↑      ↑         ↑
  |     |    |      |         +-- Key
  |     |    |      +------------ Secret name
  |     |    +------------------- Environment
  |     +------------------------ "data" (due to KV v2)
  +------------------------------- Secret engine name
```

### 📖 Practice 1: Vault Installation Check

**Task:** Check if Vault is installed and what version.

```bash
# Vault CLI check
vault version

# If not installed (Fedora):
sudo dnf install vault

# Or download:
wget https://releases.hashicorp.com/vault/1.15.4/vault_1.15.4_linux_amd64.zip
unzip vault_1.15.4_linux_amd64.zip
sudo mv vault /usr/local/bin/
```

**Expected result:**
```
Vault v1.15.4 (built 2024-01-15T12:34:56Z)
```

---

## 2⃣ Module: Dev Mode - First Steps

### What you will learn:
- Starting Vault dev server
- Basic CLI commands
- Writing/reading secrets
- Token usage

### Vault Dev Mode (For Practice)

**WARNING:** Dev mode ONLY for learning! Stores everything in memory, deletes all on restart!

```bash
# Start dev server
vault server -dev

# In new terminal:
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN='<root token from output>'

# Status check
vault status
```

**Output interpretation:**
```
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false      ← IMPORTANT: false = working
Total Shares    1
Threshold       1
Cluster Name    vault-cluster-abc123
```

### 📖 Practice 2: First Secret Management

**1. Writing secret:**
```bash
# Using KV v2 engine (default in dev mode)
vault kv put secret/myapp/config username='admin' password='supersecret'

# Multiple values at once
vault kv put secret/prod/database \
  host='db.example.com' \
  port=5432 \
  username='dbuser' \
  password='dbpass123'
```

**2. Reading secret:**
```bash
# Full secret
vault kv get secret/myapp/config

# Single field only
vault kv get -field=password secret/myapp/config

# JSON format (for scripts)
vault kv get -format=json secret/myapp/config | jq .data.data.password
```

**3. Listing secrets:**
```bash
# Existing secrets
vault kv list secret/

# Subdirectories
vault kv list secret/prod/
```

**4. Deleting secret:**
```bash
# Soft delete (recoverable)
vault kv delete secret/myapp/config

# Permanently delete
vault kv destroy -versions=1 secret/myapp/config

# Delete metadata too
vault kv metadata delete secret/myapp/config
```

### 📖 Practice 3: Secret Versioning (KV v2)

**KV v2 = automatic versioning!**

```bash
# Version 1
vault kv put secret/api/token value='token_v1'

# Version 2 (overwrites, but v1 stays)
vault kv put secret/api/token value='token_v2'

# Version 3
vault kv put secret/api/token value='token_v3'

# List all versions
vault kv metadata get secret/api/token

# Read old version
vault kv get -version=1 secret/api/token

# Rollback to previous version
vault kv rollback -version=1 secret/api/token
```

**Use case:** Accidentally overwrote API token? Rollback!

---

## 3⃣ Module: Authentication Methods

### What you will learn:
- Token-based auth
- AppRole (CI/CD)
- Kubernetes ServiceAccount auth
- LDAP/OIDC (user auth)

### Token Authentication (Simplest)

**Token types:**
- **Root token:** Superuser, all permissions (DON'T use in production!)
- **Service token:** Long-lived, with specific policies
- **Batch token:** Lightweight, not stored (for high-throughput)

```bash
# Create token
vault token create -policy=myapp-policy -ttl=1h

# Token info
vault token lookup

# Renew token
vault token renew

# Revoke token
vault token revoke <token>
```

### 📖 Practice 4: AppRole Auth (For CI/CD)

**Scenario:** GitLab CI pipeline needs access to Vault secrets.

**1. Enable AppRole:**
```bash
vault auth enable approle
```

**2. Create policy:**
```bash
# policy.hcl file
cat > myapp-policy.hcl <<EOF
path "secret/data/prod/myapp/*" {
  capabilities = ["read", "list"]
}
EOF

# Upload policy
vault policy write myapp-policy myapp-policy.hcl
```

**3. AppRole configuration:**
```bash
# Create role
vault write auth/approle/role/gitlab-ci \
    token_policies="myapp-policy" \
    token_ttl=1h \
    token_max_ttl=4h

# Get Role ID (not secret)
vault read auth/approle/role/gitlab-ci/role-id

# Generate Secret ID (SECRET! Store in GitLab CI Variable!)
vault write -f auth/approle/role/gitlab-ci/secret-id
```

**4. Login with AppRole:**
```bash
# Role ID + Secret ID → Token
vault write auth/approle/login \
    role_id="<role-id>" \
    secret_id="<secret-id>"

# Use returned token
export VAULT_TOKEN="<client_token>"
vault kv get secret/prod/myapp/config
```

**GitLab CI example (.gitlab-ci.yml):**
```yaml
variables:
  VAULT_ADDR: "https://vault.example.com"

deploy:
  stage: deploy
  script:
    # Login to Vault
    - export VAULT_TOKEN=$(vault write -field=token auth/approle/login
        role_id=${APPROLE_ROLE_ID}
        secret_id=${APPROLE_SECRET_ID})

    # Get secrets
    - export DB_PASSWORD=$(vault kv get -field=password secret/prod/database)

    # Deploy app with secret
    - ./deploy.sh
  only:
    - main
```

**Store in GitLab CI Variables:**
- `APPROLE_ROLE_ID` (not secret, can be public)
- `APPROLE_SECRET_ID` (PROTECTED + MASKED!)

### 📖 Practice 5: Kubernetes Auth

**Scenario:** Kubernetes Pods automatic Vault access based on ServiceAccount.

**1. Enable Kubernetes auth:**
```bash
vault auth enable kubernetes

# Configure K8s API endpoint
vault write auth/kubernetes/config \
    kubernetes_host="https://kubernetes.default.svc:443"
```

**2. Create role (service account → policy mapping):**
```bash
vault write auth/kubernetes/role/myapp \
    bound_service_account_names=myapp-sa \
    bound_service_account_namespaces=production \
    policies=myapp-policy \
    ttl=1h
```

**3. Kubernetes Pod usage:**

**ServiceAccount YAML:**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myapp-sa
  namespace: production
```

**Pod YAML (init container Vault login):**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  namespace: production
spec:
  serviceAccountName: myapp-sa

  initContainers:
  - name: vault-agent
    image: vault:1.15.4
    command:
    - sh
    - -c
    - |
      # Login with ServiceAccount JWT
      VAULT_TOKEN=$(vault write -field=token auth/kubernetes/login \
        role=myapp \
        jwt=@/var/run/secrets/kubernetes.io/serviceaccount/token)

      # Get secret
      vault kv get -field=password secret/prod/database > /vault/secrets/db-password

    volumeMounts:
    - name: vault-secrets
      mountPath: /vault/secrets

  containers:
  - name: myapp
    image: myapp:latest
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: myapp-vault-secret
          key: db-password
    volumeMounts:
    - name: vault-secrets
      mountPath: /vault/secrets

  volumes:
  - name: vault-secrets
    emptyDir: {}
```

**Simpler: Vault Agent Injector (Sidecar):**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  annotations:
    vault.hashicorp.com/agent-inject: "true"
    vault.hashicorp.com/role: "myapp"
    vault.hashicorp.com/agent-inject-secret-database: "secret/data/prod/database"
spec:
  serviceAccountName: myapp-sa
  containers:
  - name: myapp
    image: myapp:latest
    # Secret auto-mounted at /vault/secrets/database
```

---

## 4⃣ Module: Policies - Access Control

### What you will learn:
- Policy syntax (HCL)
- Capabilities (read, write, delete, list)
- Path-based access control
- Policy best practices

### Policy Syntax

**HCL format:**
```hcl
# Read-only access to production database secrets
path "secret/data/prod/database/*" {
  capabilities = ["read", "list"]
}

# Full access to dev environment
path "secret/data/dev/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

# Deny access to super-secret
path "secret/data/prod/super-secret" {
  capabilities = ["deny"]
}

# AppRole-specific: can only read its own secrets
path "secret/data/apps/{{identity.entity.aliases.AUTH_METHOD_ACCESSOR.metadata.role_name}}/*" {
  capabilities = ["read"]
}
```

### Capabilities Explained

| Capability | Meaning | Use For |
|------------|---------|---------|
| `create` | Create new secret | First write |
| `read` | Read secret | GET requests |
| `update` | Modify existing secret | PUT requests |
| `delete` | Delete secret | DELETE requests |
| `list` | List paths | Browse directory structure |
| `sudo` | Admin operations | Vault config changes |
| `deny` | Explicit denial | Override permissions |

### 📖 Practice 6: Multi-Environment Policy

**Scenario:** 3 environments (dev, staging, prod) with different permissions.

**1. Dev team policy:**
```hcl
# dev-team-policy.hcl
path "secret/data/dev/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

path "secret/data/staging/*" {
  capabilities = ["read", "list"]
}

path "secret/data/prod/*" {
  capabilities = ["deny"]
}
```

**2. SRE team policy:**
```hcl
# sre-team-policy.hcl
path "secret/data/prod/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

path "secret/data/staging/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

path "secret/data/dev/*" {
  capabilities = ["read", "list"]
}

# PKI certificate management
path "pki/issue/prod-certs" {
  capabilities = ["create", "update"]
}
```

**3. CI/CD pipeline policy:**
```hcl
# cicd-policy.hcl
path "secret/data/*/database" {
  capabilities = ["read"]
}

path "secret/data/*/api-tokens" {
  capabilities = ["read"]
}

# Cannot delete anything
path "secret/*" {
  capabilities = ["read", "list"]
}
```

**Upload and test policies:**
```bash
# Create policies
vault policy write dev-team dev-team-policy.hcl
vault policy write sre-team sre-team-policy.hcl
vault policy write cicd cicd-policy.hcl

# Create token with policy
vault token create -policy=dev-team -ttl=8h

# Test: try to access prod secrets with dev token
export VAULT_TOKEN="<dev-team-token>"
vault kv get secret/prod/database  # Expected: permission denied
```

---

## 5⃣ Module: Terraform + Vault Integration

### What you will learn:
- Vault provider configuration
- Reading secrets from Terraform
- Dynamic credentials (AWS, Azure)
- State file security

### Terraform Vault Provider Setup

**provider.tf:**
```hcl
terraform {
  required_providers {
    vault = {
      source  = "hashicorp/vault"
      version = "~> 3.20"
    }
  }
}

provider "vault" {
  address = "https://vault.example.com"

  # Auth method 1: Token from environment
  # export VAULT_TOKEN="s.xxxxx"

  # Auth method 2: AppRole
  auth_login {
    path = "auth/approle/login"

    parameters = {
      role_id   = var.approle_role_id
      secret_id = var.approle_secret_id
    }
  }
}
```

### 📖 Practice 7: Reading Secrets from Terraform

**Scenario:** Read database password from Vault and use it to create RDS instance.

**main.tf:**
```hcl
# Read secret from Vault
data "vault_kv_secret_v2" "database" {
  mount = "secret"
  name  = "prod/database"
}

# Use secret in AWS RDS
resource "aws_db_instance" "prod" {
  identifier        = "prod-db"
  engine            = "postgres"
  instance_class    = "db.t3.micro"
  allocated_storage = 20

  #  Secret from Vault (not in git!)
  username = data.vault_kv_secret_v2.database.data["username"]
  password = data.vault_kv_secret_v2.database.data["password"]

  # Other configs...
}

# Output (be careful with sensitive data!)
output "db_endpoint" {
  value = aws_db_instance.prod.endpoint
}

# DON'T output passwords!
# output "db_password" {
#   value = data.vault_kv_secret_v2.database.data["password"]
# }
```

**Terraform state file protection:**
```hcl
# backend.tf - State file in Vault!
terraform {
  backend "http" {
    address        = "https://vault.example.com/v1/secret/data/terraform/state"
    lock_address   = "https://vault.example.com/v1/secret/data/terraform/state/lock"
    unlock_address = "https://vault.example.com/v1/secret/data/terraform/state/lock"
  }
}
```

### 📖 Practice 8: Dynamic AWS Credentials

**Scenario:** Short-lived AWS credentials from Vault for Terraform (no static IAM key).

**1. Vault AWS secrets engine setup:**
```bash
# Enable AWS secrets engine
vault secrets enable aws

# Configure AWS root credentials (Vault uses these to generate dynamic creds)
vault write aws/config/root \
    access_key=AKIAIOSFODNN7EXAMPLE \
    secret_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY \
    region=us-east-1

# Create role for Terraform
vault write aws/roles/terraform \
    credential_type=iam_user \
    policy_document=-<<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*"
    }
  ]
}
EOF
```

**2. Terraform usage:**
```hcl
# Get dynamic AWS credentials from Vault
data "vault_aws_access_credentials" "creds" {
  backend = "aws"
  role    = "terraform"
}

provider "aws" {
  region     = "us-east-1"
  access_key = data.vault_aws_access_credentials.creds.access_key
  secret_key = data.vault_aws_access_credentials.creds.secret_key
}

# After terraform run, these credentials are automatically revoked!
```

---

## 6⃣ Module: Secret Engines Deep Dive

### What you will learn:
- KV v1 vs v2 differences
- Database dynamic credentials
- PKI (Certificate Authority)
- Transit encryption

### KV (Key-Value) Engine

**v1 vs v2:**

| Feature | KV v1 | KV v2 |
|---------|-------|-------|
| Versioning |  No |  Yes |
| Soft delete |  |  |
| Metadata |  |  |
| Path prefix | `secret/mykey` | `secret/data/mykey` |
| CAS (Check-and-Set) |  |  |

**When to use which?**
- **KV v1:** Legacy systems, simple use cases
- **KV v2:** Production (version history, audit trail)

### 📖 Practice 9: Database Dynamic Credentials

**Scenario:** PostgreSQL dynamic user/password generation (60 min TTL).

**1. Database secrets engine setup:**
```bash
# Enable database engine
vault secrets enable database

# Configure PostgreSQL connection
vault write database/config/my-postgres \
    plugin_name=postgresql-database-plugin \
    allowed_roles="readonly,readwrite" \
    connection_url="postgresql://{{username}}:{{password}}@postgres.example.com:5432/mydb?sslmode=require" \
    username="vault-admin" \
    password="vault-admin-password"
```

**2. Create role (SQL permissions):**
```bash
# Read-only role
vault write database/roles/readonly \
    db_name=my-postgres \
    creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'; \
        GRANT SELECT ON ALL TABLES IN SCHEMA public TO \"{{name}}\";" \
    default_ttl="1h" \
    max_ttl="24h"

# Read-write role
vault write database/roles/readwrite \
    db_name=my-postgres \
    creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}'; \
        GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO \"{{name}}\";" \
    default_ttl="1h" \
    max_ttl="24h"
```

**3. Get dynamic credentials:**
```bash
# Get credentials (auto-generated!)
vault read database/creds/readonly

# Output:
# Key                Value
# ---                -----
# lease_id           database/creds/readonly/abc123
# lease_duration     1h
# lease_renewable    true
# password           A1a-xyzABCDEF123456
# username           v-token-readonly-xyzabc123-1234567890
```

**4. Application usage:**
```python
import hvac
import psycopg2

# Vault client
client = hvac.Client(url='https://vault.example.com', token='s.xxxxx')

# Get dynamic DB creds
db_creds = client.read('database/creds/readonly')
username = db_creds['data']['username']
password = db_creds['data']['password']

# Connect to database
conn = psycopg2.connect(
    host="postgres.example.com",
    database="mydb",
    user=username,
    password=password
)

# Use connection...
# After TTL expires (1h), user is automatically deleted from PostgreSQL!
```

**Benefits:**
-  No static password
-  Automatic rotation (new user every 1h)
-  Audit log (who, when requested credentials)
-  Automatic cleanup (user deleted after TTL)

### 📖 Practice 10: PKI (TLS Certificate Generation)

**Scenario:** Generate TLS certificates for internal microservices with Vault PKI.

**1. PKI secrets engine setup:**
```bash
# Enable PKI at custom path
vault secrets enable -path=pki pki

# Set max TTL to 10 years
vault secrets tune -max-lease-ttl=87600h pki

# Generate root CA
vault write pki/root/generate/internal \
    common_name="My Company Internal CA" \
    ttl=87600h

# Configure CA and CRL URLs
vault write pki/config/urls \
    issuing_certificates="https://vault.example.com/v1/pki/ca" \
    crl_distribution_points="https://vault.example.com/v1/pki/crl"
```

**2. Intermediate CA (best practice):**
```bash
# Enable intermediate PKI
vault secrets enable -path=pki_int pki
vault secrets tune -max-lease-ttl=43800h pki_int

# Generate CSR
vault write -format=json pki_int/intermediate/generate/internal \
    common_name="My Company Intermediate CA" \
    | jq -r '.data.csr' > pki_intermediate.csr

# Sign with root CA
vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
    format=pem_bundle ttl=43800h \
    | jq -r '.data.certificate' > intermediate.cert.pem

# Set signed certificate
vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
```

**3. Create role (domain patterns):**
```bash
# Role for *.example.com
vault write pki_int/roles/example-dot-com \
    allowed_domains=example.com \
    allow_subdomains=true \
    max_ttl=720h
```

**4. Generate certificate:**
```bash
# Generate certificate for api.example.com
vault write pki_int/issue/example-dot-com \
    common_name=api.example.com \
    ttl=24h

# Output contains:
# - certificate (PEM)
# - private_key (PEM)
# - issuing_ca (PEM)
# - ca_chain (PEM bundle)
```

**Kubernetes integration (cert-manager):**
```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: vault-issuer
spec:
  vault:
    path: pki_int/sign/example-dot-com
    server: https://vault.example.com
    auth:
      kubernetes:
        role: cert-manager
        mountPath: /v1/auth/kubernetes
        secretRef:
          name: cert-manager-vault-token
          key: token
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: myapp-tls
spec:
  secretName: myapp-tls-secret
  issuerRef:
    name: vault-issuer
  commonName: myapp.example.com
  dnsNames:
  - myapp.example.com
  duration: 720h
  renewBefore: 360h
```

---

## 7⃣ Module: High Availability & Production Setup

### What you will learn:
- HA cluster setup (3+ nodes)
- Auto-unseal (Cloud KMS)
- Backup & restore
- Monitoring & alerting

### HA Architecture

```
                     Load Balancer
                    (HAProxy/nginx)
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    Vault Node 1     Vault Node 2     Vault Node 3
    (Active)         (Standby)        (Standby)
        │                 │                 │
        └─────────────────┼─────────────────┘
                          │
                    Consul Cluster
                    (Storage Backend)
                  or etcd/Raft/etc.
```

**Key Components:**
- **Active Node:** Handles all requests
- **Standby Nodes:** Ready for failover (hot standby)
- **Storage Backend:** Shared encrypted storage (Consul, etcd, Raft)
- **Load Balancer:** Health checks + automatic failover

### 📖 Practice 11: Vault HA Config (Raft Storage)

**vault.hcl (on every node):**
```hcl
storage "raft" {
  path    = "/opt/vault/data"
  node_id = "node1"  # node2, node3 on other nodes

  retry_join {
    leader_api_addr = "https://vault-node1.example.com:8200"
  }

  retry_join {
    leader_api_addr = "https://vault-node2.example.com:8200"
  }

  retry_join {
    leader_api_addr = "https://vault-node3.example.com:8200"
  }
}

listener "tcp" {
  address       = "0.0.0.0:8200"
  tls_cert_file = "/opt/vault/tls/vault.crt"
  tls_key_file  = "/opt/vault/tls/vault.key"
}

api_addr = "https://vault-node1.example.com:8200"
cluster_addr = "https://vault-node1.example.com:8201"

ui = true

# Auto-unseal with AWS KMS (optional)
seal "awskms" {
  region     = "us-east-1"
  kms_key_id = "arn:aws:kms:us-east-1:123456789012:key/abcd1234-..."
}
```

**Cluster setup commands:**
```bash
# Node 1 (leader initialization)
vault operator init -key-shares=5 -key-threshold=3

# Node 2 & 3 join
vault operator raft join https://vault-node1.example.com:8200

# Check cluster status
vault operator raft list-peers
```

### Backup & Restore

**Snapshot backup (Raft backend):**
```bash
# Create snapshot
vault operator raft snapshot save backup.snap

# Automated backup (cron)
0 2 * * * /usr/local/bin/vault operator raft snapshot save /backups/vault-$(date +\%Y\%m\%d).snap

# Restore from snapshot
vault operator raft snapshot restore backup.snap
```

**Secrets export (alternative):**
```bash
# Export all secrets from a path
vault kv list -format=json secret/ | jq -r '.[]' | while read path; do
  vault kv get -format=json "secret/${path}" > "backup/${path}.json"
done
```

### Monitoring Metrics

**Prometheus integration:**
```hcl
# vault.hcl
telemetry {
  prometheus_retention_time = "30s"
  disable_hostname = true
}
```

**Key metrics to monitor:**
```promql
# Vault sealed status (0 = unsealed, 1 = sealed)
vault_core_unsealed

# Request rate
rate(vault_core_handle_request_count[5m])

# Token count
vault_token_count

# Secret lease count
vault_expire_num_leases
```

**Alert examples:**
```yaml
# Prometheus alert rules
groups:
- name: vault
  rules:
  - alert: VaultSealed
    expr: vault_core_unsealed == 0
    for: 5m
    annotations:
      summary: "Vault is sealed on {{ $labels.instance }}"

  - alert: VaultHighLatency
    expr: vault_core_handle_request{quantile="0.99"} > 1000
    for: 10m
    annotations:
      summary: "Vault p99 latency > 1s"
```

---

## 8⃣ Module: Security Best Practices

### What you will learn:
- Least privilege principle
- Token rotation strategies
- Audit logging
- Secret rotation policies

### Security Checklist

** DO:**
- Use AppRole/Kubernetes auth (not root tokens)
- Enable audit logging
- Use short TTLs (1h for tokens, 24h max)
- Rotate secrets regularly
- Use policies (never give wildcard access)
- Enable TLS everywhere
- Auto-unseal in production (Cloud KMS)
- Backup unseal keys offline (encrypted!)
- Monitor Vault metrics
- Regular security updates

** DON'T:**
- Share root token
- Use long-lived tokens (> 24h)
- Store secrets in Git (even encrypted)
- Give `sudo` capability without reason
- Run without TLS
- Skip audit logs
- Use dev mode in production
- Forget to rotate secrets

### 📖 Practice 12: Audit Logging Setup

**1. Enable audit log:**
```bash
# File-based audit log
vault audit enable file file_path=/var/log/vault/audit.log

# Syslog audit log
vault audit enable syslog tag="vault" facility="AUTH"

# Socket audit (for log aggregators)
vault audit enable socket address="logstash.example.com:9090" socket_type="tcp"
```

**2. Audit log content:**
```json
{
  "time": "2024-03-17T10:15:30.123Z",
  "type": "response",
  "auth": {
    "client_token": "hmac-sha256:abcd1234...",
    "accessor": "hmac-sha256:xyz789...",
    "display_name": "approle",
    "policies": ["default", "myapp-policy"],
    "token_type": "service"
  },
  "request": {
    "id": "uuid-request-id",
    "operation": "read",
    "path": "secret/data/prod/database",
    "remote_address": "10.0.1.50"
  },
  "response": {
    "data": {
      "password": "hmac-sha256:hashed-value"  //  HMAC'd, not plaintext!
    }
  }
}
```

**3. Log analysis queries:**
```bash
# Who accessed production secrets today?
jq 'select(.request.path | startswith("secret/data/prod/"))' /var/log/vault/audit.log

# Failed authentication attempts
jq 'select(.error != null and .request.path | startswith("auth/"))' /var/log/vault/audit.log

# High-privilege operations (sudo)
jq 'select(.auth.policies | contains(["root", "admin"]))' /var/log/vault/audit.log
```

### Secret Rotation Strategy

**Policy-based auto-rotation:**
```bash
# Database credentials: rotate every 30 days
vault write database/config/my-postgres \
    password_policy="30-day-rotation"

# Manual rotation trigger
vault write -f database/rotate-root/my-postgres
```

**Application secret rotation workflow:**
```
1. Generate new secret in Vault
2. Update application config (blue-green deployment)
3. Verify new secret works
4. Delete old secret version
5. Audit log verification
```

---

## 9⃣ Module: Troubleshooting & Common Issues

### What you will learn:
- Sealed Vault recovery
- Permission denied errors
- Token expired issues
- Performance tuning

### Common Problems & Solutions

**Problem 1: Vault Sealed**
```bash
# Symptom
$ vault status
Error checking seal status: Error making API request.
URL: GET https://vault.example.com/v1/sys/seal-status
Code: 503. Errors: * Vault is sealed

# Solution: Unseal
vault operator unseal <key-1>
vault operator unseal <key-2>
vault operator unseal <key-3>  # If threshold=3

# Prevention: Auto-unseal with Cloud KMS
```

**Problem 2: Permission Denied**
```bash
# Symptom
Error reading secret/data/prod/database: permission denied

# Debug: Check token capabilities
vault token capabilities secret/data/prod/database

# Debug: Check policy
vault policy read myapp-policy

# Solution: Update policy or use correct token
```

**Problem 3: Token Expired**
```bash
# Symptom
permission denied (token expired)

# Check token TTL
vault token lookup

# Solution 1: Renew token (if renewable)
vault token renew

# Solution 2: Re-authenticate
vault login -method=approle role_id=XXX secret_id=YYY
```

### 📖 Practice 13: Vault Health Check Script

**health-check.sh:**
```bash
#!/bin/bash

VAULT_ADDR="${VAULT_ADDR:-https://vault.example.com}"

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m' # No Color

echo "=== Vault Health Check ==="

# 1. Vault reachable?
if ! curl -sf ${VAULT_ADDR}/v1/sys/health > /dev/null; then
  echo -e "${RED} Vault unreachable${NC}"
  exit 1
fi
echo -e "${GREEN} Vault reachable${NC}"

# 2. Unsealed?
SEALED=$(vault status -format=json | jq -r '.sealed')
if [ "$SEALED" = "true" ]; then
  echo -e "${RED} Vault is SEALED${NC}"
  exit 1
fi
echo -e "${GREEN} Vault unsealed${NC}"

# 3. Token valid?
if ! vault token lookup > /dev/null 2>&1; then
  echo -e "${RED} Token invalid or expired${NC}"
  exit 1
fi
echo -e "${GREEN} Token valid${NC}"

# 4. Can read test secret?
if ! vault kv get secret/health-check > /dev/null 2>&1; then
  echo -e "${RED}⚠  Cannot read test secret${NC}"
else
  echo -e "${GREEN} Secret read OK${NC}"
fi

# 5. Storage backend healthy?
STORAGE=$(vault status -format=json | jq -r '.storage_type')
echo " Storage backend: $STORAGE"

# 6. Cluster info
vault status -format=json | jq '{
  version: .version,
  cluster_name: .cluster_name,
  ha_enabled: .ha_enabled
}'

echo -e "\n${GREEN}=== All checks passed ===${NC}"
```

**Usage:**
```bash
chmod +x health-check.sh
./health-check.sh

# In cron (hourly check)
0 * * * * /opt/scripts/health-check.sh || /usr/local/bin/alert-pagerduty.sh
```

---

## 🔟 Module: Real-World Use Cases (Platform/Company)

### Use Case 1: RHDH ServiceAccount Token Management

**Scenario:** RHDH Platform plugin needs ServiceAccount token for stone-stg-rh01 cluster.

**Current solution (App SRE Vault):**
```bash
# Store token in Vault (App SRE managed)
vault kv put stonesoup/staging/ui/backstage-sa-token \
  token="<k8s-token-from-secret>"
```

**Terraform integration (Emily MR !589 style):**
```hcl
# Kubernetes ServiceAccount token resource
resource "kubernetes_token_request_v1" "backstage_sa" {
  metadata {
    name      = "backstage-konflux-test"
    namespace = "konflux-ui"
  }

  spec {
    # Ephemeral token (auto-rotates)
    expiration_seconds = 3600  # 1 hour
  }
}

# Upload token to Vault
resource "vault_kv_secret_v2" "backstage_token" {
  mount = "stonesoup"
  name  = "staging/ui/backstage-sa-token"

  data_json = jsonencode({
    token = kubernetes_token_request_v1.backstage_sa.token
  })
}
```

**RHDH backend-plugin usage:**
```typescript
// packages/backend/src/plugins/vault.ts
import { VaultClient } from '@backstage/plugin-vault';

const vaultClient = new VaultClient({
  baseUrl: 'https://vault.devshift.net',
  token: process.env.VAULT_TOKEN,
});

// Get K8s token from Vault
const backstageToken = await vaultClient.readSecret(
  'stonesoup/staging/ui/backstage-sa-token'
);

// Use for Kubernetes API calls
const k8sConfig = new KubeConfig();
k8sConfig.loadFromOptions({
  clusters: [{
    server: 'https://api.stone-stg-rh01.ov6h.p1.openshiftapps.com:6443',
    skipTLSVerify: false,
  }],
  users: [{
    token: backstageToken.data.token,
  }],
});
```

### Use Case 2: GitLab CI Secrets Management

**Scenario:** Platform CI/CD pipeline secrets (Quay.io, GHCR, AWS) from Vault.

**.gitlab-ci.yml:**
```yaml
variables:
  VAULT_ADDR: "https://vault.devshift.net"

before_script:
  # Login to Vault with AppRole
  - export VAULT_TOKEN=$(vault write -field=token auth/approle/login
      role_id=${CI_APPROLE_ROLE_ID}
      secret_id=${CI_APPROLE_SECRET_ID})

build:
  stage: build
  script:
    # Get Quay.io credentials from Vault
    - export QUAY_USER=$(vault kv get -field=username secret/konflux/quay)
    - export QUAY_PASSWORD=$(vault kv get -field=password secret/konflux/quay)

    # Docker build & push
    - docker login -u $QUAY_USER -p $QUAY_PASSWORD quay.io
    - docker build -t quay.io/konflux/myapp:${CI_COMMIT_SHA} .
    - docker push quay.io/konflux/myapp:${CI_COMMIT_SHA}

deploy_staging:
  stage: deploy
  script:
    # Get K8s token from Vault
    - export K8S_TOKEN=$(vault kv get -field=token secret/konflux/staging/k8s)

    # Deploy to staging cluster
    - kubectl --token=$K8S_TOKEN apply -f manifests/
  only:
    - main
```

**Vault secret structure:**
```
secret/
├── konflux/
│   ├── quay
│   │   ├── username: "konflux+robot"
│   │   └── password: "XXX"
│   ├── staging/
│   │   └── k8s
│   │       └── token: "YYY"
│   └── prod/
│       └── k8s
│           └── token: "ZZZ"
```

### Use Case 3: Kubernetes Pod Auto-Secret Injection

**Scenario:** LUMINO MCP Server pod needs Prometheus/AlertManager credentials.

**Vault setup:**
```bash
# Enable K8s auth
vault auth enable kubernetes
vault write auth/kubernetes/config \
  kubernetes_host="https://kubernetes.default.svc:443"

# Create policy for LUMINO
vault policy write lumino-policy - <<EOF
path "secret/data/lumino/*" {
  capabilities = ["read"]
}
EOF

# Create K8s role
vault write auth/kubernetes/role/lumino \
  bound_service_account_names=lumino-sa \
  bound_service_account_namespaces=monitoring \
  policies=lumino-policy \
  ttl=1h
```

**Kubernetes deployment:**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: lumino-sa
  namespace: monitoring
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lumino-mcp-server
  namespace: monitoring
spec:
  template:
    metadata:
      annotations:
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/role: "lumino"
        vault.hashicorp.com/agent-inject-secret-prometheus: "secret/data/lumino/prometheus"
        vault.hashicorp.com/agent-inject-template-prometheus: |
          {{- with secret "secret/data/lumino/prometheus" -}}
          export PROMETHEUS_URL="{{ .Data.data.url }}"
          export PROMETHEUS_TOKEN="{{ .Data.data.token }}"
          {{- end }}
    spec:
      serviceAccountName: lumino-sa
      containers:
      - name: lumino
        image: quay.io/geored/lumino-mcp-server:latest
        command:
        - /bin/sh
        - -c
        - |
          source /vault/secrets/prometheus
          uv run python main.py
```

**Auto-rotation:** Token expires → Vault Agent auto-renews → Pod gets fresh secret (zero downtime!)

---

##  Summary

### What you learned:

1.  **Vault basics** - Sealed/Unsealed, Secret Engines, Paths
2.  **CLI usage** - Dev mode, secrets CRUD, versioning
3.  **Authentication** - Token, AppRole, Kubernetes SA
4.  **Policies** - HCL syntax, capabilities, multi-env
5.  **Terraform integration** - Provider, dynamic credentials
6.  **Secret Engines** - KV, Database, PKI, Transit
7.  **Production HA** - Cluster, auto-unseal, backups
8.  **Security** - Best practices, audit logging, rotation
9.  **Troubleshooting** - Common issues, health checks
10.  **Real-world** - Platform/Company use cases

### Next Steps

**Beginner level (1-2 weeks):**
- [ ] Vault CLI installation
- [ ] Dev mode practices (modules 1-3)
- [ ] First secret management (read/write/delete)

**Advanced level (1 month):**
- [ ] AppRole setup for CI/CD
- [ ] Try Terraform integration
- [ ] Write policies for multi-env

**Master level (3 months):**
- [ ] Production HA cluster setup
- [ ] Implement dynamic database credentials
- [ ] PKI certificate automation
- [ ] Setup monitoring & alerting

### Further Learning

**Official documentation:**
- https://developer.hashicorp.com/vault/docs
- https://developer.hashicorp.com/vault/tutorials

**Hands-on Lab:**
- https://www.katacoda.com/hashicorp/scenarios/vault-intro
- https://play.instruqt.com/hashicorp (interactive Vault tutorials)

**Company specific:**
- App SRE Vault documentation (internal)
- ServiceNow KB articles (search: "vault")

---

**Questions?** Start with the practices and ask anything! 
