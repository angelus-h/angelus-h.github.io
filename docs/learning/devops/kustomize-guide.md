# Kustomize Guide and Quick Reference

**Version:** 1.0
**Last Updated:** 2026-03-20
**Author:** Learning Plans Collection

---

## Table of Contents

1. [Introduction](#introduction)
2. [Installation](#installation)
3. [Core Concepts](#core-concepts)
4. [Basic Usage](#basic-usage)
5. [Kustomization File Reference](#kustomization-file-reference)
6. [Common Patterns](#common-patterns)
7. [Advanced Features](#advanced-features)
8. [Quick Reference](#quick-reference)
9. [Practical Examples](#practical-examples)
10. [Best Practices](#best-practices)
11. [Troubleshooting](#troubleshooting)

---

## Introduction

**Kustomize** is a template-free Kubernetes configuration customization tool. It introduces a declarative approach to configuration management using plain YAML files and overlays, without requiring templates or a custom DSL.

### What is Kustomize?

Kustomize lets you customize raw, template-free YAML files for multiple purposes, leaving the original YAML untouched and usable as-is.

### Why Kustomize?

- **No Templates** - Work with plain Kubernetes YAML
- **Declarative** - Describe what you want, not how to get there
- **Composable** - Build complex configurations from simple pieces
- **GitOps Friendly** - Plain YAML in version control
- **Built into oc/kubectl** - No extra tools needed (OpenShift 4.x / kubectl 1.14+)
- **Overlay Pattern** - Base + patches for different environments

### Kustomize vs Helm

| Feature | Kustomize | Helm |
|---------|-----------|------|
| Templating | No (YAML patches) | Yes (Go templates) |
| Package Manager | No | Yes |
| Learning Curve | Low | Medium |
| Built into oc/kubectl | Yes | No (separate CLI) |
| Use Case | Configuration management | App packaging + deployment |

---

## Installation

### Built-in oc/kubectl (Recommended)

Kustomize is built into OpenShift CLI (oc) and kubectl 1.14+:

```bash
# OpenShift CLI
oc version
# or kubectl
kubectl version --client

# Use with -k flag
oc apply -k ./
oc kustomize ./
```

### Standalone Kustomize CLI

For latest features or if using older kubectl:

```bash
# Linux
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh" | bash
sudo mv kustomize /usr/local/bin/

# macOS (Homebrew)
brew install kustomize

# Windows (Chocolatey)
choco install kustomize

# Go install
go install sigs.k8s.io/kustomize/kustomize/v5@latest
```

### Verify Installation

```bash
# Standalone CLI
kustomize version

# oc/kubectl built-in
oc kustomize --help
# or
oc kustomize --help
```

### Version Differences

- **oc/oc kustomize** - May lag behind standalone (e.g., OpenShift 4.14 / kubectl 1.28 = Kustomize v5.0.1)
- **Standalone kustomize** - Latest features, faster updates

---

## Core Concepts

### 1. Base

The **base** is a directory with a `kustomization.yaml` file containing a set of resources and common customizations.

```
base/
├── kustomization.yaml
├── deployment.yaml
└── service.yaml
```

### 2. Overlay

An **overlay** is a directory with a `kustomization.yaml` that references a base and adds/modifies resources.

```
overlays/
├── development/
│   └── kustomization.yaml
├── staging/
│   └── kustomization.yaml
└── production/
    └── kustomization.yaml
```

### 3. Kustomization File

`kustomization.yaml` - The config file that defines:
- Resources to include
- Patches to apply
- Generators (ConfigMaps, Secrets)
- Transformers (labels, annotations, namespaces)

### 4. Resources

Kubernetes YAML files referenced in `kustomization.yaml`:

```yaml
resources:
  - deployment.yaml
  - service.yaml
  - https://github.com/user/repo//base?ref=v1.0.0
```

### 5. Patches

Modifications to resources:
- **Strategic Merge Patch** - YAML patches (default)
- **JSON Patch (RFC 6902)** - Precise modifications
- **JSON 6902 Patch** - Target specific resources

### 6. Generators

Create ConfigMaps and Secrets from files or literals:

```yaml
configMapGenerator:
  - name: app-config
    files:
      - config.properties
    literals:
      - LOG_LEVEL=info
```

### 7. Transformers

Apply common modifications:
- `namePrefix` / `nameSuffix`
- `commonLabels` / `commonAnnotations`
- `namespace`
- `replicas`
- `images` (image tag updates)

---

## Basic Usage

### Directory Structure

```
my-app/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    ├── dev/
    │   └── kustomization.yaml
    ├── staging/
    │   └── kustomization.yaml
    └── prod/
        └── kustomization.yaml
```

### Base Example

**base/deployment.yaml:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:1.0.0
        ports:
        - containerPort: 8080
```

**base/service.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
```

**base/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml

commonLabels:
  app: myapp
  team: backend
```

### Overlay Example

**overlays/dev/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namePrefix: dev-
namespace: development

replicas:
  - name: myapp
    count: 1

images:
  - name: myapp
    newTag: 1.1.0-dev

commonLabels:
  environment: dev
```

**overlays/prod/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namePrefix: prod-
namespace: production

replicas:
  - name: myapp
    count: 3

images:
  - name: myapp
    newTag: 1.0.0

commonLabels:
  environment: prod

resources:
  - hpa.yaml  # Production-only Horizontal Pod Autoscaler
```

### Build and Apply

```bash
# Build (preview YAML output)
oc kustomize overlays/dev

# Or with standalone CLI
kustomize build overlays/dev

# Apply directly
oc apply -k overlays/dev

# Apply to production
oc apply -k overlays/prod

# Diff before applying (kubectl 1.24+)
oc diff -k overlays/dev
```

---

## Kustomization File Reference

### Complete kustomization.yaml Schema

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# Include other kustomization directories
bases:
  - ../base
  - github.com/user/repo//base?ref=v1.0.0

# Include resource YAML files
resources:
  - deployment.yaml
  - service.yaml
  - https://raw.githubusercontent.com/user/repo/main/resource.yaml

# Add prefix to all resource names
namePrefix: dev-

# Add suffix to all resource names
nameSuffix: -v2

# Set namespace for all resources
namespace: my-namespace

# Add labels to all resources
commonLabels:
  app: myapp
  team: platform

# Add annotations to all resources
commonAnnotations:
  managed-by: kustomize
  version: "1.0"

# Update container images
images:
  - name: myapp
    newName: registry.io/myapp
    newTag: v2.0.0
  - name: nginx
    digest: sha256:abc123...

# Set replica counts
replicas:
  - name: myapp-deployment
    count: 3

# Generate ConfigMaps
configMapGenerator:
  - name: app-config
    files:
      - config.properties
      - settings.json
    literals:
      - LOG_LEVEL=info
      - DEBUG=false
    envs:
      - .env

# Generate Secrets
secretGenerator:
  - name: app-secrets
    files:
      - secrets.txt
    literals:
      - DB_PASSWORD=secret123
    envs:
      - .env.secret
    type: Opaque

# Strategic Merge Patches
patchesStrategicMerge:
  - patch-deployment.yaml
  - patch-service.yaml

# JSON 6902 Patches
patchesJson6902:
  - target:
      group: apps
      version: v1
      kind: Deployment
      name: myapp
    path: patch.yaml

# Inline patches
patches:
  - target:
      kind: Deployment
      name: myapp
    patch: |-
      - op: replace
        path: /spec/replicas
        value: 3

# Replace resources
replacements:
  - source:
      kind: ConfigMap
      name: config
      fieldPath: data.version
    targets:
      - select:
          kind: Deployment
        fieldPaths:
          - spec.template.metadata.labels.version

# OpenAPI schema location (for validation)
openapi:
  path: https://k8s.io/api/openapi-spec/swagger.json

# CRD locations
crds:
  - https://raw.githubusercontent.com/cert-manager/cert-manager/v1.7.1/deploy/crds/crd-certificaterequests.yaml

# Build metadata
buildMetadata:
  - managedByLabel
  - originAnnotations

# Generator options
generatorOptions:
  disableNameSuffixHash: false
  labels:
    generator: kustomize
  annotations:
    generated-by: kustomize

# Vars (deprecated, use replacements)
# vars:
#   - name: SERVICE_NAME
#     objref:
#       kind: Service
#       name: myapp
#       apiVersion: v1
#     fieldref:
#       fieldpath: metadata.name
```

---

## Common Patterns

### 1. Environment-Specific Overlays

```
app/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml
    │   └── dev-config.yaml
    ├── staging/
    │   ├── kustomization.yaml
    │   └── staging-config.yaml
    └── prod/
        ├── kustomization.yaml
        ├── prod-config.yaml
        └── hpa.yaml
```

### 2. ConfigMap from Files

**base/kustomization.yaml:**

```yaml
configMapGenerator:
  - name: nginx-config
    files:
      - configs/nginx.conf

generatorOptions:
  disableNameSuffixHash: true  # Keep consistent name
```

### 3. Secret from Literals

**overlays/prod/kustomization.yaml:**

```yaml
secretGenerator:
  - name: db-credentials
    literals:
      - username=admin
      - password=super-secret
    type: Opaque
```

### 4. Image Tag Update

```yaml
images:
  - name: myapp
    newTag: v2.1.0
  - name: sidecar
    newName: myregistry.io/sidecar
    newTag: latest
```

### 5. Namespace Per Environment

**overlays/dev/kustomization.yaml:**

```yaml
namespace: dev-team-a

resources:
  - namespace.yaml

# namespace.yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: dev-team-a
```

### 6. Strategic Merge Patch

**overlays/prod/increase-replicas.yaml:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 5
  template:
    spec:
      containers:
      - name: myapp
        resources:
          limits:
            cpu: 1000m
            memory: 2Gi
```

**overlays/prod/kustomization.yaml:**

```yaml
patchesStrategicMerge:
  - increase-replicas.yaml
```

### 7. JSON Patch (Precise Changes)

**overlays/prod/json-patch.yaml:**

```yaml
- op: replace
  path: /spec/replicas
  value: 10

- op: add
  path: /spec/template/spec/containers/0/env/-
  value:
    name: NEW_VAR
    value: "production"

- op: remove
  path: /spec/template/spec/containers/0/env/2
```

**overlays/prod/kustomization.yaml:**

```yaml
patchesJson6902:
  - target:
      group: apps
      version: v1
      kind: Deployment
      name: myapp
    path: json-patch.yaml
```

### 8. Remote Bases

```yaml
bases:
  - github.com/kubernetes-sigs/kustomize//examples/multibases/base?ref=v4.5.7
  - https://github.com/argoproj/argo-cd//manifests/cluster-install?ref=v2.5.0
```

### 9. Multiple Patches

```yaml
patches:
  - target:
      kind: Deployment
    patch: |-
      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: not-used
      spec:
        template:
          spec:
            securityContext:
              runAsNonRoot: true
              runAsUser: 1000

  - target:
      kind: Service
      name: frontend
    patch: |-
      - op: replace
        path: /spec/type
        value: LoadBalancer
```

---

## Advanced Features

### 1. Replacements (New Way to Share Values)

**base/kustomization.yaml:**

```yaml
replacements:
  - source:
      kind: ConfigMap
      name: shared-config
      fieldPath: data.appVersion
    targets:
      - select:
          kind: Deployment
        fieldPaths:
          - spec.template.metadata.labels.version
      - select:
          kind: Service
        fieldPaths:
          - metadata.annotations.[app.version]
```

### 2. Helm Chart Inflation

Kustomize can render Helm charts:

```yaml
helmCharts:
  - name: minecraft
    repo: https://itzg.github.io/minecraft-server-charts
    version: 3.1.3
    releaseName: minetest
    namespace: games
    valuesFile: values.yaml
    valuesInline:
      minecraftServer:
        eula: true
        difficulty: hard
```

### 3. Generators with Custom Behavior

```yaml
configMapGenerator:
  - name: app-config
    behavior: create  # create, replace, or merge
    files:
      - application.yaml

generatorOptions:
  disableNameSuffixHash: false
  labels:
    config-type: application
  annotations:
    config-version: "1.0"
```

### 4. Label and Annotation Transformers

```yaml
commonLabels:
  app.kubernetes.io/name: myapp
  app.kubernetes.io/managed-by: kustomize

commonAnnotations:
  prometheus.io/scrape: "true"
  prometheus.io/port: "9090"
```

### 5. Name Reference Transformers

Automatically updates references when names change:

```yaml
namePrefix: prod-

# ConfigMap reference in Deployment will update automatically
# from: configMapRef: { name: app-config }
# to:   configMapRef: { name: prod-app-config }
```

### 6. Component Architecture

Reusable pieces across overlays:

```
app/
├── base/
│   └── kustomization.yaml
├── components/
│   ├── monitoring/
│   │   ├── kustomization.yaml
│   │   └── servicemonitor.yaml
│   └── ingress/
│       ├── kustomization.yaml
│       └── ingress.yaml
└── overlays/
    ├── dev/
    │   └── kustomization.yaml  # includes monitoring component
    └── prod/
        └── kustomization.yaml  # includes both components
```

**components/monitoring/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1alpha1
kind: Component

resources:
  - servicemonitor.yaml
```

**overlays/prod/kustomization.yaml:**

```yaml
components:
  - ../../components/monitoring
  - ../../components/ingress
```

### 7. Post-Build Variable Substitution

```yaml
replacements:
  - source:
      kind: Service
      name: frontend
      fieldPath: metadata.name
    targets:
      - select:
          kind: Ingress
        fieldPaths:
          - spec.rules.0.http.paths.0.backend.service.name
```

### 8. OpenAPI Schema Validation

```yaml
openapi:
  path: https://raw.githubusercontent.com/kubernetes/kubernetes/v1.25.0/api/openapi-spec/swagger.json

# Kustomize will validate resources against schema
```

---

## Quick Reference

### Essential Commands

```bash
# Build and preview
kustomize build <directory>
oc kustomize <directory>

# Apply directly
oc apply -k <directory>

# Delete
oc delete -k <directory>

# Diff
oc diff -k <directory>

# Build to file
kustomize build overlays/prod > prod.yaml

# Validate (dry-run)
oc apply -k <directory> --dry-run=client

# Server-side dry run
oc apply -k <directory> --dry-run=server

# Create kustomization.yaml
kustomize create --autodetect

# Add resource
kustomize edit add resource deployment.yaml

# Set image
kustomize edit set image myapp=myapp:v2.0.0

# Set namespace
kustomize edit set namespace production

# Set name prefix
kustomize edit set nameprefix prod-
```

### Kustomization Fields Quick Ref

| Field | Type | Purpose | Example |
|-------|------|---------|---------|
| `resources` | list | YAML files to include | `- deployment.yaml` |
| `bases` | list | Other kustomizations | `- ../base` |
| `namespace` | string | Set namespace | `production` |
| `namePrefix` | string | Prefix all names | `dev-` |
| `nameSuffix` | string | Suffix all names | `-v2` |
| `commonLabels` | map | Add labels | `app: myapp` |
| `commonAnnotations` | map | Add annotations | `version: "1.0"` |
| `images` | list | Update images | `- name: myapp` |
| `replicas` | list | Set replicas | `- name: deploy` |
| `configMapGenerator` | list | Generate ConfigMaps | `- name: config` |
| `secretGenerator` | list | Generate Secrets | `- name: secret` |
| `patchesStrategicMerge` | list | YAML patches | `- patch.yaml` |
| `patchesJson6902` | list | JSON patches | See example |
| `patches` | list | Inline patches | See example |
| `replacements` | list | Value substitution | See example |

### Patch Types

| Type | Use Case | Format | Target Precision |
|------|----------|--------|------------------|
| Strategic Merge | Kubernetes-aware merging | YAML | Resource kind/name |
| JSON 6902 | Precise modifications | JSON/YAML ops | Field paths |
| Inline Patch | Quick changes | YAML in kustomization | Flexible selectors |

### Image Update Strategies

```yaml
images:
  # Update tag only
  - name: myapp
    newTag: v2.0.0

  # Update registry and tag
  - name: myapp
    newName: myregistry.io/myapp
    newTag: v2.0.0

  # Use digest (immutable)
  - name: myapp
    digest: sha256:abc123def456...

  # Combined
  - name: myapp
    newName: myregistry.io/myapp
    newTag: v2.0.0
    digest: sha256:abc123def456...
```

### Generator Options

```yaml
generatorOptions:
  # Disable hash suffix (default: false)
  disableNameSuffixHash: true

  # Add labels to generated resources
  labels:
    generated: "true"

  # Add annotations
  annotations:
    generator: kustomize

  # Behavior: create, replace, merge
  behavior: merge
```

---

## Practical Examples

### Example 1: Multi-Environment Application

```
myapp/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml
    │   ├── dev-config.env
    │   └── dev-patch.yaml
    ├── staging/
    │   ├── kustomization.yaml
    │   └── staging-patch.yaml
    └── prod/
        ├── kustomization.yaml
        ├── prod-patch.yaml
        ├── hpa.yaml
        └── pdb.yaml
```

**base/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml
  - configmap.yaml

commonLabels:
  app: myapp
  managed-by: kustomize
```

**overlays/dev/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namespace: dev

namePrefix: dev-

replicas:
  - name: myapp
    count: 1

images:
  - name: myapp
    newTag: latest

configMapGenerator:
  - name: env-config
    envs:
      - dev-config.env

patchesStrategicMerge:
  - dev-patch.yaml
```

**overlays/prod/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base

namespace: production

namePrefix: prod-

replicas:
  - name: myapp
    count: 5

images:
  - name: myapp
    newTag: v1.2.3
    digest: sha256:abc123...

resources:
  - hpa.yaml
  - pdb.yaml

patchesStrategicMerge:
  - prod-patch.yaml

commonAnnotations:
  environment: production
  cost-center: "12345"
```

### Example 2: ArgoCD Integration

**.argocd/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

namespace: argocd

resources:
  - https://raw.githubusercontent.com/argoproj/argo-cd/v2.8.0/manifests/install.yaml

patchesStrategicMerge:
  - argocd-cm-patch.yaml
  - argocd-server-service-patch.yaml

images:
  - name: quay.io/argoproj/argocd
    newTag: v2.8.0
```

### Example 3: Microservices Monorepo

```
services/
├── base/
│   ├── kustomization.yaml
│   └── common/
│       ├── namespace.yaml
│       └── rbac.yaml
├── frontend/
│   ├── base/
│   │   ├── kustomization.yaml
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── overlays/
│       ├── dev/
│       └── prod/
├── backend/
│   ├── base/
│   │   ├── kustomization.yaml
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── overlays/
│       ├── dev/
│       └── prod/
└── overlays/
    ├── dev/
    │   └── kustomization.yaml
    └── prod/
        └── kustomization.yaml
```

**overlays/dev/kustomization.yaml:**

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

bases:
  - ../../base
  - ../../frontend/overlays/dev
  - ../../backend/overlays/dev

namespace: dev-microservices
```

### Example 4: ConfigMap Versioning

When ConfigMap changes, pods should restart. Use name suffixes:

```yaml
configMapGenerator:
  - name: app-config
    files:
      - config.json
    # Hash suffix will change when content changes
    # e.g., app-config-abc123xyz

generatorOptions:
  disableNameSuffixHash: false  # Enable hash suffix
```

Deployment will automatically reference `app-config-abc123xyz`, so changing config triggers new rollout.

### Example 5: Secret Management with External Tool

**Using sealed-secrets:**

```yaml
# Generate sealed secret
kubeseal --format=yaml < secret.yaml > sealed-secret.yaml

# Include in kustomization
resources:
  - sealed-secret.yaml
```

**Using SOPS:**

```bash
# Encrypt
sops --encrypt --in-place secret.yaml

# Kustomize with SOPS generator
# Install: https://github.com/viaduct-ai/kustomize-sops
generators:
  - secret-generator.yaml
```

### Example 6: Blue-Green Deployment

```
app/
├── base/
│   └── kustomization.yaml
└── overlays/
    ├── blue/
    │   └── kustomization.yaml
    └── green/
        └── kustomization.yaml
```

**overlays/blue/kustomization.yaml:**

```yaml
namePrefix: blue-
commonLabels:
  version: blue

images:
  - name: myapp
    newTag: v1.0.0
```

**overlays/green/kustomization.yaml:**

```yaml
namePrefix: green-
commonLabels:
  version: green

images:
  - name: myapp
    newTag: v2.0.0
```

Switch traffic by updating Service selector.

### Example 7: Multi-Cluster with Shared Base

```
clusters/
├── base/
│   └── kustomization.yaml
├── us-east-1/
│   ├── kustomization.yaml
│   └── cluster-config.yaml
├── us-west-2/
│   ├── kustomization.yaml
│   └── cluster-config.yaml
└── eu-central-1/
    ├── kustomization.yaml
    └── cluster-config.yaml
```

Each cluster overlay customizes region-specific settings.

---

## Best Practices

### 1. Directory Structure

**Recommended:**

```
app/
├── base/                    # Shared base configuration
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/                # Environment-specific
    ├── dev/
    ├── staging/
    └── prod/
```

**Alternative (Components):**

```
app/
├── base/
├── components/              # Reusable pieces
│   ├── monitoring/
│   ├── ingress/
│   └── autoscaling/
└── overlays/
    ├── dev/
    └── prod/
```

### 2. Use Bases for Reusability

```yaml
# Don't duplicate YAML
bases:
  - ../../base
  - github.com/org/common-configs//database?ref=v1.0.0
```

### 3. Keep Patches Small and Focused

```yaml
# Good: One concern per patch
patchesStrategicMerge:
  - replica-count.yaml
  - resource-limits.yaml
  - security-context.yaml

# Avoid: Monolithic patches
```

### 4. Use ConfigMap/Secret Generators

```yaml
# Automatically restarts pods on config change
configMapGenerator:
  - name: app-config
    files:
      - config.yaml
# Hash suffix changes → pod restart
```

### 5. Pin Remote Bases with Tags/Commits

```yaml
# Good: Pinned version
bases:
  - github.com/org/repo//base?ref=v1.2.3

# Avoid: Unpinned (breaks reproducibility)
bases:
  - github.com/org/repo//base
```

### 6. Use Namespaces Wisely

```yaml
# Set once in overlay
namespace: production

# Don't hardcode in base resources
```

### 7. Label Everything

```yaml
commonLabels:
  app.kubernetes.io/name: myapp
  app.kubernetes.io/instance: prod
  app.kubernetes.io/version: "1.0"
  app.kubernetes.io/component: backend
  app.kubernetes.io/part-of: ecommerce
  app.kubernetes.io/managed-by: kustomize
```

### 8. Use Image Digests in Production

```yaml
images:
  - name: myapp
    newTag: v1.2.3
    digest: sha256:abc123...  # Immutable reference
```

### 9. Validate Before Applying

```bash
# Preview
oc kustomize overlays/prod

# Dry-run
oc apply -k overlays/prod --dry-run=server

# Diff
oc diff -k overlays/prod
```

### 10. Document Your Overlays

Add README in each overlay:

```
overlays/prod/README.md
```

```markdown
# Production Overlay

- **Replicas:** 5
- **Image:** myapp:v1.2.3 (sha256:abc123...)
- **Namespace:** production
- **Special Resources:** HPA, PDB
- **ConfigMaps:** prod-config (from files/prod.env)
```

---

## Troubleshooting

### Problem: "Resource not found in base"

**Error:**

```
Error: couldn't find resource deployment.yaml in base
```

**Solution:**

Check paths are correct relative to kustomization.yaml:

```yaml
resources:
  - ../../base/deployment.yaml  # Correct path?
```

### Problem: "Namespace Already Set"

**Error:**

```
Error: namespace is already set
```

**Solution:**

Remove hardcoded namespace from base resources. Let overlays set it:

```yaml
# base/deployment.yaml - Remove this:
# metadata:
#   namespace: default

# overlays/prod/kustomization.yaml - Set here:
namespace: production
```

### Problem: ConfigMap Not Updating

**Issue:** Pod not restarting when ConfigMap changes.

**Solution:**

Enable hash suffix:

```yaml
configMapGenerator:
  - name: app-config
    files:
      - config.yaml

generatorOptions:
  disableNameSuffixHash: false  # Must be false
```

### Problem: "Invalid Patch"

**Error:**

```
Error: error applying patch
```

**Solution:**

Ensure patch matches resource structure:

```yaml
# Patch must have correct apiVersion, kind, metadata.name
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp  # Must match target
spec:
  replicas: 3
```

### Problem: Image Not Updating

**Issue:** `images` field not working.

**Solution:**

Ensure base uses exact container name:

```yaml
# base/deployment.yaml
spec:
  template:
    spec:
      containers:
      - name: myapp  # Must match images.name

# overlay/kustomization.yaml
images:
  - name: myapp  # Exact match
    newTag: v2.0.0
```

### Problem: "Duplicate Resource"

**Error:**

```
Error: multiple resources match name
```

**Solution:**

Use unique names or name prefixes:

```yaml
namePrefix: dev-
```

Or specify target more precisely:

```yaml
patchesJson6902:
  - target:
      group: apps
      version: v1
      kind: Deployment
      name: myapp
      namespace: default  # Add namespace to be specific
    path: patch.yaml
```

### Problem: Remote Base Not Found

**Error:**

```
Error: failed to load remote base
```

**Solution:**

Check URL format and ref:

```yaml
# Correct formats:
bases:
  - github.com/user/repo//path/to/base?ref=v1.0.0
  - https://github.com/user/repo//path/to/base?ref=main
```

### Problem: Strategic Merge Not Merging Arrays

**Issue:** Arrays are replaced, not merged.

**Solution:**

Use `$patch: merge` directive:

```yaml
spec:
  template:
    spec:
      containers:
      - name: myapp
        env:
          $patch: merge  # Merge env vars instead of replace
        - name: NEW_VAR
          value: "new"
```

Or use JSON Patch for precise control:

```yaml
patchesJson6902:
  - target:
      kind: Deployment
      name: myapp
    patch: |-
      - op: add
        path: /spec/template/spec/containers/0/env/-
        value:
          name: NEW_VAR
          value: "new"
```

---

## Additional Resources

### Official Documentation

- **Kustomize Docs:** https://kubectl.docs.kubernetes.io/guides/introduction/kustomize/
- **GitHub:** https://github.com/kubernetes-sigs/kustomize
- **Examples:** https://github.com/kubernetes-sigs/kustomize/tree/master/examples

### Tools and Integrations

- **Flux CD:** GitOps with Kustomize - https://fluxcd.io/
- **ArgoCD:** GitOps with Kustomize - https://argo-cd.readthedocs.io/
- **Kustomize Controller:** https://fluxcd.io/docs/components/kustomize/
- **SOPS:** Secret encryption - https://github.com/mozilla/sops

### Ecosystem

- **Kustomize Plugins:** https://kubectl.docs.kubernetes.io/guides/extending_kustomize/
- **KRM Functions:** https://github.com/kubernetes-sigs/kustomize/blob/master/cmd/config/docs/api-conventions/functions-spec.md

### Community

- **Kubernetes Slack:** #kustomize channel
- **Stack Overflow:** [kustomize] tag

---

## Cheat Sheet Summary

```bash
# Initialize
kustomize create --autodetect

# Build
kustomize build .
oc kustomize .

# Apply
oc apply -k .
oc apply -k overlays/prod

# Delete
oc delete -k .

# Edit
kustomize edit add resource deployment.yaml
kustomize edit set image myapp=myapp:v2
kustomize edit set namespace prod
kustomize edit set nameprefix prod-
kustomize edit add label app:myapp

# Validate
oc apply -k . --dry-run=client
oc diff -k .

# Build to file
kustomize build . > output.yaml

# Remote base
oc apply -k github.com/user/repo//base?ref=v1.0.0
```

### Minimal kustomization.yaml

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml
```

### Environment Overlay Pattern

```yaml
# overlays/prod/kustomization.yaml
bases:
  - ../../base

namespace: production
namePrefix: prod-

replicas:
  - name: myapp
    count: 3

images:
  - name: myapp
    newTag: v1.0.0

commonLabels:
  environment: prod
```

---

**Happy Kustomizing!**

This guide covers essential and advanced Kustomize patterns for Kubernetes configuration management. For more details, consult the [official Kustomize documentation](https://kubectl.docs.kubernetes.io/).
