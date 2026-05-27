# OpenShift Virtualization Quick Reference

**Last Updated:** 2026-03-21
**Target Audience:** SREs, Platform Engineers, Traditional Pipelines Team
**Level:** Beginner to Advanced

---

## Table of Contents

1. [What is OpenShift Virtualization?](#what-is-openshift-virtualization)
2. [Why It Matters for Traditional Pipelines](#why-it-matters-for-traditional-pipelines)
3. [Architecture Overview](#architecture-overview)
4. [Managing Virtual Machines](#managing-virtual-machines)
5. [Common Operations](#common-operations)
6. [Monitoring & Troubleshooting](#monitoring--troubleshooting)
7. [Traditional Pipelines Use Cases](#traditional-pipelines-use-cases)
8. [Quick Reference](#quick-reference)
9. [Related Documentation](#related-documentation)

---

## What is OpenShift Virtualization?

**OpenShift Virtualization** (formerly Container-Native Virtualization / CNV) allows you to run **virtual machines alongside containers** on the same OpenShift cluster.

**Built on:**
- **KubeVirt** - Open-source Kubernetes extension for VMs
- **libvirt** - Virtualization library
- **QEMU/KVM** - Hypervisor technology

**Key Benefits:**
- Unified platform (containers + VMs)
- Kubernetes-native VM management (kubectl, oc commands)
- Live migration of VMs
- Integration with OpenShift features (networking, storage, RBAC)
- Gradual migration from VMs to containers

---

### OpenShift Virtualization vs Traditional Virtualization

| Feature | OpenShift Virtualization | VMware / RHEV / libvirt |
|---------|------------------------|------------------------|
| Management | Kubernetes API (oc, kubectl) | Separate management tools |
| Platform | OpenShift clusters | Standalone hypervisors |
| Networking | OpenShift SDN | vSwitch, physical networks |
| Storage | PVCs, OpenShift storage | SAN, NFS, local disks |
| Automation | Kubernetes operators | Ansible, custom scripts |
| Co-location | VMs + Containers | VMs only |

---

### the company ITUP Platform

**ITUP (IT Unified Platform)** is the company's internal OpenShift-based infrastructure platform.

**What it provides:**
- Multi-tenant OpenShift clusters
- Centralized management and monitoring
- Standardized deployment workflows
- Integration with the company IT services

**Traditional Pipelines Services on ITUP:**
- **Dist-Git** - Running on ITUP (OCP)
- **Service-A (Module Build Service)** - Running on ITUP (OCP)
- **Service-C** - Partially running on ITUP (OCP) + Mainframe

---

## Why It Matters for Traditional Pipelines

### Legacy Service Migration

**Many traditional pipelines services are:**
- Legacy RHEL VMs (not yet containerized)
- Require specific RHEL versions (RHEL 7, 8, 9)
- Have complex dependencies (databases, caches, file systems)
- Stateful workloads (persistent data)

**OpenShift Virtualization enables:**
- Lift-and-shift VMs to OpenShift without re-architecting
- Gradual migration (VM → containerized services over time)
- Unified operations (same oc commands for VMs and containers)
- Modern automation (Kubernetes operators for VM lifecycle)

---

### Real-World Examples

**Dist-Git on OpenShift Virtualization:**
```
Platform: ITUP (OCP)
RHEL Version: 9
Architecture: x86_64
Deployment: Virtual Machines on OpenShift

Why VM and not Container?
- Complex git repository structure
- NFS dependencies for lookaside cache
- Requires specific RHEL kernel features
- Gradual modernization in progress
```

**Service-A (Module Build Service):**
```
Platform: ITUP (OCP)
RHEL Version: 8
Components:
- service-a-frontend (VM)
- service-a-backend (VM)
- service-a-database (VM)

Why VMs?
- Database requires specific RHEL 8 configuration
- Integration with Service-C (legacy system)
- Persistent state management
- Migration to containers planned
```

**Service-C:**
```
Platform: ITUP (OCP) + Mainframe
Architecture: x86_64 (OCP), s390x (Mainframe)

Hybrid deployment:
- Builders on OpenShift VMs (x86_64)
- Hub on Mainframe (s390x)
- Windows builders on physical hardware (x86_64)
```

---

## Architecture Overview

### Components

**OpenShift Virtualization Stack:**

```
┌─────────────────────────────────────────────────┐
│     OpenShift Cluster (ITUP)        │
├─────────────────────────────────────────────────┤
│ ┌───────────────┐ ┌──────────────────────┐  │
│ │ Containers  │ │ Virtual Machines   │  │
│ │        │ │           │  │
│ │ - Pods    │ │ - VirtualMachine  │  │
│ │ - Deployments│ │ - VirtualMachineInstance│
│ │ - StatefulSet│ │ - DataVolumes    │  │
│ └───────────────┘ └──────────────────────┘  │
├─────────────────────────────────────────────────┤
│ OpenShift Virtualization Operator (KubeVirt) │
├─────────────────────────────────────────────────┤
│ Kubernetes API (kubectl, oc commands)     │
├─────────────────────────────────────────────────┤
│ OpenShift Control Plane            │
├─────────────────────────────────────────────────┤
│ Worker Nodes (with KVM enabled)        │
│ - libvirt + QEMU hypervisor          │
│ - Container runtime (CRI-O)          │
└─────────────────────────────────────────────────┘
```

---

### Key Resources

**VirtualMachine (VM):**
- Defines the VM specification (CPU, memory, disks, network)
- Persistent resource (survives restarts)
- Similar to Deployment for containers

**VirtualMachineInstance (VMI):**
- Running instance of a VM
- Temporary (deleted when VM stops)
- Similar to Pod for containers

**DataVolume:**
- Persistent storage for VM disks
- Backed by PersistentVolumeClaims (PVCs)
- Supports cloning, importing, uploading disk images

---

## Managing Virtual Machines

### Using oc / kubectl Commands

**List VMs:**

```bash
# List all VMs in namespace
oc get virtualmachines -n <namespace>
oc get vm -n <namespace>

# List running VM instances
oc get virtualmachineinstances -n <namespace>
oc get vmi -n <namespace>

# Get VM details
oc describe vm <vm-name> -n <namespace>
```

**Start / Stop / Restart VM:**

```bash
# Start VM
virtctl start <vm-name> -n <namespace>

# Stop VM (graceful shutdown)
virtctl stop <vm-name> -n <namespace>

# Restart VM
virtctl restart <vm-name> -n <namespace>

# Force stop (hard power off)
oc patch vm <vm-name> -n <namespace> --type merge -p '{"spec":{"running":false}}'
```

**Access VM Console:**

```bash
# Serial console (text-based)
virtctl console <vm-name> -n <namespace>

# VNC console (graphical - requires VNC client)
virtctl vnc <vm-name> -n <namespace>

# SSH into VM (if network configured)
virtctl ssh <user>@<vm-name> -n <namespace>
```

**Check VM Status:**

```bash
# Get VM status
oc get vm <vm-name> -n <namespace> -o yaml

# Check if VM is running
oc get vmi <vm-name> -n <namespace>

# Get VM logs (console output)
oc logs virt-launcher-<vm-name>-xxxxx -n <namespace>
```

---

### VM Lifecycle

**Create VM from YAML:**

```yaml
apiVersion: kubevirt.io/v1
kind: VirtualMachine
metadata:
name: dist-git-frontend
namespace: traditional-pipelines
spec:
running: true
template:
metadata:
labels:
app: dist-git
tier: frontend
spec:
domain:
cpu:
cores: 4
memory:
guest: 16Gi
devices:
disks:
- name: rootdisk
disk:
bus: virtio
- name: cloudinit
disk:
bus: virtio
interfaces:
- name: default
masquerade: {}
networks:
- name: default
pod: {}
volumes:
- name: rootdisk
dataVolume:
name: dist-git-frontend-root
- name: cloudinit
cloudInitNoCloud:
userDataSecretRef:
name: dist-git-cloud-init
```

**Apply configuration:**

```bash
oc apply -f dist-git-vm.yaml
```

---

### Managing VM Storage

**Create DataVolume (VM disk):**

```yaml
apiVersion: cdi.kubevirt.io/v1beta1
kind: DataVolume
metadata:
name: dist-git-frontend-root
namespace: traditional-pipelines
spec:
source:
pvc:
namespace: golden-images
name: rhel9-base-image
pvc:
accessModes:
- ReadWriteOnce
resources:
requests:
storage: 100Gi
storageClassName: ocs-storagecluster-ceph-rbd
```

**Check storage:**

```bash
# List DataVolumes
oc get datavolume -n <namespace>
oc get dv -n <namespace>

# List PVCs (underlying storage)
oc get pvc -n <namespace>

# Check storage usage
oc describe pvc <pvc-name> -n <namespace>
```

---

## Common Operations

### Live Migration

**Migrate VM to different node (zero downtime):**

```bash
# Trigger live migration
virtctl migrate <vm-name> -n <namespace>

# Check migration status
oc get virtualmachineinstancemigration -n <namespace>

# Cancel migration
oc delete virtualmachineinstancemigration <migration-name> -n <namespace>
```

**Use cases:**
- Node maintenance (drain node without downtime)
- Resource balancing
- Hardware upgrades

---

### Snapshots and Clones

**Create VM snapshot:**

```yaml
apiVersion: snapshot.kubevirt.io/v1alpha1
kind: VirtualMachineSnapshot
metadata:
name: dist-git-snapshot-20260321
namespace: traditional-pipelines
spec:
source:
apiGroup: kubevirt.io
kind: VirtualMachine
name: dist-git-frontend
```

```bash
oc apply -f snapshot.yaml

# List snapshots
oc get vmsnapshot -n <namespace>

# Restore from snapshot
oc apply -f restore.yaml
```

**Clone VM:**

```bash
# Clone creates new VM from existing VM or snapshot
virtctl image-upload dv <new-vm-disk> \
--source-datavolume=<original-vm-disk> \
--namespace=<namespace>
```

---

### Networking

**Expose VM service:**

```bash
# Create Service for VM
oc expose vm <vm-name> --port=80 --target-port=8080 -n <namespace>

# Create Route (external access)
oc create route edge --service=<vm-service> -n <namespace>

# Get external URL
oc get route -n <namespace>
```

**Check VM network:**

```bash
# Get VM IP address
oc get vmi <vm-name> -n <namespace> -o jsonpath='{.status.interfaces[0].ipAddress}'

# Test connectivity
oc debug node/<node-name> -- ping <vm-ip>
```

---

## Monitoring & Troubleshooting

### Monitoring VM Health

**Check VM status:**

```bash
# VM overview
oc get vm,vmi -n <namespace>

# Detailed status
oc describe vm <vm-name> -n <namespace>

# Check events
oc get events -n <namespace> --field-selector involvedObject.name=<vm-name>
```

**Resource usage:**

```bash
# CPU and memory usage (if metrics-server installed)
oc adm top pods -n <namespace> -l kubevirt.io/domain=<vm-name>

# Disk usage
oc get pvc -n <namespace>
oc describe pvc <pvc-name> -n <namespace>
```

---

### Common Issues and Solutions

#### Issue 1: VM Won't Start

**Symptoms:** VM stays in "Stopped" state

**Debug:**

```bash
# Check VM events
oc describe vm <vm-name> -n <namespace>

# Check virt-launcher pod logs
oc get pods -n <namespace> | grep virt-launcher
oc logs <virt-launcher-pod> -n <namespace>

# Common causes:
# - Insufficient node resources
# - Storage not ready (PVC pending)
# - Image not found
```

**Solutions:**

```bash
# Check node capacity
oc describe nodes | grep -A5 "Allocated resources"

# Check PVC status
oc get pvc -n <namespace>

# Recreate VM if stuck
oc delete vm <vm-name> -n <namespace>
oc apply -f vm.yaml
```

---

#### Issue 2: VM Network Issues

**Symptoms:** Can't reach VM, VM can't reach external services

**Debug:**

```bash
# Check VMI interfaces
oc get vmi <vm-name> -n <namespace> -o yaml | grep -A10 interfaces

# Get VM IP
oc get vmi <vm-name> -n <namespace> -o jsonpath='{.status.interfaces[0].ipAddress}'

# Test from another pod
oc run -it --rm debug --image=busybox --restart=Never -- ping <vm-ip>
```

**Solutions:**

```bash
# Verify Service exists
oc get svc -n <namespace>

# Check Network Policies
oc get networkpolicy -n <namespace>

# Restart VM network
virtctl restart <vm-name> -n <namespace>
```

---

#### Issue 3: VM Disk Full

**Symptoms:** VM performance degraded, services failing

**Debug:**

```bash
# Check PVC usage (requires metrics)
oc exec -it <virt-launcher-pod> -n <namespace> -- df -h

# Access VM console
virtctl console <vm-name> -n <namespace>
# Inside VM:
df -h
du -sh /var/log/*
```

**Solutions:**

```bash
# Expand PVC (if storage class supports it)
oc patch pvc <pvc-name> -n <namespace> -p '{"spec":{"resources":{"requests":{"storage":"200Gi"}}}}'

# Clean up inside VM (via console)
virtctl console <vm-name> -n <namespace>
# rm /var/log/old-logs/*
# journalctl --vacuum-time=7d
```

---

#### Issue 4: VM Migration Failed

**Symptoms:** Live migration stuck or failed

**Debug:**

```bash
# Check migration status
oc get virtualmachineinstancemigration -n <namespace>

# Get migration details
oc describe virtualmachineinstancemigration <migration-name> -n <namespace>

# Check source and target nodes
oc get vmi <vm-name> -n <namespace> -o yaml | grep nodeName
```

**Solutions:**

```bash
# Cancel failed migration
oc delete virtualmachineinstancemigration <migration-name> -n <namespace>

# Retry migration
virtctl migrate <vm-name> -n <namespace>

# If persistent failures, check:
# - Network connectivity between nodes
# - Storage class supports ReadWriteMany
# - Sufficient resources on target node
```

---

## Traditional Pipelines Use Cases

### Use Case 1: Dist-Git Service

**Deployment:**

```yaml
apiVersion: kubevirt.io/v1
kind: VirtualMachine
metadata:
name: service-b-git-prod
namespace: dgit-001
labels:
app: dist-git
env: production
spec:
running: true
template:
metadata:
labels:
app: dist-git
spec:
domain:
cpu:
cores: 8
memory:
guest: 32Gi
devices:
disks:
- name: rootdisk
disk:
bus: virtio
- name: datadisk
disk:
bus: virtio
interfaces:
- name: default
bridge: {}
networks:
- name: default
multus:
networkName: dist-git-network
volumes:
- name: rootdisk
dataVolume:
name: service-b-git-root
- name: datadisk
persistentVolumeClaim:
claimName: dist-git-repos
```

**Why VM for Dist-Git:**
- Complex git repository structure
- NFS mount for lookaside cache
- Requires specific RHEL 9 kernel features
- Legacy authentication systems integration

**Monitoring:**

```bash
# Check Dist-Git VM status
oc get vm service-b-git-prod -n dgit-001

# Access logs
oc logs -f virt-launcher-service-b-git-prod-xxxxx -n dgit-001

# Splunk query
# host="*.dgit-001.prod.iad2.dc.company.internal"
```

---

### Use Case 2: Service-A (Module Build Service)

**Multi-VM Architecture:**

```
┌─────────────────────────────────────────┐
│ Service-A Service on OpenShift        │
├─────────────────────────────────────────┤
│ ┌─────────────┐ ┌──────────────────┐ │
│ │ service-a-frontend│ │ service-a-backend   │ │
│ │ (VM)    │ │ (VM)      │ │
│ │ RHEL 8   │ │ RHEL 8     │ │
│ │ Port: 8080 │ │ Celery workers │ │
│ └─────────────┘ └──────────────────┘ │
│     │         │       │
│     └──────────┬───────┘       │
│          │           │
│      ┌───────────────┐       │
│      │ service-a-database │       │
│      │ (VM)     │       │
│      │ PostgreSQL  │       │
│      │ RHEL 8    │       │
│      └───────────────┘       │
└─────────────────────────────────────────┘
```

**Deployment manifests:**

```bash
# Apply all Service-A VMs
oc apply -f service-a-frontend-vm.yaml
oc apply -f service-a-backend-vm.yaml
oc apply -f service-a-database-vm.yaml

# Verify all running
oc get vm -n service-a-001 -l app=service-a
```

**Monitoring:**

```bash
# Check all Service-A VMs
oc get vm,vmi -n service-a-001

# SignalFx dashboards (see service.md)
# - Service-A Backend: https://redhat.signalfx.com/#/dashboard/F2cNpTXA4AE
# - Service-A DB: https://redhat.signalfx.com/#/dashboard/F2b6-4lAwAE
# - Service-A Frontend: https://redhat.signalfx.com/#/dashboard/F2b6qinA0AA
```

---

### Use Case 3: Service-C Builders

**Service-C Build Environment:**

```
┌─────────────────────────────────────────────────┐
│ Service-C Infrastructure              │
├─────────────────────────────────────────────────┤
│ OpenShift (ITUP)   │ Mainframe (Z15/Z16) │
│ ┌─────────────────┐  │ ┌──────────────────┐ │
│ │ Builders (VMs) │  │ │ Service-C Hub    │ │
│ │ - x86_64    │◄──┼──┤ - s390x     │ │
│ │ - RHEL 7/8/9  │  │ │ - koji     │ │
│ └─────────────────┘  │ └──────────────────┘ │
│            │            │
│ ┌─────────────────┐  │            │
│ │ Windows Builders│  │            │
│ │ - Physical HW  │◄──┼────────────────────────┤
│ │ - x86_64-07   │  │            │
│ └─────────────────┘  │            │
└─────────────────────────────────────────────────┘
```

**Check Service-C builders:**

```bash
# List Service-C builder VMs
oc get vm -n service-c-001 -l role=builder

# Check builder capacity
oc get vm -n service-c-001 -o custom-columns=NAME:.metadata.name,CPU:.spec.template.spec.domain.cpu.cores,MEMORY:.spec.template.spec.domain.memory.guest
```

**Why VMs for Service-C:**
- Legacy Koji integration
- Requires specific RHEL versions per build target
- Complex build isolation requirements
- RPM/Container build environment dependencies

---

## Quick Reference

### Essential Commands

| Task | Command |
|------|---------|
| **List VMs** | `oc get vm -n <namespace>` |
| **List running VMs** | `oc get vmi -n <namespace>` |
| **Start VM** | `virtctl start <vm> -n <ns>` |
| **Stop VM** | `virtctl stop <vm> -n <ns>` |
| **Restart VM** | `virtctl restart <vm> -n <ns>` |
| **Console access** | `virtctl console <vm> -n <ns>` |
| **SSH into VM** | `virtctl ssh user@<vm> -n <ns>` |
| **Get VM IP** | `oc get vmi <vm> -n <ns> -o jsonpath='{.status.interfaces[0].ipAddress}'` |
| **Migrate VM** | `virtctl migrate <vm> -n <ns>` |
| **Check events** | `oc get events -n <ns> --field-selector involvedObject.name=<vm>` |

---

### Troubleshooting Checklist

**VM won't start:**
```bash
1. oc describe vm <vm> -n <ns>
2. oc get pvc -n <ns> # Check storage
3. oc get nodes # Check node capacity
4. oc logs virt-launcher-<vm>-xxxxx -n <ns>
```

**Network issues:**
```bash
1. oc get vmi <vm> -n <ns> -o yaml | grep ipAddress
2. oc get svc -n <ns>
3. oc get networkpolicy -n <ns>
4. virtctl console <vm> -n <ns> # Check inside VM
```

**Performance issues:**
```bash
1. oc adm top pods -n <ns>
2. oc describe pvc <pvc> -n <ns>
3. virtctl console <vm> -n <ns> # Run top, df -h
4. Check SignalFx dashboards
```

---

### Traditional Pipelines VM Locations

| Service | Namespace | VMs | Platform |
|---------|-----------|-----|----------|
| **Dist-Git** | dgit-001 | service-b-git-* | ITUP (OCP) |
| **Service-A** | service-a-001 | service-a-frontend, service-a-backend, service-a-database | ITUP (OCP) |
| **Service-C** | service-c-001 | Builders (multiple) | ITUP (OCP) + Mainframe |
| **Indy** | indy-001 | indy-* | ITUP (OCP) |
| **Metaxor** | metaxor-001 | metaxor-* | ITUP (OCP) |

**Check all traditional pipelines VMs:**

```bash
# List all VMs in traditional pipeline namespaces
for ns in dgit-001 service-a-001 service-c-001 indy-001 metaxor-001; do
echo "=== $ns ==="
oc get vm -n $ns 2>/dev/null || echo "Namespace not found or no access"
done
```

---

## Related Documentation

### Internal Documentation

**OPS Notes (this site):**
- [Dist-Git Service Overview](../../traditional-pipelines/dist-git/service.md)
- [Service-A Service Overview](../../traditional-pipelines/service-a/service.md)
- [Service-C Service Overview](../../traditional-pipelines/service-c/service.md)
- [OpenShift Overview](./README.md)

**the company Internal:**
- [ITUP Platform Documentation](https://internal-docs.company.internal/itup)
- [OpenShift Virtualization Admin Guide](https://access.company.internal/documentation/en-us/openshift_container_platform/4.15/html/virtualization/)

---

### External Resources

**Official Documentation:**
- [OpenShift Virtualization Product Page](https://www.company.internal/en/technologies/cloud-computing/openshift/virtualization)
- [KubeVirt Project](https://kubevirt.io/)
- [OpenShift Virtualization 4.15 Docs](https://docs.openshift.com/container-platform/4.15/virt/about_virt/about-virt.html)

**Tutorials:**
- [Getting Started with OpenShift Virtualization](https://www.company.internal/en/blog/getting-started-openshift-virtualization)
- [VM Migration Guide](https://www.company.internal/en/blog/migrate-vms-to-openshift-virtualization)

---

## Summary

**Key Takeaways:**

1. **OpenShift Virtualization = VMs on Kubernetes**
- Run VMs and containers on same platform
- Kubernetes-native management (oc, kubectl)

2. **Traditional Pipelines Use Case**
- Legacy services (Dist-Git, Service-A, Service-C) run as VMs on ITUP
- Enables gradual migration from VMs → containers
- Unified operations and monitoring

3. **Management**
- Use `oc` and `virtctl` commands
- VMs defined as Kubernetes resources (YAML)
- Storage via DataVolumes and PVCs

4. **Troubleshooting**
- Check VM events: `oc describe vm`
- Access console: `virtctl console`
- Monitor via SignalFx dashboards

5. **Next Steps**
- Explore your namespace VMs: `oc get vm -n <namespace>`
- Access a VM console: `virtctl console <vm-name> -n <namespace>`
- Review traditional pipelines service docs

---

**Document Version:** 1.0
**Last Updated:** 2026-03-21
**Author:** Infrastructure Team (the company)
**Feedback:** Submit improvements based on operational experience
