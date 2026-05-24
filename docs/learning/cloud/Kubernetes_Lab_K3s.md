# Kubernetes Cluster from Scratch - K3s

**Created:** 2026-03-07
**Goal:** Simple, lightweight Kubernetes cluster on 3 VMs (1 master + 2 workers)

## Architecture

```
k3s-master 192.168.122.10 2 CPU, 2GB RAM, 50GB disk
k3s-worker1 192.168.122.11 2 CPU, 4GB RAM, 50GB disk
k3s-worker2 192.168.122.12 2 CPU, 4GB RAM, 50GB disk
```

**Why K3s?**
- Simple installation (few commands)
- Low resource requirements
- Full Kubernetes API
- Built-in CNI, storage, ingress (Traefik)
- Production-ready (Rancher)

---

## 1. Create VMs

### KVM/libvirt

```bash
# Master
virt-install \
 --name k3s-master \
 --ram 2048 \
 --vcpus 2 \
 --disk path=/var/lib/libvirt/images/k3s-master.qcow2,size=50 \
 --network network=default \
 --os-variant rocky9 \
 --location /path/to/rocky9.iso \
 --extra-args "console=ttyS0"

# Worker 1
virt-install \
 --name k3s-worker1 \
 --ram 4096 \
 --vcpus 2 \
 --disk path=/var/lib/libvirt/images/k3s-worker1.qcow2,size=50 \
 --network network=default \
 --os-variant rocky9 \
 --location /path/to/rocky9.iso \
 --extra-args "console=ttyS0"

# Worker 2
virt-install \
 --name k3s-worker2 \
 --ram 4096 \
 --vcpus 2 \
 --disk path=/var/lib/libvirt/images/k3s-worker2.qcow2,size=50 \
 --network network=default \
 --os-variant rocky9 \
 --location /path/to/rocky9.iso \
 --extra-args "console=ttyS0"
```

### OpenShift Virtualization

Create 3 VMs via web UI using Rocky Linux 9 / RHEL 9 image.

---

## 2. Base Configuration (All 3 VMs)

```bash
# Set hostname
# ON MASTER:
hostnamectl set-hostname k3s-master

# ON WORKER1:
hostnamectl set-hostname k3s-worker1

# ON WORKER2:
hostnamectl set-hostname k3s-worker2
```

```bash
# /etc/hosts file (same on all 3 VMs)
cat >> /etc/hosts << EOF
192.168.122.10 k3s-master
192.168.122.11 k3s-worker1
192.168.122.12 k3s-worker2
EOF
```

```bash
# Disable firewall (lab environment)
systemctl disable --now firewalld

# SELinux permissive
setenforce 0
sed -i 's/^SELINUX=enforcing/SELINUX=permissive/' /etc/selinux/config

# Basic packages
dnf install -y curl wget vim git
```

**Or use:** `scripts/prepare-node.sh`

---

## 3. Install K3s Master

**ON MASTER NODE ONLY:**

```bash
# Install K3s master
curl -sfL https://get.k3s.io | sh -

# Wait a few seconds
sleep 10

# Verification
systemctl status k3s

# Use kubectl
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get nodes
```

**Get token (needed for workers):**

```bash
cat /var/lib/rancher/k3s/server/node-token
```

Copy it! Example: `K10abcd1234567890::server:1234567890abcdef`

**Or use:** `scripts/setup-master.sh`

---

## 4. Join Worker Nodes

**ON WORKER1 AND WORKER2 NODES:**

```bash
# REPLACE THE TOKEN!
export K3S_URL="https://192.168.122.10:6443"
export K3S_TOKEN="K10abcd1234567890::server:1234567890abcdef"

curl -sfL https://get.k3s.io | sh -
```

**Verification:**

```bash
systemctl status k3s-agent
```

**Or use:** `scripts/setup-worker.sh`

---

## 5. Verification (On Master Node)

```bash
# Nodes
kubectl get nodes

# Expected:
# NAME STATUS ROLES AGE VERSION
# k3s-master Ready control-plane,master 5m v1.28.x+k3s1
# k3s-worker1 Ready <none> 2m v1.28.x+k3s1
# k3s-worker2 Ready <none> 2m v1.28.x+k3s1

# System pods
kubectl get pods -A

# StorageClass
kubectl get storageclass
```

---

## 6. First App Deploy - Nginx

```bash
# Deployment
kubectl create deployment nginx --image=nginx --replicas=3

# Service
kubectl expose deployment nginx --port=80 --type=NodePort

# Verification
kubectl get pods
kubectl get svc nginx

# Get NodePort
kubectl get svc nginx -o jsonpath='{.spec.ports[0].nodePort}'

# Test (with any node IP)
curl http://192.168.122.11:<nodeport>
```

**Or use:** `scripts/test-nginx.sh`

---

## 7. Persistent Storage Test

```bash
# Create PVC
kubectl apply -f manifests/test-pvc.yaml

# Pod with storage
kubectl apply -f manifests/test-pod-with-storage.yaml

# Write data
kubectl exec test-pod -- sh -c "echo 'Hello K3s!' > /data/test.txt"

# Delete and recreate pod
kubectl delete pod test-pod
kubectl apply -f manifests/test-pod-with-storage.yaml

# Data persisted
kubectl exec test-pod -- cat /data/test.txt
```

---

## 8. Ingress Example (Traefik)

```bash
# Application with ingress
kubectl apply -f manifests/web-app-with-ingress.yaml

# Test (add to /etc/hosts: 192.168.122.10 web.local)
curl http://web.local
```

---

## 9. Dashboard (Optional)

```bash
# Install
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

# Admin user
kubectl apply -f manifests/dashboard-admin.yaml

# Token
kubectl -n kubernetes-dashboard create token admin-user

# Proxy (on master node)
kubectl proxy --address='0.0.0.0' --accept-hosts='.*'

# Browser:
# http://192.168.122.10:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

---

## Useful Commands

See: `docs/useful-commands.md`

---

## Delete Cluster

```bash
# On master node
/usr/local/bin/k3s-uninstall.sh

# On worker nodes
/usr/local/bin/k3s-agent-uninstall.sh
```

---

## Next Steps

- [ ] MetalLB - LoadBalancer services on bare metal
- [ ] Cert-Manager - SSL certificates
- [ ] ArgoCD - GitOps
- [ ] Prometheus + Grafana - Monitoring
- [ ] Helm - Package manager

---

## Project Structure

```
k8s-lab/
 README.md # This file
 scripts/
 prepare-node.sh # VM preparation
 setup-master.sh # Master installation
 setup-worker.sh # Worker join
 test-nginx.sh # Test deployment
 manifests/
 test-pvc.yaml
 test-pod-with-storage.yaml
 web-app-with-ingress.yaml
 dashboard-admin.yaml
 docs/
 useful-commands.md
 troubleshooting.md
```

---

**Start:** `cat scripts/setup-master.sh` 
