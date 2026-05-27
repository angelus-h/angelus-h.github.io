---
description: OpenSSL quick reference - SSL/TLS certificates, key generation, encryption, hashing, digital signatures, and network connection testing.
---

# OpenSSL Quick Reference

## Overview

**OpenSSL** is an open-source cryptographic library and toolkit providing SSL/TLS protocol implementations, certificate management, encryption/decryption, and hash functions.

**Main Use Cases:**
- SSL/TLS certificate generation and management
- Private/public key creation
- File encryption/decryption
- Hashing and digital signatures
- Testing network connections with SSL/TLS

---

## 1. Certificate and Key Generation

### Generate Private Key (RSA)

```bash
# RSA 2048 bit key (standard)
openssl genrsa -out private.key 2048

# RSA 4096 bit key (stronger)
openssl genrsa -out private.key 4096

# RSA key protected with password (AES-256)
openssl genrsa -aes256 -out private.key 2048
```

### Extract Public Key from Private Key

```bash
openssl rsa -in private.key -pubout -out public.key
```

### Generate Self-Signed Certificate

```bash
# One step: private key + self-signed cert (1 year validity)
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes

# Interactive (prompts for data: Country, State, Organization, etc.)
# -nodes: saves private key without password
```

### Generate Certificate Signing Request (CSR)

```bash
# Generate CSR from existing private key
openssl req -new -key private.key -out request.csr

# Generate CSR and new private key simultaneously
openssl req -new -newkey rsa:2048 -nodes -keyout private.key -out request.csr
```

**CSR Required Fields:**
- **Country Name (C):** US
- **State (ST):** California
- **Organization (O):** Example Inc
- **Common Name (CN):** *.example.com or example.com (domain name!)
- **Email Address:** admin@example.com

---

## 2. Viewing and Validating Certificates

### View Certificate Contents

```bash
# X.509 cert details
openssl x509 -in cert.pem -text -noout

# Check expiration date
openssl x509 -in cert.pem -noout -dates

# View Subject and Issuer
openssl x509 -in cert.pem -noout -subject -issuer

# Certificate fingerprint (SHA256)
openssl x509 -in cert.pem -noout -fingerprint -sha256
```

### Check CSR Contents

```bash
openssl req -in request.csr -text -noout
```

### Compare Private Key and Certificate

```bash
# Private key modulus
openssl rsa -in private.key -noout -modulus | openssl md5

# Certificate modulus
openssl x509 -in cert.pem -noout -modulus | openssl md5

# If MD5 hashes match -> key and cert belong together
```

### Fetch Remote SSL Certificate

```bash
# Retrieve HTTPS server certificate
openssl s_client -connect example.com:443 -showcerts < /dev/null

# Only the certificate chain
openssl s_client -connect example.com:443 -showcerts 2>/dev/null | openssl x509 -text

# StartTLS for SMTP
openssl s_client -connect smtp.example.com:587 -starttls smtp
```

---

## 3. File Encryption and Decryption

### Symmetric Encryption (AES-256-CBC)

```bash
# Encrypt file (prompts for password)
openssl enc -aes-256-cbc -salt -in file.txt -out file.txt.enc

# Decrypt
openssl enc -aes-256-cbc -d -in file.txt.enc -out file.txt

# Encrypt with base64 output (email-friendly)
openssl enc -aes-256-cbc -salt -a -in file.txt -out file.txt.enc

# Decrypt from base64
openssl enc -aes-256-cbc -d -a -in file.txt.enc -out file.txt
```

### Asymmetric Encryption (RSA Keys)

```bash
# Encrypt with public key
openssl rsautl -encrypt -pubin -inkey public.key -in file.txt -out file.enc

# Decrypt with private key
openssl rsautl -decrypt -inkey private.key -in file.enc -out file.txt
```

**Limitation:** RSA can only encrypt small files (max key size - 11 bytes). For larger files, use hybrid approach (AES + RSA).

---

## 4. Hash Functions and Digital Signatures

### Calculate Hash

```bash
# MD5 (weak, don't use in production!)
openssl md5 file.txt

# SHA-256 (recommended)
openssl sha256 file.txt

# SHA-512 (stronger)
openssl sha512 file.txt

# Hash in base64 format
openssl dgst -sha256 -binary file.txt | openssl base64
```

### Digital Signatures

```bash
# Sign file with private key
openssl dgst -sha256 -sign private.key -out file.sig file.txt

# Verify signature with public key
openssl dgst -sha256 -verify public.key -signature file.sig file.txt
```

---

## 5. Format Conversions

### PEM ↔ DER Conversion

```bash
# PEM -> DER (binary format)
openssl x509 -in cert.pem -outform DER -out cert.der

# DER -> PEM (text format)
openssl x509 -in cert.der -inform DER -out cert.pem
```

### PEM ↔ PKCS#12 (.pfx, .p12) Conversion

```bash
# PEM -> PKCS#12 (key + cert in one file, Windows-friendly)
openssl pkcs12 -export -out cert.pfx -inkey private.key -in cert.pem -certfile ca-cert.pem

# PKCS#12 -> PEM (extract)
openssl pkcs12 -in cert.pfx -out cert.pem -nodes
```

### PKCS#7 Handling

```bash
# PEM -> PKCS#7
openssl crl2pkcs7 -nocrl -certfile cert.pem -out cert.p7b

# PKCS#7 -> PEM
openssl pkcs7 -print_certs -in cert.p7b -out cert.pem
```

---

## 6. SSL/TLS Testing and Debugging

### Test HTTPS Connection

```bash
# Basic SSL handshake test
openssl s_client -connect example.com:443

# Force TLS 1.2
openssl s_client -connect example.com:443 -tls1_2

# Specify SNI (Server Name Indication)
openssl s_client -connect 192.168.1.10:443 -servername example.com

# Test cipher suite
openssl s_client -connect example.com:443 -cipher 'ECDHE-RSA-AES256-GCM-SHA384'
```

### Validate Certificate Chain

```bash
# Verify cert chain against CA
openssl verify -CAfile ca-cert.pem cert.pem

# Validate full chain
openssl verify -CAfile ca-bundle.pem -untrusted intermediate.pem cert.pem
```

### OCSP (Online Certificate Status Protocol) Test

```bash
# Fetch OCSP response
openssl ocsp -issuer ca-cert.pem -cert cert.pem -url http://ocsp.example.com -resp_text
```

---

## 7. Common Errors and Solutions

### "unable to load Private Key" Error

**Cause:** Wrong format or password-protected key.

**Solution:**
```bash
# Remove password from private key
openssl rsa -in encrypted-private.key -out private.key

# Check format
openssl rsa -in private.key -text -noout
```

### "certificate verify failed" Errors

**Cause:** Missing CA certificate or certificate chain issue.

**Solution:**
```bash
# Use CA bundle
openssl s_client -connect example.com:443 -CAfile /etc/ssl/certs/ca-bundle.crt

# Use system CAs (Red Hat/Fedora)
openssl s_client -connect example.com:443 -CApath /etc/pki/tls/certs/
```

### Accept Self-Signed Certificate for Testing

```bash
# Skip validation (TESTING ONLY!)
curl -k https://example.com
# or
openssl s_client -connect example.com:443 -verify_return_error
```

---

## 8. Useful One-Liners

```bash
# Certificate expiration date in human-readable format
openssl x509 -in cert.pem -noout -enddate | cut -d= -f2

# Generate random password (32 characters, base64)
openssl rand -base64 32

# Generate random hex string (16 bytes)
openssl rand -hex 16

# File SHA-256 checksum (compatible with sha256sum)
openssl dgst -sha256 file.txt | awk '{print $2}'

# List certificate Subject Alternative Names (SAN)
openssl x509 -in cert.pem -noout -ext subjectAltName

# Check private key bit length
openssl rsa -in private.key -text -noout | grep "Private-Key"

# List all available cipher suites
openssl ciphers -v

# TLS 1.3 ciphers
openssl ciphers -v -tls1_3
```

---

## 9. Automation and Scripting

### Non-Interactive CSR Generation

```bash
# Specify subject from command line
openssl req -new -key private.key -out request.csr -subj "/C=US/ST=California/L=San Francisco/O=Example Inc/OU=IT/CN=example.com"

# Use config file
openssl req -new -key private.key -out request.csr -config openssl.cnf
```

**openssl.cnf Example:**
```ini
[ req ]
default_bits = 2048
distinguished_name = req_distinguished_name
req_extensions = req_ext

[ req_distinguished_name ]
countryName = Country Name (2 letter code)
countryName_default = US
stateOrProvinceName = State or Province Name
stateOrProvinceName_default = California
commonName = Common Name (FQDN)
commonName_default = example.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = example.com
DNS.2 = www.example.com
DNS.3 = *.example.com
```

### Batch Encryption/Decryption

```bash
# Encrypt all .txt files in a directory
for file in *.txt; do
openssl enc -aes-256-cbc -salt -in "$file" -out "$file.enc" -pass pass:MySecretPassword
done

# Decrypt
for file in *.txt.enc; do
openssl enc -aes-256-cbc -d -in "$file" -out "${file%.enc}" -pass pass:MySecretPassword
done
```

---

## 10. Kubernetes/OpenShift Context

### Create TLS Secret

```bash
# 1. Generate certificate and key
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout tls.key -out tls.crt \
-subj "/CN=myapp.example.com/O=myapp"

# 2. Create Kubernetes TLS secret
kubectl create secret tls myapp-tls --cert=tls.crt --key=tls.key

# 3. Use in Ingress/Route
# OpenShift Route YAML:
# spec:
# tls:
# termination: edge
# key: <tls.key content>
# certificate: <tls.crt content>
```

### Verify Service Mesh (Istio) Certificates

```bash
# Retrieve Istio sidecar certificate
kubectl exec -it <pod-name> -c istio-proxy -- openssl s_client -connect myservice:8080 -showcerts

# Test HTTPS connection from pod
kubectl exec -it <pod-name> -- openssl s_client -connect external-service.com:443
```

---

## References

**Official Documentation:**
- https://www.openssl.org/docs/
- `man openssl` - OpenSSL man pages

**Useful Links:**
- OpenSSL Cookbook (SSL/TLS): https://www.feistyduck.com/library/openssl-cookbook/
- Mozilla SSL Configuration Generator: https://ssl-config.mozilla.org/

**Red Hat Specific:**
- RHEL Crypto Policy: `/etc/crypto-policies/`
- System-wide CA certificates: `/etc/pki/tls/certs/`

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** SRE, DevOps, Security Engineering
