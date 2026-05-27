# Kerberos Authentication - Quick Reference

## What is Kerberos?

**Kerberos** is a network authentication protocol that enables secure identification over untrusted networks (e.g., the Internet). Developed by MIT, it's an encrypted, ticket-based system.

**Key Features:**
- **Single Sign-On (SSO):** Log in once, access multiple services
- **Mutual Authentication:** Both client and server verify each other's identity
- **Ticket-based:** No repeated password transmission over the network
- **Time-sensitive:** Tickets expire (default: 10-24 hours)

## Kerberos Architecture

### Main Components

```
+-------------------+
|  KDC       | Key Distribution Center
| (Kerberos Server)|
|          |
| +-------------+ |
| | AS     | | Authentication Service
| +-------------+ |
| | TGS     | | Ticket Granting Service
| +-------------+ |
+-------------------+
|
| TGT, Service Tickets
|
+--------+       +------------------+
| Client | <--------> | Service (SSH,  |
|    |  Ticket   | HTTP, NFS, etc.)|
+--------+       +------------------+
```

### Key Entities

1. **Principal:** Unique identifier (e.g., `user@EXAMPLE.COM`)
2. **Realm:** Kerberos domain (e.g., `EXAMPLE.COM`)
3. **KDC (Key Distribution Center):** Central authentication server
4. **TGT (Ticket Granting Ticket):** Entry ticket needed for further tickets
5. **Service Ticket:** Access ticket for a specific service

## Kerberos Authentication Flow

### 1. Initial Authentication (AS_REQ/AS_REP)

```
Client --> AS: "Request TGT for user@EXAMPLE.COM"
AS --> Client: TGT (encrypted with user's password)
```

**Command:** `kinit user@EXAMPLE.COM`

### 2. Service Ticket Request (TGS_REQ/TGS_REP)

```
Client --> TGS: "Request ticket for SSH service" (with TGT)
TGS --> Client: Service Ticket (for SSH)
```

**Automatic:** Happens in the background when accessing the service

### 3. Service Access (AP_REQ/AP_REP)

```
Client --> SSH Server: Present Service Ticket
SSH Server --> Client: Access granted
```

## Essential Kerberos Commands

### 1. Obtain TGT (Login)

```bash
# Use default realm
kinit

# Specific principal
kinit user@EXAMPLE.COM

# Specify ticket lifetime (8 hours)
kinit -l 8h user@EXAMPLE.COM

# Renewable ticket (7-day renewal period)
kinit -r 7d user@EXAMPLE.COM

# Use keytab file (password-less authentication)
kinit -k -t /etc/krb5.keytab host/server.example.com@EXAMPLE.COM
```

### 2. List Tickets

```bash
# Display all tickets
klist

# Verbose output (with encryption types)
klist -e

# Specify ticket cache file
klist -c /tmp/krb5cc_1000
```

**Example output:**
```
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: user@EXAMPLE.COM

Valid starting    Expires       Service principal
05/06/2026 08:30:00 05/06/2026 18:30:00 krbtgt/EXAMPLE.COM@EXAMPLE.COM
05/06/2026 09:15:00 05/06/2026 18:30:00 host/bastion.example.com@EXAMPLE.COM
```

### 3. Renew TGT

```bash
# Renew ticket (if renewable)
kinit -R

# Automatic renewal in scripts
kinit -R || kinit user@EXAMPLE.COM
```

### 4. Delete Tickets (Logout)

```bash
# Delete all tickets
kdestroy

# Delete specific cache
kdestroy -c /tmp/krb5cc_1000
```

### 5. Change Password

```bash
# Change Kerberos password
kpasswd

# Change password for specific principal
kpasswd user@EXAMPLE.COM
```

## Kerberos Configuration

### `/etc/krb5.conf` - Main Configuration File

```ini
[libdefaults]
default_realm = EXAMPLE.COM
dns_lookup_realm = false
dns_lookup_kdc = true
ticket_lifetime = 24h
renew_lifetime = 7d
forwardable = true
# Encryption algorithms
default_tkt_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96
default_tgs_enctypes = aes256-cts-hmac-sha1-96 aes128-cts-hmac-sha1-96

[realms]
EXAMPLE.COM = {
kdc = kerberos.example.com:88
admin_server = kerberos.example.com:749
default_domain = example.com
}

[domain_realm]
.example.com = EXAMPLE.COM
example.com = EXAMPLE.COM
```

### Kerberos Cache Locations

```bash
# Default cache
/tmp/krb5cc_$(id -u)

# Environment variable for custom cache
export KRB5CCNAME=/tmp/krb5cc_custom

# Keyring-based cache (more secure)
export KRB5CCNAME=KEYRING:persistent:$(id -u)
```

## the company/Fedora Specific Usage

### Installing Kerberos

```bash
# Fedora/RHEL
sudo dnf install krb5-workstation krb5-libs pam_krb5

# Kerberos server (only for KDC)
sudo dnf install krb5-server krb5-libs
```

### Kerberos Integration with SSH

**SSH client configuration (`~/.ssh/config`):**
```
Host *.example.com
GSSAPIAuthentication yes
GSSAPIDelegateCredentials yes
```

**SSH server configuration (`/etc/ssh/sshd_config`):**
```
GSSAPIAuthentication yes
GSSAPICleanupCredentials yes
UsePAM yes
```

### Kerberos + LDAP Integration

```bash
# SSSD configuration with Kerberos authentication
sudo authselect select sssd with-mkhomedir

# /etc/sssd/sssd.conf
[domain/example.com]
id_provider = ldap
auth_provider = krb5
krb5_realm = EXAMPLE.COM
```

## Common Use Cases

### 1. SSH Kerberos Authentication

```bash
# Obtain TGT
kinit user@EXAMPLE.COM

# SSH connection (password-less)
ssh -K server.example.com

# Ticket forwarding (delegation)
ssh -K -o GSSAPIDelegateCredentials=yes server.example.com
```

### 2. NFS Kerberos Mount

```bash
# NFSv4 Kerberos mount
sudo mount -t nfs4 -o sec=krb5 server.example.com:/export /mnt/nfs

# /etc/fstab entry
server.example.com:/export /mnt/nfs nfs4 sec=krb5,_netdev 0 0
```

### 3. HTTP Kerberos Authentication (curl)

```bash
# Use Kerberos ticket for HTTP request
curl --negotiate -u : https://web.example.com/api

# Service ticket automatically requested
```

### 4. Ansible Kerberos Authentication

```bash
# Inventory configuration
[all:vars]
ansible_connection=ssh
ansible_ssh_common_args='-o GSSAPIAuthentication=yes'

# Run playbook
kinit user@EXAMPLE.COM
ansible-playbook -i inventory.yml playbook.yml
```

## Troubleshooting

### 1. Ticket Verification

```bash
# List tickets
klist -e

# Common issues:
# - "No credentials cache found" → kinit required
# - "Ticket expired" → kinit -R or re-run kinit
# - No service ticket → Service Kerberos configuration issue
```

### 2. Debug Mode

```bash
# SSH debug with Kerberos
ssh -vvv -K server.example.com 2>&1 | grep -i gssapi

# Kerberos library debug
export KRB5_TRACE=/dev/stderr
kinit user@EXAMPLE.COM
```

### 3. Time Synchronization Issues

Kerberos is very sensitive to clock skew (max 5-minute tolerance).

```bash
# Check NTP status
timedatectl status

# Force time synchronization
sudo chronyc -a makestep
```

### 4. DNS Issues

```bash
# Test KDC DNS lookup
nslookup -type=SRV _kerberos._tcp.example.com

# Reverse DNS check (important!)
nslookup $(hostname -f)
```

### 5. Error Messages and Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| `Clock skew too great` | Time difference > 5 min | NTP sync (`chronyc`) |
| `Server not found in Kerberos database` | Missing SPN | Create service principal on KDC |
| `Cannot contact any KDC` | KDC unreachable | Check network/firewall, `krb5.conf` |
| `Preauthentication failed` | Wrong password | Verify password |
| `Ticket expired` | TGT expired | Re-run `kinit` |

## Security

### Best Practices

1. **Ticket Lifetime:** Limited (max 24h), renewable up to 7 days
2. **Ticket Forwarding:** Only to trusted servers (`GSSAPIDelegateCredentials yes`)
3. **Keytab Protection:** `chmod 600 /etc/krb5.keytab`, only root can read
4. **AES Encryption:** Avoid DES/RC4, use AES256
5. **Replay Attack Protection:** Time-based replay detection (5-minute tolerance)

### Keytab Files

Keytab = password-less authentication for service accounts

```bash
# Create keytab (on KDC)
kadmin.local -q "ktadd -k /etc/krb5.keytab host/server.example.com"

# List keytab contents
klist -k /etc/krb5.keytab

# Use keytab
kinit -k -t /etc/krb5.keytab host/server.example.com
```

## Kerberos + Kubernetes/OpenShift

### Service Account Kerberos Authentication

```yaml
# Secret with keytab
apiVersion: v1
kind: Secret
metadata:
name: kerberos-keytab
type: Opaque
data:
krb5.keytab: <base64-encoded-keytab>
---
# Pod configuration
apiVersion: v1
kind: Pod
metadata:
name: kerberos-client
spec:
containers:
- name: app
image: registry.access.company.internal/ubi9
volumeMounts:
- name: keytab
mountPath: /etc/krb5.keytab
subPath: krb5.keytab
readOnly: true
env:
- name: KRB5_CLIENT_KTNAME
value: /etc/krb5.keytab
volumes:
- name: keytab
secret:
secretName: kerberos-keytab
defaultMode: 0600
```

## Command Summary

| Command | Description |
|---------|-------------|
| `kinit` | Obtain TGT (login) |
| `kinit -R` | Renew TGT |
| `klist` | List tickets |
| `klist -e` | List tickets + encryption types |
| `kdestroy` | Delete all tickets |
| `kpasswd` | Change password |
| `kvno` | Get service principal version |
| `kadmin` | KDC administration (privileged) |

## References

- **MIT Kerberos Documentation:** https://web.mit.edu/kerberos/
- **the company Kerberos Guide:** https://access.company.internal/documentation/en-us/red_hat_enterprise_linux/9/html/configuring_authentication_and_authorization_in_rhel/configuring-a-kerberos-5-server_configuring-authentication-and-authorization-in-rhel
- **RFC 4120:** Kerberos V5 protocol specification
- **Fedora Kerberos:** https://docs.fedoraproject.org/en-US/quick-docs/kerberos/

---

**Last Updated:** 2026-05-06 
**Author:** Generated for GitHub Portfolio 
**Version:** 1.0
