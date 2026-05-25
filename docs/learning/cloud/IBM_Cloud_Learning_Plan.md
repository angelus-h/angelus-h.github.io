---
description: IBM Cloud quick reference - Virtual Servers, Object Storage, Kubernetes, Watson AI, hybrid cloud, OpenShift, and enterprise integration.
---

# IBM Cloud Quick Reference

## Goals

1. **Understanding IBM Cloud platform** and hybrid cloud approach
2. **Using key services** (Compute, Storage, Kubernetes, Watson AI)
3. **Leveraging Free Tier** (Lite plan + $200 credit for 30 days)
4. **Preparing for enterprise hybrid cloud** (on-prem + cloud)
5. **Understanding Company OpenShift** on IBM Cloud

---

## Prerequisites

- IBM Cloud account: https://cloud.ibm.com/registration
- IBM Cloud CLI installed
- Basic Linux/bash knowledge
- Credit card (for trial credit, won't charge on Lite plan)
- Understanding of Kubernetes basics (helpful for OpenShift)

---

## 4-WEEK LEARNING PLAN

### Week 1: FUNDAMENTALS + COMPUTE + NETWORKING

#### Day 1-2: IBM Cloud Console + IAM + CLI

**Why important:** Identity management and resource organization. IBM Cloud uses Resource Groups and Access Groups.

**Exercises:**
```
 Create IBM Cloud account
 Understand Resource Groups
 Create Access Groups
 Assign IAM policies
 Install IBM Cloud CLI
 Install CLI plugins (container-service, kubernetes-service)
 Create Service ID (machine identity)
 Generate API keys
```

**Hands-on:**

1. **Install IBM Cloud CLI:**
 ```bash
 # Linux
 curl -fsSL https://clis.cloud.ibm.com/install/linux | sh

 # Verify
 ibmcloud --version

 # Login
 ibmcloud login
 ibmcloud login --sso # For federated ID

 # Target region
 ibmcloud target -r us-south
 ```

2. **Install plugins:**
 ```bash
 # Kubernetes service plugin
 ibmcloud plugin install container-service

 # Container registry plugin
 ibmcloud plugin install container-registry

 # List plugins
 ibmcloud plugin list
 ```

3. **Create Resource Group:**
 ```bash
 # List resource groups
 ibmcloud resource groups

 # Create new resource group
 ibmcloud resource group-create my-learning-rg

 # Target resource group
 ibmcloud target -g my-learning-rg
 ```

4. **Create Service ID (for automation):**
 ```bash
 # Create Service ID
 ibmcloud iam service-id-create my-service-id \
 --description "Service ID for automation"

 # Create API key for Service ID
 ibmcloud iam service-api-key-create my-api-key my-service-id \
 --description "API key for CI/CD"

 # Assign policy
 ibmcloud iam service-policy-create my-service-id \
 --roles Editor \
 --resource-group-name my-learning-rg
 ```

**Key concepts:**
- **Resource Group**: Logical container for resources (billing, access)
- **Access Group**: Collection of users/service IDs with same access
- **Service ID**: Machine identity (for apps, automation)
- **IAM Policy**: Permissions (Platform roles, Service roles)
- **API Key**: Programmatic authentication

**IBM Cloud IAM concepts used in enterprises:**
- Trusted profiles (workload identity for Kubernetes)
- Context-based restrictions (CBR)
- Enterprise account structure
- Access tags

---

#### Day 3-5: Virtual Server (Classic & VPC)

**Why important:** IBM Cloud offers both Classic and VPC-based virtual servers.

**Exercises:**
```
 Create VPC
 Create subnet
 Create security group
 Launch Virtual Server for VPC (VSI)
 SSH into instance
 Attach block storage
 Create floating IP
 Configure user data script
```

**Hands-on Project: Web Server on VPC**

1. **Create VPC and subnet:**
 ```bash
 # Install VPC plugin
 ibmcloud plugin install vpc-infrastructure

 # Create VPC
 ibmcloud is vpc-create my-vpc

 # Get VPC ID
 VPC_ID=$(ibmcloud is vpcs --output json | jq -r '.[] | select(.name=="my-vpc") | .id')

 # Create subnet
 ibmcloud is subnet-create my-subnet $VPC_ID \
 --zone us-south-1 \
 --ipv4-cidr-block 10.240.0.0/24
 ```

2. **Create security group:**
 ```bash
 # Create security group
 ibmcloud is security-group-create my-sg $VPC_ID

 # Get security group ID
 SG_ID=$(ibmcloud is security-groups --output json | jq -r '.[] | select(.name=="my-sg") | .id')

 # Allow SSH
 ibmcloud is security-group-rule-add $SG_ID inbound tcp \
 --port-min 22 --port-max 22

 # Allow HTTP
 ibmcloud is security-group-rule-add $SG_ID inbound tcp \
 --port-min 80 --port-max 80
 ```

3. **Create SSH key:**
 ```bash
 # Generate key
 ssh-keygen -t rsa -b 4096 -f ~/.ssh/ibm_cloud_key

 # Add to IBM Cloud
 ibmcloud is key-create my-key @~/.ssh/ibm_cloud_key.pub
 ```

4. **Launch Virtual Server:**
 ```bash
 # Get subnet ID
 SUBNET_ID=$(ibmcloud is subnets --output json | jq -r '.[] | select(.name=="my-subnet") | .id')

 # Get key ID
 KEY_ID=$(ibmcloud is keys --output json | jq -r '.[] | select(.name=="my-key") | .id')

 # Create instance
 ibmcloud is instance-create web-server \
 $VPC_ID us-south-1 cx2-2x4 $SUBNET_ID \
 --image ibm-ubuntu-22-04-minimal-amd64-1 \
 --keys $KEY_ID \
 --security-groups $SG_ID \
 --user-data @startup.sh
 ```

5. **Startup script (startup.sh):**
 ```bash
 #!/bin/bash
 apt-get update
 apt-get install -y nginx
 echo "<h1>Hello from IBM Cloud VPC!</h1>" > /var/www/html/index.html
 systemctl start nginx
 systemctl enable nginx
 ```

6. **Create floating IP:**
 ```bash
 # Get instance ID
 INSTANCE_ID=$(ibmcloud is instances --output json | jq -r '.[] | select(.name=="web-server") | .id')

 # Reserve floating IP
 ibmcloud is floating-ip-reserve my-floating-ip \
 --nic primary --in $INSTANCE_ID
 ```

7. **SSH and test:**
 ```bash
 # Get floating IP
 FLOATING_IP=$(ibmcloud is floating-ips --output json | jq -r '.[] | select(.name=="my-floating-ip") | .address')

 # SSH
 ssh -i ~/.ssh/ibm_cloud_key root@$FLOATING_IP

 # Test web server
 curl http://$FLOATING_IP
 ```

**Key concepts:**
- **VPC**: Isolated virtual network
- **Subnet**: IP range within VPC
- **Security Group**: Stateful firewall
- **VSI**: Virtual Server Instance
- **Floating IP**: Public IP address
- **Profile**: Instance size (cx2-2x4 = 2 vCPU, 4 GB RAM)

**IBM Cloud compute concepts used in enterprises:**
- Auto Scale for VPC
- Placement groups (affinity/anti-affinity)
- Dedicated hosts
- Bare metal servers

---

#### Day 6-7: Networking (VPC, Load Balancer, VPN)

**Why important:** Enterprise-grade networking for hybrid cloud.

**Exercises:**
```
 Create Load Balancer for VPC
 Configure backend pool
 Health checks
 Create VPN Gateway
 VPC peering (transit gateway)
 Public Gateway (for private instances)
```

**Hands-on: Load Balancer Setup**

```bash
# Get subnet ID
SUBNET_ID=$(ibmcloud is subnets --output json | jq -r '.[] | select(.name=="my-subnet") | .id')

# Create load balancer
ibmcloud is load-balancer-create my-lb public \
 --subnet $SUBNET_ID

# Get LB ID
LB_ID=$(ibmcloud is load-balancers --output json | jq -r '.[] | select(.name=="my-lb") | .id')

# Create backend pool
ibmcloud is load-balancer-pool-create my-pool $LB_ID \
 round_robin http 15 2 5 http

# Get pool ID
POOL_ID=$(ibmcloud is load-balancer-pools $LB_ID --output json | jq -r '.[] | select(.name=="my-pool") | .id')

# Add instance to pool
ibmcloud is load-balancer-pool-member-create $LB_ID $POOL_ID \
 80 $INSTANCE_ID

# Create listener
ibmcloud is load-balancer-listener-create $LB_ID \
 80 http --default-pool $POOL_ID
```

**Key concepts:**
- **Load Balancer**: Public or private load balancing
- **Backend Pool**: Collection of instances
- **Health Check**: Monitor instance health
- **VPN Gateway**: Site-to-site VPN
- **Transit Gateway**: Multi-VPC connectivity

---

### Week 2: STORAGE + DATABASE + CONTAINERS

#### Day 1-2: Cloud Object Storage (COS)

**Why important:** S3-compatible object storage, used for backups, archives, analytics.

**Exercises:**
```
 Create COS instance
 Create bucket
 Upload/download objects
 Configure lifecycle policies
 Enable versioning
 Set bucket access policies
 Generate presigned URLs
```

**Hands-on: Static Website on COS**

1. **Create COS instance:**
 ```bash
 # Create service instance
 ibmcloud resource service-instance-create my-cos \
 cloud-object-storage standard global
 ```

2. **Create bucket:**
 ```bash
 # Install COS plugin
 ibmcloud plugin install cloud-object-storage

 # Create bucket
 ibmcloud cos bucket-create --bucket my-static-website \
 --ibm-service-instance-id $(ibmcloud resource service-instance my-cos --output json | jq -r '.[0].guid')
 ```

3. **Upload files:**
 ```bash
 echo "<h1>Hello from IBM Cloud Object Storage!</h1>" > index.html

 # Upload
 ibmcloud cos object-put --bucket my-static-website \
 --key index.html --body index.html
 ```

4. **Make bucket public:**
 ```bash
 # Set bucket policy (via UI or API)
 # Enable public access and static website hosting
 ```

5. **Configure static website:**
 - Navigate to bucket in IBM Cloud Console
 - Configuration → Static website hosting
 - Set index.html as index document

**Key concepts:**
- **COS**: S3-compatible object storage
- **Storage Classes**: Standard, Vault, Cold Vault, Flex
- **Bucket**: Container for objects
- **HMAC credentials**: S3-compatible access keys
- **Aspera**: High-speed data transfer

**COS concepts used in enterprises:**
- Cross-region replication
- Archive policies
- Immutable object storage (WORM)
- Key Protect integration

---

#### Day 3-4: Databases for IBM Cloud

**Why important:** Managed databases (PostgreSQL, MongoDB, MySQL, Redis, Elasticsearch).

**Exercises:**
```
 Create Databases for PostgreSQL instance
 Configure firewall (allowlists)
 Connect with psql
 Create database and tables
 Configure automated backups
 Scale resources (CPU, RAM, disk)
```

**Hands-on: PostgreSQL Database**

1. **Create database instance:**
 ```bash
 # Create Databases for PostgreSQL
 ibmcloud resource service-instance-create my-postgres \
 databases-for-postgresql standard us-south \
 -p '{"members_memory_allocation_mb": "1024",
 "members_disk_allocation_mb": "5120"}'
 ```

2. **Get connection info:**
 ```bash
 # Get credentials
 ibmcloud resource service-key-create my-postgres-key \
 --instance-name my-postgres

 # View credentials
 ibmcloud resource service-key my-postgres-key --output json
 ```

3. **Connect:**
 ```bash
 # Using connection string from credentials
 psql "postgres://ibm_cloud_user:PASSWORD@HOST:PORT/ibmclouddb?sslmode=verify-full"
 ```

4. **Database operations:**
 ```sql
 CREATE TABLE users (
 id SERIAL PRIMARY KEY,
 name VARCHAR(100),
 email VARCHAR(100),
 created_at TIMESTAMP DEFAULT NOW()
 );

 INSERT INTO users (name, email) VALUES
 ('Alice', 'alice@example.com'),
 ('Bob', 'bob@example.com');

 SELECT * FROM users;
 ```

5. **Configure backup:**
 ```bash
 # Backups are automatic (daily)
 # View backup schedule in console
 ibmcloud resource service-instance my-postgres
 ```

**Key concepts:**
- **Databases for PostgreSQL**: Managed PostgreSQL
- **Databases for MongoDB**: Managed MongoDB
- **Databases for Redis**: Managed Redis
- **High Availability**: Multi-zone deployment
- **Point-in-time recovery**: Restore to any point in time

---

#### Day 5-7: Company OpenShift on IBM Cloud

**Why important:** Enterprise Kubernetes platform, IBM's strategic container platform.

**Exercises:**
```
 Create OpenShift cluster
 Access OpenShift web console
 Deploy application from Git (S2I)
 Create route (ingress)
 Deploy using oc CLI
 OpenShift Pipelines (Tekton)
 Understand Operators
```

**Hands-on: Deploy Application on OpenShift**

1. **Create OpenShift cluster:**
 ```bash
 # List available versions
 ibmcloud ks versions --show-version OpenShift

 # Create cluster (can take 20-30 minutes)
 ibmcloud oc cluster create vpc-gen2 \
 --name my-openshift \
 --version 4.14_openshift \
 --zone us-south-1 \
 --flavor bx2.4x16 \
 --workers 2 \
 --vpc-id $VPC_ID \
 --subnet-id $SUBNET_ID
 ```

2. **Get cluster config:**
 ```bash
 # Download cluster config
 ibmcloud oc cluster config --cluster my-openshift --admin

 # Verify
 oc get nodes
 oc get projects
 ```

3. **Create project:**
 ```bash
 oc new-project my-app
 ```

4. **Deploy from Git (Source-to-Image):**
 ```bash
 # Deploy Node.js app from GitHub
 oc new-app https://github.com/sclorg/nodejs-ex.git \
 --name=nodejs-app

 # Expose service
 oc expose svc/nodejs-app

 # Get route
 oc get route nodejs-app
 ```

5. **Deploy using YAML:**
 ```yaml
 # deployment.yaml
 apiVersion: apps/v1
 kind: Deployment
 metadata:
 name: nginx
 spec:
 replicas: 3
 selector:
 matchLabels:
 app: nginx
 template:
 metadata:
 labels:
 app: nginx
 spec:
 containers:
 - name: nginx
 image: nginx:latest
 ports:
 - containerPort: 80
 ---
 apiVersion: v1
 kind: Service
 metadata:
 name: nginx
 spec:
 selector:
 app: nginx
 ports:
 - protocol: TCP
 port: 80
 targetPort: 80
 ---
 apiVersion: route.openshift.io/v1
 kind: Route
 metadata:
 name: nginx
 spec:
 to:
 kind: Service
 name: nginx
 port:
 targetPort: 80
 ```

6. **Apply and access:**
 ```bash
 oc apply -f deployment.yaml
 oc get route nginx
 curl http://<ROUTE_URL>
 ```

**Key concepts:**
- **OpenShift**: Enterprise Kubernetes distribution by Company
- **Project**: Kubernetes namespace with additional security
- **Route**: OpenShift-specific ingress (easier than Ingress)
- **S2I (Source-to-Image)**: Build container from source code
- **Operator**: Kubernetes application management pattern
- **OperatorHub**: Marketplace for Operators

**OpenShift concepts used in enterprises:**
- OpenShift Pipelines (Tekton CI/CD)
- OpenShift GitOps (ArgoCD)
- OpenShift Service Mesh (Istio)
- OpenShift Serverless (Knative)
- Company Quay (container registry)

---

### Week 3: AI/ML + MONITORING + SECURITY

#### Day 1-2: Watson Services (AI/ML)

**Why important:** IBM's differentiator - enterprise AI services.

**Exercises:**
```
 Create Watson Assistant (chatbot)
 Watson Natural Language Understanding (NLU)
 Watson Speech to Text
 Watson Text to Speech
 Watson Machine Learning
```

**Hands-on: Build Chatbot with Watson Assistant**

1. **Create Watson Assistant:**
 ```bash
 ibmcloud resource service-instance-create my-assistant \
 conversation lite us-south
 ```

2. **Access Watson Assistant UI:**
 - Navigate to IBM Cloud Console → Watson Assistant
 - Create skill (Dialog skill)
 - Add intents, entities, dialog flows

3. **Example API usage (Python):**
 ```python
 from ibm_watson import AssistantV2
 from ibm_cloud_sdk_core.authenticators import IAMAuthenticator

 authenticator = IAMAuthenticator('YOUR_API_KEY')
 assistant = AssistantV2(
 version='2021-11-27',
 authenticator=authenticator
 )
 assistant.set_service_url('YOUR_SERVICE_URL')

 # Create session
 session = assistant.create_session(
 assistant_id='YOUR_ASSISTANT_ID'
 ).get_result()

 # Send message
 response = assistant.message(
 assistant_id='YOUR_ASSISTANT_ID',
 session_id=session['session_id'],
 input={
 'message_type': 'text',
 'text': 'Hello'
 }
 ).get_result()

 print(response['output']['generic'][0]['text'])
 ```

**Watson NLU Example:**
```python
from ibm_watson import NaturalLanguageUnderstandingV1
from ibm_watson.natural_language_understanding_v1 import Features, EntitiesOptions, KeywordsOptions

nlu = NaturalLanguageUnderstandingV1(
 version='2022-04-07',
 authenticator=IAMAuthenticator('YOUR_API_KEY')
)
nlu.set_service_url('YOUR_SERVICE_URL')

response = nlu.analyze(
 text='IBM is an American multinational technology company.',
 features=Features(entities=EntitiesOptions(), keywords=KeywordsOptions())
).get_result()

print(response)
```

**Key concepts:**
- **Watson Assistant**: Conversational AI (chatbots)
- **Watson NLU**: Extract entities, keywords, sentiment
- **Watson Discovery**: AI-powered search
- **Watson Studio**: Data science workbench
- **Watson Machine Learning**: Deploy ML models

---

#### Day 3-4: Monitoring & Logging (IBM Cloud Monitoring, Log Analysis)

**Why important:** Observability for IBM Cloud resources.

**Exercises:**
```
 Create IBM Cloud Monitoring instance
 Configure monitoring agent
 View metrics and dashboards
 Create alerts
 Create Log Analysis instance
 Stream logs from applications
```

**Hands-on: Monitoring Setup**

1. **Create monitoring instance:**
 ```bash
 ibmcloud resource service-instance-create my-monitoring \
 sysdig-monitor graduated-tier us-south
 ```

2. **Install monitoring agent on VSI:**
 ```bash
 # Get access key from IBM Cloud Console
 curl -sL https://ibm.biz/install-sysdig-agent | \
 sudo bash -s -- --access_key YOUR_ACCESS_KEY \
 --collector ingest.us-south.monitoring.cloud.ibm.com
 ```

3. **View metrics:**
 - Navigate to IBM Cloud Console → Monitoring
 - Open Sysdig dashboard
 - View system metrics (CPU, memory, disk, network)

4. **Create alert:**
 - Sysdig UI → Alerts
 - Condition: CPU > 80% for 5 minutes
 - Notification: Email/Slack

**Log Analysis:**
```bash
# Create Log Analysis instance
ibmcloud resource service-instance-create my-logging \
 logdna standard us-south

# Install logging agent
curl -sL https://ibm.biz/install-logdna-agent | \
 sudo bash -s -- -k YOUR_INGESTION_KEY
```

**Key concepts:**
- **IBM Cloud Monitoring**: Powered by Sysdig
- **IBM Cloud Log Analysis**: Powered by LogDNA
- **Platform metrics**: Automatic metrics for IBM Cloud services
- **Activity Tracker**: Audit logs (who did what)

---

#### Day 5-7: Security (Secrets Manager, Key Protect, Security & Compliance)

**Why important:** Enterprise-grade security and compliance.

**Exercises:**
```
 Create Secrets Manager instance
 Store secrets (API keys, certificates)
 Create Key Protect instance
 Generate encryption keys
 Encrypt Cloud Object Storage with KYOK
 Enable Activity Tracker
 Security and Compliance Center
```

**Hands-on: Secrets Management**

1. **Create Secrets Manager:**
 ```bash
 ibmcloud resource service-instance-create my-secrets \
 secrets-manager trial us-south
 ```

2. **Store secret:**
 ```bash
 # Using IBM Cloud Console or API
 # Create arbitrary secret (API key, password, etc.)
 ```

3. **Create Key Protect instance:**
 ```bash
 ibmcloud resource service-instance-create my-kp \
 kms tiered-pricing us-south
 ```

4. **Create root key:**
 ```bash
 # Install Key Protect plugin
 ibmcloud plugin install key-protect

 # Create root key
 ibmcloud kp key create my-root-key \
 --instance-id $(ibmcloud resource service-instance my-kp --output json | jq -r '.[0].guid')
 ```

5. **Encrypt COS bucket:**
 - Navigate to COS bucket
 - Encryption → Key Protect
 - Select root key

**Key concepts:**
- **Secrets Manager**: Store and manage secrets
- **Key Protect**: Hardware Security Module (HSM) backed encryption
- **Hyper Protect Crypto Services**: FIPS 140-2 Level 4 certified HSM
- **Activity Tracker**: Audit trail (SIEM integration)
- **Security and Compliance Center**: Compliance posture management

---

### Week 4: SERVERLESS + CI/CD + HYBRID CLOUD

#### Day 1-2: Code Engine (Serverless Containers)

**Why important:** Run containers or code without managing servers.

**Exercises:**
```
 Create Code Engine project
 Deploy container image
 Deploy from source code (buildpack)
 Configure autoscaling
 Create jobs (batch processing)
 Event subscriptions (COS, Kafka)
```

**Hands-on: Deploy API on Code Engine**

1. **Create project:**
 ```bash
 # Install Code Engine plugin
 ibmcloud plugin install code-engine

 # Create project
 ibmcloud ce project create --name my-project
 ibmcloud ce project select --name my-project
 ```

2. **Deploy from container image:**
 ```bash
 ibmcloud ce application create \
 --name my-api \
 --image icr.io/codeengine/hello \
 --min-scale 0 \
 --max-scale 10
 ```

3. **Deploy from source code:**
 ```bash
 # From GitHub
 ibmcloud ce application create \
 --name nodejs-app \
 --build-source https://github.com/IBM/code-engine-node-app \
 --strategy buildpacks
 ```

4. **Get URL:**
 ```bash
 ibmcloud ce application get --name my-api
 curl https://<APP_URL>
 ```

5. **Create job (batch):**
 ```bash
 ibmcloud ce job create \
 --name batch-job \
 --image icr.io/codeengine/cron \
 --array-indices 1-5

 # Run job
 ibmcloud ce jobrun submit --job batch-job
 ```

**Key concepts:**
- **Code Engine**: Serverless containers and code
- **Application**: Long-running HTTP service
- **Job**: Batch/scheduled workload
- **Build**: Source-to-image conversion
- **Subscription**: Event-driven triggers

---

#### Day 3-4: IBM Cloud Continuous Delivery (CI/CD)

**Why important:** Native CI/CD toolchain with DevOps insights.

**Exercises:**
```
 Create toolchain
 Add Git repository
 Configure delivery pipeline
 Deploy to OpenShift
 Add Slack notifications
 DevOps Insights integration
```

**Hands-on: CI/CD Pipeline**

1. **Create toolchain:**
 - Navigate to IBM Cloud Console → DevOps
 - Create toolchain from template (Kubernetes/OpenShift)
 - Connect GitHub repository

2. **Pipeline configuration (.bluemix/pipeline.yml):**
 ```yaml
 stages:
 - name: BUILD
 inputs:
 - type: git
 branch: main
 jobs:
 - name: Build Docker Image
 type: builder
 build_type: cr
 target:
 region_id: ${REGISTRY_REGION_ID}
 api_key: ${API_KEY}
 namespace: ${REGISTRY_NAMESPACE}
 image_name: ${APP_NAME}
 script: |
 docker build -t ${REGISTRY_URL}/${REGISTRY_NAMESPACE}/${APP_NAME}:${BUILD_NUMBER} .
 docker push ${REGISTRY_URL}/${REGISTRY_NAMESPACE}/${APP_NAME}:${BUILD_NUMBER}

 - name: DEPLOY
 inputs:
 - type: job
 stage: BUILD
 job: Build Docker Image
 jobs:
 - name: Deploy to OpenShift
 type: deployer
 target:
 region_id: ${PROD_REGION_ID}
 api_key: ${API_KEY}
 kubernetes_cluster: ${PROD_CLUSTER_NAME}
 script: |
 oc apply -f deployment.yaml
 oc set image deployment/${APP_NAME} ${APP_NAME}=${REGISTRY_URL}/${REGISTRY_NAMESPACE}/${APP_NAME}:${BUILD_NUMBER}
 ```

**Key concepts:**
- **Toolchain**: Integrated DevOps tools
- **Delivery Pipeline**: CI/CD automation
- **DevOps Insights**: Quality gates and risk analysis
- **Tekton Pipelines**: Kubernetes-native CI/CD

---

#### Day 5-7: Satellite & Hybrid Cloud

**Why important:** Extend IBM Cloud to on-premises and edge locations.

**Exercises:**
```
 Understand Satellite architecture
 Create Satellite location (demo/simulation)
 Deploy Satellite-enabled services
 Understand Company Advanced Cluster Management
 Multi-cluster management
```

**Satellite Concepts:**

```
 IBM Cloud (Control Plane)
 |
 +--------------------|--------------------+
 | | |
 Satellite Satellite Satellite
 Location 1 Location 2 Location 3
 (On-prem DC) (Edge) (Other Cloud)
 | | |
 OpenShift OpenShift OpenShift
 + IBM Services + IBM Services + IBM Services
```

**Key concepts:**
- **Satellite**: Bring IBM Cloud services anywhere
- **Satellite Location**: On-prem, edge, or other cloud
- **Link**: Secure connection to IBM Cloud
- **Satellite-enabled services**: COS, Databases, OpenShift

---

## IBM Cloud Services Priority (For Enterprise)

| Priority | Service | Why important |
|----------|---------|---------------|
| ** HIGH** | **Company OpenShift** | Container platform |
| ** HIGH** | **VPC** | Networking |
| ** HIGH** | **Cloud Object Storage** | Object storage |
| ** HIGH** | **IAM** | Access management |
| ** MEDIUM** | **Databases for PostgreSQL** | Managed databases |
| ** MEDIUM** | **Code Engine** | Serverless |
| ** MEDIUM** | **Secrets Manager** | Secrets management |
| ** MEDIUM** | **Monitoring & Logging** | Observability |
| ** LOW** | **Watson Services** | AI/ML (if needed) |
| ** LOW** | **Satellite** | Hybrid cloud (if needed) |

---

## Learning Resources

**Official IBM Cloud:**
- IBM Cloud Docs: https://cloud.ibm.com/docs
- IBM Cloud Learning: https://www.ibm.com/training/cloud
- IBM Cloud Architecture Center: https://www.ibm.com/cloud/architecture
- Free Tier: https://www.ibm.com/cloud/free

**Hands-on Labs:**
- IBM Developer: https://developer.ibm.com/
- IBM Cloud Code Patterns: https://developer.ibm.com/patterns/
- OpenShift Learning Portal: https://learn.openshift.com/

**YouTube:**
- IBM Technology
- IBM Cloud
- OpenShift (Company)

---

## COST MANAGEMENT

### Free Tier (Lite Plan):

| Service | Free Tier | Duration |
|---------|-----------|----------|
| **Trial Credit** | $200 credit | 30 days |
| **Cloud Object Storage** | 25 GB storage | Always Free |
| **Code Engine** | 100K vCPU-seconds/month | Always Free |
| **Databases for PostgreSQL** | Lite plan available | Trial period |
| **Watson Assistant** | Lite plan (10K messages) | Always Free |
| **Virtual Server (Classic)** | Limited instances | Trial credit |

### Cost Protection:

1. **Set spending notifications:**
 - Navigate to Manage → Billing and usage → Spending notifications
 - Set threshold (e.g., $50)

2. **Monitor usage:**
 - Billing and usage → Usage
 - View by service and resource group

3. **Clean up resources:**
 ```bash
 # Delete OpenShift cluster
 ibmcloud oc cluster rm --cluster my-openshift -f

 # Delete VPC instances
 ibmcloud is instance-delete web-server -f

 # Delete COS bucket
 ibmcloud cos bucket-delete --bucket my-bucket

 # Delete database
 ibmcloud resource service-instance-delete my-postgres -f
 ```

---

## Weekly Checklist

### Week 1:
- [ ] IBM Cloud account created
- [ ] IBM Cloud CLI installed
- [ ] Resource group created
- [ ] VPC and subnet created
- [ ] Virtual Server launched
- [ ] Spending notification set

### Week 2:
- [ ] Cloud Object Storage bucket created
- [ ] Database instance created
- [ ] OpenShift cluster created
- [ ] Application deployed on OpenShift

### Week 3:
- [ ] Watson service tested
- [ ] Monitoring configured
- [ ] Secrets Manager secret created
- [ ] Key Protect key created

### Week 4:
- [ ] Code Engine application deployed
- [ ] CI/CD toolchain created
- [ ] Satellite concepts understood
- [ ] Cleanup completed

---

**Ready?** Start with **Week 1, Day 1-2: IAM + CLI**! 
