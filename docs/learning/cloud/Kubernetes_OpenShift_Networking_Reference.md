# Kubernetes/OpenShift Networking Referencia

**Verzió:** 1.0 
**Létrehozva:** 2026-04-24 
**Nyelv:** Magyar 
**Előfeltétel:** [Networking Gyors Referencia](Networking_Gyors_Referenciak.md) ismerete

---

## Tartalomjegyzék

1. [Kubernetes Networking Alapelvek](#kubernetes-networking-alapelvek)
2. [Pod Networking](#pod-networking)
3. [CNI (Container Network Interface)](#cni-container-network-interface)
4. [Services](#services)
5. [Ingress és OpenShift Routes](#ingress-és-openshift-routes)
6. [Network Policies](#network-policies)
7. [DNS a Kubernetes-ben](#dns-a-kubernetes-ben)
8. [Service Mesh](#service-mesh)
9. [OpenShift Specifikus Networking](#openshift-specifikus-networking)
10. [Troubleshooting](#troubleshooting)

---

## Kubernetes Networking Alapelvek

### 4 Alapkövetelmény (Kubernetes Networking Model)

Kubernetes networking 4 követelménye:

1. **Pod-to-Pod kommunikáció NAT nélkül**
- Bármely pod tud kommunikálni bármely másik pod-dal
- Nincs szükség NAT-ra (Network Address Translation)
- Minden pod saját IP címet kap

2. **Node-to-Pod kommunikáció NAT nélkül**
- Node-ok közvetlenül elérhetik a pod-okat
- Pod-ok látják saját IP címüket (nem NAT-olt)

3. **Pod-to-Service kommunikáció**
- Service abstraction pod-ok felett
- Load balancing automatikusan

4. **External-to-Service kommunikáció**
- Külső forgalom elérése (Ingress, LoadBalancer)
- SSL termination, L7 routing

### Networking Rétegek

```
┌─────────────────────────────────────────┐
│ External Traffic (Internet)      │
└─────────────────┬───────────────────────┘
│
┌─────────────────▼───────────────────────┐
│ Ingress / OpenShift Route (L7)     │ ← HTTP/HTTPS routing
│ - Host-based routing          │
│ - Path-based routing          │
│ - TLS termination           │
└─────────────────┬───────────────────────┘
│
┌─────────────────▼───────────────────────┐
│ Service (L4 Load Balancing)      │ ← ClusterIP, NodePort, LoadBalancer
│ - kube-proxy (iptables/IPVS)      │
│ - Internal DNS (servicename.namespace) │
└─────────────────┬───────────────────────┘
│
┌─────────────────▼───────────────────────┐
│ Pod Network (CNI Plugin)        │ ← Calico, OVN-Kubernetes, Cilium
│ - Pod-to-Pod communication       │
│ - Network Policies           │
└─────────────────┬───────────────────────┘
│
┌─────────────────▼───────────────────────┐
│ Node Network (Physical/VM)       │ ← eth0, underlay network
└─────────────────────────────────────────┘
```

---

## Pod Networking

### IP Allokáció

Minden pod saját IP címet kap a pod CIDR-ből.

**Példa:**
```
Cluster Pod CIDR: 10.244.0.0/16

Node 1: 10.244.0.0/24 (254 pod)
├─> pod-a: 10.244.0.5
├─> pod-b: 10.244.0.6
└─> pod-c: 10.244.0.7

Node 2: 10.244.1.0/24 (254 pod)
├─> pod-d: 10.244.1.5
└─> pod-e: 10.244.1.6
```

### Pod Network Namespace

Minden pod saját network namespace-t kap:
- Saját loopback interface (lo)
- Saját routing table
- Saját iptables rules
- Saját network interfaces

**Pod interfészek:**
```bash
# Node-on belül
$ ip netns list
cni-xxxxx-yyyy-zzzz # Pod network namespace

# Pod-on belül (kubectl exec)
$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536
inet 127.0.0.1/8 scope host lo
2: eth0@if123: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450
inet 10.244.0.5/24 scope global eth0
```

### Container-to-Container (Ugyanazon Pod-on Belül)

Egy pod-ban több konténer **ugyanazt a network namespace-t osztja meg**.

**Következmények:**
- Ugyanaz az IP cím
- Port ütközések lehetségesek (egyik konténer port 8080 → másik konténer nem használhatja)
- `localhost` kommunikáció működik

**Példa:**
```yaml
apiVersion: v1
kind: Pod
metadata:
name: multi-container-pod
spec:
containers:
- name: app
image: myapp:latest
ports:
- containerPort: 8080 # App listens on 8080
- name: sidecar
image: logger:latest
# Sidecar connects to localhost:8080 (same network namespace)
```

**Sidecar konténerből:**
```bash
curl http://localhost:8080/health # Works! Same pod, same IP
```

---

## CNI (Container Network Interface)

### Mi az a CNI?

**CNI = Container Network Interface**

- Kubernetes plugin interface hálózatkezeléshez
- Pod létrehozáskor: CNI plugin allokál IP-t, konfigurálja network namespace-t
- Pod törléskor: CNI plugin felszabadítja az erőforrásokat

### Népszerű CNI Pluginek

| Plugin | Overlay Mode | Routing Mode | Network Policy | eBPF | Service Mesh |
|--------|--------------|--------------|----------------|------|--------------|
| **Calico** | VXLAN, IP-in-IP | BGP | Igen | Opcionális | Nem |
| **OVN-Kubernetes** | Geneve | - | Igen | Nem | Nem |
| **Cilium** | VXLAN, Geneve | Direct routing | Igen | **Igen** | Igen |
| **Flannel** | VXLAN, UDP | host-gw | Nem | Nem | Nem |
| **Weave Net** | Sleeve | fastdp | Igen | Nem | Nem |

### Calico

**Népszerűség:** Széles körben használt enterprise környezetekben.

**Működési Módok:**
1. **BGP Mode (No Overlay):**
- Node-ok BGP peer-ek
- Közvetlen routing (nincs encapsulation)
- Gyorsabb, de szükséges BGP support az underlay hálózaton

2. **VXLAN Mode (Overlay):**
- VXLAN encapsulation
- Működik bármilyen underlay hálózaton
- Kis performance overhead

3. **IP-in-IP Mode (Overlay):**
- IP tunnel encapsulation
- Lightweight overlay

**Network Policy:**
- Igen, natív támogatás
- Label-based rules
- Namespace isolation
- Global policies (CRD)

**Példa Calico Network Policy:**
```yaml
apiVersion: projectcalico.org/v3
kind: NetworkPolicy
metadata:
name: allow-frontend-to-backend
namespace: production
spec:
selector: app == 'backend'
ingress:
- action: Allow
protocol: TCP
source:
selector: app == 'frontend'
destination:
ports:
- 8080
egress:
- action: Allow
```

### OVN-Kubernetes (OpenShift Default)

**OVN = Open Virtual Network** (OVS - Open vSwitch alapú)

**Működés:**
- Geneve overlay network
- Distributed virtual router minden node-on
- OpenFlow rules az OVS switch-ekben

**Architektúra:**
```
┌────────────────────────────────────┐
│ ovnkube-master (control plane)  │
│ - ovn-northd (logical flows)   │
│ - ovn-nbdb (config database)   │
│ - ovn-sbdb (southbound database) │
└────────────────┬───────────────────┘
│
┌────────────┴─────────────┬──────────────┐
│             │       │
┌───▼────────┐    ┌────────▼───┐  ┌──────▼──────┐
│ Node 1   │    │ Node 2   │  │ Node 3   │
│ ovn-controller│  │ ovn-controller│ │ ovn-controller│
│ ovs-vswitchd│   │ ovs-vswitchd│ │ ovs-vswitchd│
└────────────┘    └────────────┘  └─────────────┘
```

**Előnyök:**
- Jó integráció OpenShift-tel
- Hardware offload support (SR-IOV, DPDK)
- Multi-tenant isolation
- Hybrid networking (több network interfész pod-nak)

**Hátrányok:**
- Komplexebb troubleshooting
- Több overhead mint Calico BGP mode

### Cilium (eBPF-alapú)

**eBPF = extended Berkeley Packet Filter**

**Működés:**
- eBPF programok a Linux kernel-ben
- Nincs iptables/IPVS (kernel bypass)
- Rendkívül gyors és hatékony

**Funkciók:**
- Network policies (L3/L4/L7)
- Service load balancing (eBPF-based)
- Transparent encryption (WireGuard)
- Service mesh capabilities (Envoy integration)
- Network observability (Hubble)

**Példa Cilium L7 Network Policy:**
```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
name: http-only
spec:
endpointSelector:
matchLabels:
app: backend
ingress:
- fromEndpoints:
- matchLabels:
app: frontend
toPorts:
- ports:
- port: "80"
protocol: TCP
rules:
http:
- method: "GET"
path: "/api/.*"
```

**Előnyök:**
- Kiváló performance
- L7 visibility és policies
- Hubble observability (network flow visualization)

**Hátrányok:**
- Újabb kernel szükséges (4.19+, ajánlott 5.10+)
- Komplexebb debuggolás (eBPF ismeretek)

---

## Services

### Service Típusok

#### 1. ClusterIP (Default)

**Funkció:** Belső load balancing, csak cluster-en belül elérhető.

**Példa:**
```yaml
apiVersion: v1
kind: Service
metadata:
name: backend-service
spec:
type: ClusterIP
selector:
app: backend
ports:
- protocol: TCP
port: 80    # Service port (cluster-en belüli hívás)
targetPort: 8080 # Pod port (konténer tényleges port-ja)
```

**Használat:**
```bash
# Cluster-en belül (másik pod-ból)
curl http://backend-service.default.svc.cluster.local

# Vagy rövidített forma (ha ugyanabban a namespace-ben)
curl http://backend-service
```

**IP Allokáció:**
- Service ClusterIP CIDR-ből (pl. `10.96.0.0/12`)
- Példa: `backend-service` → `10.96.100.50`

#### 2. NodePort

**Funkció:** Minden node-on nyit egy portot (30000-32767), amely forward-ol a service-re.

**Példa:**
```yaml
apiVersion: v1
kind: Service
metadata:
name: frontend-service
spec:
type: NodePort
selector:
app: frontend
ports:
- protocol: TCP
port: 80     # ClusterIP port
targetPort: 8080 # Pod port
nodePort: 30080  # Node port (opcionális, auto-assign ha nincs megadva)
```

**Elérés:**
```bash
# Bármelyik node IP-jén keresztül
curl http://<node-1-ip>:30080
curl http://<node-2-ip>:30080 # Működik, még ha a pod nem is ezen a node-on van
```

**Működés:**
```
External Client
└─> Node IP:30080 (NodePort)
└─> kube-proxy iptables rules
└─> Load balance to backend pods (10.244.x.y:8080)
```

#### 3. LoadBalancer

**Funkció:** Cloud provider load balancer (AWS ELB, GCP LB, Azure LB).

**Példa:**
```yaml
apiVersion: v1
kind: Service
metadata:
name: public-app-service
spec:
type: LoadBalancer
selector:
app: public-app
ports:
- protocol: TCP
port: 80
targetPort: 8080
```

**Működés:**
```
External Load Balancer (Cloud Provider)
└─> External IP: 203.0.113.10
└─> NodePort (auto-created)
└─> kube-proxy
└─> Backend pods
```

**Cloud-specific Annotations:**
```yaml
# AWS
metadata:
annotations:
service.beta.kubernetes.io/aws-load-balancer-type: "nlb" # Network LB
service.beta.kubernetes.io/aws-load-balancer-internal: "true"

# GCP
metadata:
annotations:
cloud.google.com/load-balancer-type: "Internal"

# Azure
metadata:
annotations:
service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

#### 4. ExternalName

**Funkció:** DNS CNAME alias külső service-ekhez.

**Példa:**
```yaml
apiVersion: v1
kind: Service
metadata:
name: external-db
spec:
type: ExternalName
externalName: mysql.external.example.com
```

**Használat:**
```bash
# Pod-ból
curl http://external-db.default.svc.cluster.local
# → DNS CNAME → mysql.external.example.com
```

### Service Discovery

#### DNS-based Discovery (Javasolt)

Kubernetes DNS (CoreDNS) automatikusan létrehoz rekordokat minden service-hez.

**DNS Formátum:**
```
<service-name>.<namespace>.svc.<cluster-domain>

Példák:
backend-service.default.svc.cluster.local
frontend-service.production.svc.cluster.local
```

**Rövidített Formák (Ugyanazon Namespace-ből):**
```bash
curl http://backend-service     # Ugyanaz a namespace
curl http://backend-service.default # Kereszt-namespace
```

#### Environment Variables (Legacy)

Kubernetes automatikusan injectel environment variable-öket minden pod-ba.

**Példa:** Ha létezik egy `redis-service` (ClusterIP: 10.96.100.20, port: 6379):
```bash
REDIS_SERVICE_SERVICE_HOST=10.96.100.20
REDIS_SERVICE_SERVICE_PORT=6379
REDIS_SERVICE_PORT=tcp://10.96.100.20:6379
REDIS_SERVICE_PORT_6379_TCP=tcp://10.96.100.20:6379
REDIS_SERVICE_PORT_6379_TCP_PROTO=tcp
REDIS_SERVICE_PORT_6379_TCP_PORT=6379
REDIS_SERVICE_PORT_6379_TCP_ADDR=10.96.100.20
```

**Hátrány:** Service-nek léteznie kell MIELŐTT a pod létrejön (ordering issue).

**Javaslat:** DNS-based discovery használata.

### kube-proxy Modes

**kube-proxy:** Minden node-on futó komponens, amely implementálja a Service load balancing-et.

#### 1. iptables Mode (Default)

**Működés:**
- iptables rules minden Service-hez
- Random load balancing (equal probability)
- Nincs health check (csak Kubernetes liveness/readiness probe-ok)

**Példa iptables Rules:**
```bash
$ sudo iptables -t nat -L -n | grep backend-service

-A KUBE-SERVICES -d 10.96.100.50/32 -p tcp -m tcp --dport 80 -j KUBE-SVC-XXXXX
-A KUBE-SVC-XXXXX -m statistic --mode random --probability 0.33 -j KUBE-SEP-AAAA
-A KUBE-SVC-XXXXX -m statistic --mode random --probability 0.50 -j KUBE-SEP-BBBB
-A KUBE-SVC-XXXXX -j KUBE-SEP-CCCC
```

**Előnyök:** Egyszerű, stabil.

**Hátrányok:**
- Nem skálázható sok service-nél (túl sok iptables rule)
- Nincs valódi load balancing algoritmus (csak random)

#### 2. IPVS Mode

**IPVS = IP Virtual Server** (Linux kernel load balancer)

**Működés:**
- Kernel-level load balancing
- Több algoritmus: round-robin, least connection, source hashing
- Jobb performance sok service esetén

**Engedélyezés:**
```yaml
# kube-proxy ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
name: kube-proxy
namespace: kube-system
data:
config.conf: |
mode: "ipvs"
ipvs:
scheduler: "rr" # round-robin
```

**Előnyök:**
- Jobb performance
- Több load balancing algoritmus
- Connection tracking

**Hátrányok:**
- Kernel module szükséges (`ip_vs`)
- Komplexebb troubleshooting

#### 3. eBPF Mode (Cilium)

**Cilium kube-proxy replacement:**
- Teljes kernel bypass
- Leggyorsabb megoldás
- Service load balancing eBPF-ben

---

## Ingress és OpenShift Routes

### Ingress (Kubernetes Standard)

**Funkció:** L7 (HTTP/HTTPS) routing külső forgalomhoz.

**Architektúra:**
```
External Traffic (Internet)
└─> Ingress Controller (NGINX, HAProxy, Traefik)
└─> Ingress Resource (Rules)
└─> Service (ClusterIP)
└─> Pods
```

#### Ingress Controller Típusok

| Controller | Provider | Protokollok | Spec Compliance |
|------------|----------|-------------|-----------------|
| **NGINX** | Nginx Inc / Community | HTTP, HTTPS, TCP, UDP | Magas |
| **HAProxy** | HAProxy Technologies | HTTP, HTTPS, TCP | Magas |
| **Traefik** | Traefik Labs | HTTP, HTTPS, TCP, UDP | Közepes |
| **AWS ALB** | Amazon | HTTP, HTTPS | Közepes (AWS-specific) |
| **GCE** | Google | HTTP, HTTPS | Közepes (GCP-specific) |

#### Ingress Példa: Host-Based Routing

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: multi-host-ingress
annotations:
nginx.ingress.kubernetes.io/rewrite-target: /
spec:
rules:
- host: app1.example.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: app1-service
port:
number: 80
- host: app2.example.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: app2-service
port:
number: 80
```

**Működés:**
```
http://app1.example.com/ → app1-service:80 → app1 pods
http://app2.example.com/ → app2-service:80 → app2 pods
```

#### Ingress Példa: Path-Based Routing

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: path-based-ingress
spec:
rules:
- host: api.example.com
http:
paths:
- path: /users
pathType: Prefix
backend:
service:
name: user-service
port:
number: 8080
- path: /orders
pathType: Prefix
backend:
service:
name: order-service
port:
number: 8080
```

**Működés:**
```
http://api.example.com/users  → user-service:8080
http://api.example.com/orders → order-service:8080
```

#### Ingress TLS Termination

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
name: tls-ingress
spec:
tls:
- hosts:
- secure.example.com
secretName: tls-secret # Kubernetes Secret (type: kubernetes.io/tls)
rules:
- host: secure.example.com
http:
paths:
- path: /
pathType: Prefix
backend:
service:
name: secure-app-service
port:
number: 80
```

**TLS Secret:**
```yaml
apiVersion: v1
kind: Secret
metadata:
name: tls-secret
type: kubernetes.io/tls
data:
tls.crt: <base64-encoded-certificate>
tls.key: <base64-encoded-private-key>
```

### OpenShift Routes (OpenShift-Specific)

**Funkció:** Egyszerűbb alternatíva Ingress-hez (OpenShift 3.x óta).

**Előnyök Ingress-hez képest:**
- Egyszerűbb konfiguráció
- Automatikus hostname generálás
- Jobb integráció OpenShift-tel

**Route Példa:**
```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
name: my-app-route
spec:
host: my-app.apps.cluster.example.com # Auto-generated or custom
to:
kind: Service
name: my-app-service
weight: 100
port:
targetPort: 8080
tls:
termination: edge # edge, passthrough, reencrypt
insecureEdgeTerminationPolicy: Redirect # HTTP → HTTPS redirect
```

#### TLS Termination Modes

| Mode | Működés | Használat |
|------|---------|-----------|
| **edge** | TLS terminál a router-nél, HTTP → backend | Legtöbb alkalmazás |
| **passthrough** | TLS pass-through, backend végzi a TLS-t | Backend TLS certificate control |
| **reencrypt** | TLS terminál, új TLS → backend | End-to-end encryption |

**Edge Termination:**
```
Client --HTTPS--> Router --HTTP--> Pod
(TLS decryption)
```

**Passthrough:**
```
Client --HTTPS--> Router --HTTPS--> Pod
(no decryption, pass-through)
```

**Re-encrypt:**
```
Client --HTTPS--> Router --HTTPS--> Pod
(TLS decrypt)  (new TLS encrypt)
```

#### Blue/Green Deployment Route

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
name: bluegreen-route
spec:
host: app.example.com
to:
kind: Service
name: app-blue
weight: 90 # 90% traffic to blue
alternateBackends:
- kind: Service
name: app-green
weight: 10 # 10% traffic to green (canary)
```

---

## Network Policies

### Kubernetes Network Policy

**Funkció:** Pod-to-Pod forgalom szabályozása (firewall rules).

**Alapértelmezett Viselkedés:**
- **Ha NINCS NetworkPolicy:** Minden pod minden pod-ot elér (open network).
- **Ha VAN NetworkPolicy egy pod-ra:** Csak az engedélyezett forgalom mehet át (deny by default).

### Példa 1: Namespace Isolation

**Feladat:** `production` namespace pod-jai csak egymással kommunikálhatnak.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: deny-from-other-namespaces
namespace: production
spec:
podSelector: {} # Applies to all pods in this namespace
policyTypes:
- Ingress
ingress:
- from:
- podSelector: {} # Allow from all pods in this namespace
```

### Példa 2: Csak Meghatározott Pod-ok Közötti Kommunikáció

**Feladat:** Csak a `frontend` pod-ok érhetik el a `backend` pod-okat (port 8080).

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: allow-frontend-to-backend
namespace: production
spec:
podSelector:
matchLabels:
app: backend
policyTypes:
- Ingress
ingress:
- from:
- podSelector:
matchLabels:
app: frontend
ports:
- protocol: TCP
port: 8080
```

### Példa 3: Database Isolation (Csak Backend Hozzáférés)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: database-policy
namespace: production
spec:
podSelector:
matchLabels:
app: postgres
policyTypes:
- Ingress
- Egress
ingress:
- from:
- podSelector:
matchLabels:
tier: backend
ports:
- protocol: TCP
port: 5432
egress:
- to:
- podSelector:
matchLabels:
app: postgres
ports:
- protocol: TCP
port: 5432
- to: # DNS resolution
- namespaceSelector: {}
podSelector:
matchLabels:
k8s-app: kube-dns
ports:
- protocol: UDP
port: 53
```

### Példa 4: Egress Policy (Kimenő Forgalom Szabályozása)

**Feladat:** Pod-ok csak HTTPS-t használhatnak külső API-khoz.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: allow-https-egress
namespace: production
spec:
podSelector:
matchLabels:
app: api-client
policyTypes:
- Egress
egress:
- to:
- namespaceSelector: {} # Allow DNS
podSelector:
matchLabels:
k8s-app: kube-dns
ports:
- protocol: UDP
port: 53
- to:
- ipBlock:
cidr: 0.0.0.0/0
except:
- 169.254.169.254/32 # Block metadata service
ports:
- protocol: TCP
port: 443
```

### Calico Global Network Policy

**Csak Calico CNI-nél működik** (CRD: `GlobalNetworkPolicy`).

**Előny:** Cluster-wide policy (nem namespace-specific).

```yaml
apiVersion: projectcalico.org/v3
kind: GlobalNetworkPolicy
metadata:
name: deny-all-ingress
spec:
order: 1000 # Lower order = higher priority
selector: all()
types:
- Ingress
ingress:
- action: Deny
```

---

## DNS a Kubernetes-ben

### CoreDNS (Default DNS Provider)

**Funkció:** Service discovery, pod hostname resolution.

**CoreDNS Pod:**
```bash
$ kubectl get pods -n kube-system -l k8s-app=kube-dns
NAME            READY  STATUS  RESTARTS  AGE
coredns-6d4b75cb6d-abc12  1/1   Running  0     10d
coredns-6d4b75cb6d-xyz34  1/1   Running  0     10d
```

### DNS Records

#### Service A Record

**Formátum:** `<service-name>.<namespace>.svc.<cluster-domain>`

**Példa:**
```bash
# Service: backend-service (namespace: default, ClusterIP: 10.96.100.50)

$ nslookup backend-service.default.svc.cluster.local
Name:  backend-service.default.svc.cluster.local
Address: 10.96.100.50
```

**Rövidített formák:**
```bash
backend-service           # Same namespace
backend-service.default       # Cross-namespace
backend-service.default.svc     # Full service path
backend-service.default.svc.cluster.local # FQDN
```

#### Pod A Record (Ha Enabled)

**Formátum:** `<pod-ip-dashed>.<namespace>.pod.<cluster-domain>`

**Példa:**
```bash
# Pod IP: 10.244.1.5

$ nslookup 10-244-1-5.default.pod.cluster.local
Name:  10-244-1-5.default.pod.cluster.local
Address: 10.244.1.5
```

**Engedélyezés:**
```yaml
# Pod spec
spec:
hostname: my-app-pod
subdomain: my-app-service # Service name
```

**Ekkor:**
```bash
$ nslookup my-app-pod.my-app-service.default.svc.cluster.local
```

#### Headless Service (StatefulSet)

**Headless Service:** `clusterIP: None` → Nincs load balancing, direct pod DNS.

**Példa:**
```yaml
apiVersion: v1
kind: Service
metadata:
name: web-headless
spec:
clusterIP: None # Headless
selector:
app: web
ports:
- port: 80
```

**StatefulSet:**
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
name: web
spec:
serviceName: web-headless
replicas: 3
selector:
matchLabels:
app: web
template:
metadata:
labels:
app: web
spec:
containers:
- name: nginx
image: nginx
```

**DNS Records:**
```bash
# Pod-specific DNS
web-0.web-headless.default.svc.cluster.local → 10.244.1.5
web-1.web-headless.default.svc.cluster.local → 10.244.2.6
web-2.web-headless.default.svc.cluster.local → 10.244.3.7

# Service DNS (all pod IPs)
$ nslookup web-headless.default.svc.cluster.local
Name:  web-headless.default.svc.cluster.local
Address: 10.244.1.5
Address: 10.244.2.6
Address: 10.244.3.7
```

### DNS Troubleshooting

**1. DNS Resolution Test (Pod-ból):**
```bash
kubectl run -it --rm debug --image=busybox --restart=Never -- sh

# DNS test
nslookup kubernetes.default.svc.cluster.local
nslookup google.com

# DNS config
cat /etc/resolv.conf
```

**Tipikus `/etc/resolv.conf` (Pod):**
```
nameserver 10.96.0.10    # CoreDNS service IP
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

**2. CoreDNS Logs:**
```bash
kubectl logs -n kube-system -l k8s-app=kube-dns
```

**3. CoreDNS ConfigMap:**
```bash
kubectl get configmap coredns -n kube-system -o yaml
```

**CoreDNS Corefile Példa:**
```
.:53 {
errors
health {
lameduck 5s
}
ready
kubernetes cluster.local in-addr.arpa ip6.arpa {
pods insecure
fallthrough in-addr.arpa ip6.arpa
ttl 30
}
prometheus :9153
forward . /etc/resolv.conf {
max_concurrent 1000
}
cache 30
loop
reload
loadbalance
}
```

---

## Service Mesh

### Mi az a Service Mesh?

**Service Mesh:** Microservice-ek közötti kommunikáció kezelése egy dedikált infrastruktúra réteggel.

**Funkciók:**
- **Traffic Management:** Load balancing, routing, canary deployment
- **Security:** mTLS (mutual TLS) pod-to-pod encryption
- **Observability:** Metrics, tracing, logging
- **Resilience:** Retry, circuit breaking, timeout

**Architektúra:**
```
┌────────────────────────────────────┐
│ Control Plane           │ ← Policy, config, telemetry
│ (Istiod, Linkerd Controller)   │
└────────────────┬───────────────────┘
│ Config push
┌────────────┴─────────────┬──────────────┐
│             │       │
┌───▼────────────────┐  ┌────▼──────────┐ ┌▼────────────┐
│ Pod 1       │  │ Pod 2     │ │ Pod 3    │
│ ┌────────┐     │  │ ┌────────┐  │ │ ┌────────┐ │
│ │App   │     │  │ │App   │  │ │ │App   │ │
│ └───┬────┘     │  │ └───┬────┘  │ │ └───┬────┘ │
│   │       │  │   │     │ │   │    │
│ ┌───▼────────┐  │  │ ┌───▼────────┐│ │ ┌───▼──────┐│
│ │Sidecar   │  │  │ │Sidecar   ││ │ │Sidecar  ││
│ │(Envoy)   │◄───┼───┼►│(Envoy)   ││ │ │(Envoy)  ││
│ └────────────┘  │  │ └────────────┘│ │ └──────────┘│
└───────────────────┘  └───────────────┘ └─────────────┘
```

### Istio (Legelterjedtebb)

**Komponensek:**
- **Istiod:** Control plane (pilot, citadel, galley egybeépítve)
- **Envoy Sidecar:** Data plane (minden pod-ban)
- **Ingress Gateway:** External traffic kezelés

**Sidecar Injection:**
```bash
# Namespace label
kubectl label namespace production istio-injection=enabled

# Pod-ok újraindítása után automatikusan injektálódik a sidecar
kubectl rollout restart deployment -n production
```

**Virtual Service (Traffic Routing):**
```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
name: reviews
spec:
hosts:
- reviews
http:
- match:
- headers:
end-user:
exact: jason
route:
- destination:
host: reviews
subset: v2
- route:
- destination:
host: reviews
subset: v1
weight: 90
- destination:
host: reviews
subset: v2
weight: 10 # Canary: 10% traffic to v2
```

**Destination Rule (Load Balancing, Subsets):**
```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
name: reviews
spec:
host: reviews
trafficPolicy:
loadBalancer:
simple: LEAST_REQUEST
subsets:
- name: v1
labels:
version: v1
- name: v2
labels:
version: v2
```

**mTLS Policy:**
```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
name: default
namespace: production
spec:
mtls:
mode: STRICT # Enforce mTLS for all traffic
```

### Linkerd (Lightweight Alternative)

**Előnyök:**
- Kisebb resource footprint (Linkerd2-proxy, nem Envoy)
- Egyszerűbb konfiguráció
- Gyorsabb startup

**Telepítés:**
```bash
# CLI telepítés
curl -sL https://run.linkerd.io/install | sh

# Cluster telepítés
linkerd install | kubectl apply -f -

# Sidecar injection
kubectl get deploy -o yaml | linkerd inject - | kubectl apply -f -
```

**Service Profile (Retry, Timeout):**
```yaml
apiVersion: linkerd.io/v1alpha2
kind: ServiceProfile
metadata:
name: backend-service.production.svc.cluster.local
namespace: production
spec:
routes:
- name: GET /api/users
condition:
method: GET
pathRegex: /api/users
timeout: 1s
retryBudget:
retryRatio: 0.2
minRetriesPerSecond: 10
ttl: 10s
```

---

## OpenShift Specifikus Networking

### OpenShift SDN (Legacy, Deprecated)

**3 Mód (OpenShift 3.x - 4.11):**
1. **ovs-subnet:** Flat network, nincs namespace isolation
2. **ovs-multitenant:** Namespace isolation (Network Policy előtti megoldás)
3. **ovs-networkpolicy:** Network Policy support

**Státusz:** Deprecated OpenShift 4.12-től, OVN-Kubernetes az új default.

### OVN-Kubernetes (Default OpenShift 4.12+)

*Lásd fentebb a CNI szekcióban.*

**OpenShift-Specific Funkciók:**
- **EgressIP:** Statikus forrás IP pod egress forgalmához
- **EgressFirewall:** Namespace-based egress szabályok
- **Multi-homing:** Több network interfész pod-oknak (Multus)

#### EgressIP Példa

**Probléma:** Pod-ok random node IP-t használnak egress forgalomhoz → external firewall nem tudja white-list-elni.

**Megoldás:** EgressIP - statikus IP címet allokál namespace-hez.

```yaml
apiVersion: k8s.ovn.org/v1
kind: EgressIP
metadata:
name: production-egress
spec:
egressIPs:
- 192.168.100.50 # Static egress IP
namespaceSelector:
matchLabels:
env: production
podSelector:
matchLabels:
tier: backend
```

**Működés:**
```
Backend pod (namespace: production)
└─> Egress traffic
└─> Source IP: 192.168.100.50 (static)
└─> External API (firewall white-list: 192.168.100.50)
```

#### EgressFirewall Példa

**Funkció:** Namespace-level egress szabályok (hová mehetnek ki a pod-ok).

```yaml
apiVersion: k8s.ovn.org/v1
kind: EgressFirewall
metadata:
name: production-egress-firewall
namespace: production
spec:
egress:
- type: Allow
to:
cidrSelector: 10.0.0.0/8 # Corporate network
- type: Allow
to:
dnsName: api.example.com
- type: Deny
to:
cidrSelector: 0.0.0.0/0 # Deny all other egress
```

### Multus (Multiple Network Interfaces)

**Funkció:** Több CNI plugin használata egyidejűleg → pod-oknak több network interfész.

**Használati Eset:**
- Management network + Data network szeparálása
- SR-IOV high-performance networking
- Legacy network integration

**NetworkAttachmentDefinition:**
```yaml
apiVersion: k8s.cni.cncf.io/v1
kind: NetworkAttachmentDefinition
metadata:
name: macvlan-conf
spec:
config: '{
"cniVersion": "0.3.1",
"type": "macvlan",
"master": "eth1",
"mode": "bridge",
"ipam": {
"type": "host-local",
"subnet": "192.168.2.0/24",
"rangeStart": "192.168.2.100",
"rangeEnd": "192.168.2.200"
}
}'
```

**Pod Annotation:**
```yaml
apiVersion: v1
kind: Pod
metadata:
name: multi-nic-pod
annotations:
k8s.v1.cni.cncf.io/networks: macvlan-conf
spec:
containers:
- name: app
image: myapp:latest
```

**Pod-on belül:**
```bash
$ ip addr show
1: lo: <LOOPBACK,UP> ...
2: eth0@if10: <BROADCAST,UP> ... # Default CNI (OVN-Kubernetes)
inet 10.244.1.5/24
3: net1@if15: <BROADCAST,UP> ... # Multus secondary (macvlan)
inet 192.168.2.100/24
```

---

## Troubleshooting

### Pod Connectivity Issues

**1. Pod nem éri el másik Pod-ot**

```bash
# 1. Van-e IP cím?
kubectl exec -it pod-a -- ip addr show

# 2. Ping test (ha ICMP engedélyezett)
kubectl exec -it pod-a -- ping <pod-b-ip>

# 3. DNS resolution
kubectl exec -it pod-a -- nslookup backend-service

# 4. Port test
kubectl exec -it pod-a -- telnet backend-service 8080
kubectl exec -it pod-a -- curl -v http://backend-service:8080

# 5. Network Policy check
kubectl get networkpolicy -n <namespace>
kubectl describe networkpolicy <policy-name>

# 6. CNI plugin logs (node-on)
# Calico
kubectl logs -n kube-system -l k8s-app=calico-node
# OVN-Kubernetes
kubectl logs -n openshift-ovn-kubernetes -l app=ovnkube-node
```

**2. Service nem válaszol**

```bash
# 1. Service létezik?
kubectl get svc backend-service

# 2. Endpoints léteznek? (pod-ok ready)
kubectl get endpoints backend-service

# 3. Service selector helyes?
kubectl get svc backend-service -o yaml | grep selector
kubectl get pods --show-labels | grep <selector>

# 4. Pod ready?
kubectl get pods -l <selector>
kubectl describe pod <pod-name> # Readiness probe?

# 5. kube-proxy működik?
kubectl logs -n kube-system -l k8s-app=kube-proxy
```

**3. Ingress/Route nem működik**

```bash
# 1. Ingress/Route létezik?
kubectl get ingress
oc get route # OpenShift

# 2. Ingress controller fut?
kubectl get pods -n ingress-nginx # NGINX
oc get pods -n openshift-ingress  # OpenShift Router

# 3. TLS certificate valid?
openssl s_client -connect app.example.com:443 -servername app.example.com

# 4. DNS resolution helyes?
nslookup app.example.com

# 5. Backend service elérhető?
kubectl exec -it <ingress-controller-pod> -- curl http://backend-service:8080
```

### DNS Issues

```bash
# 1. CoreDNS fut?
kubectl get pods -n kube-system -l k8s-app=kube-dns

# 2. CoreDNS logs
kubectl logs -n kube-system -l k8s-app=kube-dns

# 3. DNS test pod-ból
kubectl run -it --rm debug --image=busybox --restart=Never -- sh
nslookup kubernetes.default.svc.cluster.local
nslookup google.com

# 4. CoreDNS service IP
kubectl get svc -n kube-system kube-dns

# 5. Pod /etc/resolv.conf
kubectl exec -it <pod> -- cat /etc/resolv.conf
```

### Network Policy Debugging

```bash
# 1. Van-e policy a namespace-ben?
kubectl get networkpolicy -n <namespace>

# 2. Policy részletei
kubectl describe networkpolicy <policy-name>

# 3. Calico policy (ha Calico CNI)
kubectl get caliconetworkpolicy -A
kubectl get globalnetworkpolicy

# 4. Test connectivity (before/after policy)
kubectl exec -it pod-a -- curl http://pod-b:8080

# 5. Logs (CNI-specifikus)
# Calico
kubectl logs -n kube-system -l k8s-app=calico-kube-controllers
```

### Performance Issues

**1. High Latency**

```bash
# 1. Pod-to-Pod latency
kubectl exec -it pod-a -- ping <pod-b-ip>

# 2. Node-to-Node latency
ping <node-ip>

# 3. MTU issues (packet fragmentation)
kubectl exec -it pod-a -- ping -M do -s 1450 <pod-b-ip>

# 4. CNI overhead check
# Direct node-to-node vs. pod-to-pod comparison
```

**2. Low Throughput**

```bash
# iperf3 bandwidth test
kubectl run iperf-server --image=networkstatic/iperf3 -- -s
kubectl run -it iperf-client --rm --image=networkstatic/iperf3 -- -c <server-ip>

# Node-level network stats
ip -s link show eth0
```

### Packet Capture

**tcpdump a Node-on:**
```bash
# CNI interface listázás
ip link show | grep cali # Calico
ip link show | grep veth # Generic veth pairs

# Specifikus pod traffic capture
sudo tcpdump -i <cali-interface> -w /tmp/pod-traffic.pcap

# Pod IP alapján filter
sudo tcpdump -i any host 10.244.1.5 -w /tmp/pod-traffic.pcap
```

**tcpdump Pod-ban (privileged):**
```yaml
apiVersion: v1
kind: Pod
metadata:
name: debug-pod
spec:
hostNetwork: true # Use host network namespace
containers:
- name: debug
image: nicolaka/netshoot
command: ["/bin/bash"]
stdin: true
tty: true
securityContext:
privileged: true
```

```bash
kubectl exec -it debug-pod -- bash
tcpdump -i any port 8080 -w /tmp/capture.pcap
```

---

## Hasznos Parancsok Összefoglalója

### Networking Info

```bash
# Node networking
kubectl get nodes -o wide
kubectl describe node <node-name> | grep -A5 Addresses

# Pod networking
kubectl get pods -o wide
kubectl get pods -o custom-columns=NAME:.metadata.name,IP:.status.podIP,NODE:.spec.nodeName

# Service networking
kubectl get svc -A
kubectl get endpoints <service-name>

# Ingress/Routes
kubectl get ingress -A
oc get routes -A # OpenShift
```

### CNI Plugin Info

```bash
# Calico
kubectl get ippools
kubectl get felixconfiguration
kubectl get bgpconfigurations

# OVN-Kubernetes (OpenShift)
oc get network.operator cluster -o yaml
oc get clusternetwork

# Cilium
kubectl get ciliumnodes
kubectl get ciliumnetworkpolicies
```

### Network Policy

```bash
# List policies
kubectl get networkpolicy -A

# Describe policy
kubectl describe networkpolicy <policy-name> -n <namespace>

# Test connectivity (dry-run)
kubectl auth can-i create pods --as=system:serviceaccount:<namespace>:<serviceaccount>
```

---

## Összefoglalás és Következő Lépések

### Kulcs Koncepciók Recap

1. **Pod Networking:** Minden pod saját IP, CNI plugin menedzseli
2. **Services:** L4 load balancing (ClusterIP, NodePort, LoadBalancer)
3. **Ingress/Routes:** L7 HTTP/HTTPS routing
4. **Network Policies:** Pod-to-Pod firewall szabályok
5. **DNS:** Automatikus service discovery
6. **Service Mesh:** Advanced traffic management, security, observability
7. **CNI Plugins:** Calico (BGP/VXLAN), OVN-Kubernetes (Geneve), Cilium (eBPF)

### Ajánlott Gyakorlati Feladatok

1. **Deploy egy multi-tier alkalmazást:**
- Frontend (ClusterIP Service)
- Backend (ClusterIP Service)
- Database (Headless Service, StatefulSet)
- Ingress/Route külső hozzáféréshez

2. **Network Policy gyakorlás:**
- Namespace isolation
- Frontend → Backend allowed
- Backend → Database allowed
- Database deny all ingress (except backend)

3. **Service Mesh kipróbálása:**
- Istio vagy Linkerd telepítése
- Canary deployment (10% v2, 90% v1)
- mTLS engedélyezése
- Metrics és tracing megfigyelése

4. **Troubleshooting szimuláció:**
- DNS issue: CoreDNS pod leállítása
- Network Policy: Deny all policy létrehozása
- Service issue: Selector nem egyezik pod label-ekkel
- Ingress issue: TLS certificate lejárt

---

**Verzió:** 1.0 
**Utolsó Frissítés:** 2026-04-24 
**Készítette:** Claude Code (Anthropic)
