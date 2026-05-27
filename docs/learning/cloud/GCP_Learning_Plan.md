---
description: GCP quick reference - Compute Engine, Cloud Storage, GKE, networking, free tier usage, and Terraform integration for Google Cloud infrastructure.
---

# Google Cloud Platform (GCP) Quick Reference

## Goals

1. **Understanding GCP basics** and architecture
2. **Using key services** (Compute, Storage, Networking, Kubernetes)
3. **Leveraging Free Tier** ($300 credit for 90 days + always free tier)
4. **Preparing for corporate GCP environments**
5. **Combining with Terraform** for Infrastructure as Code

---

## Prerequisites

- Google Account (personal or workspace)
- GCP Free Trial: https://cloud.google.com/free
- Google Cloud SDK (`gcloud`) installed
- Basic Linux/bash knowledge
- Credit card (won't charge during trial, can set billing alerts)

---

## 4-WEEK LEARNING PLAN

### Week 1: FUNDAMENTALS + COMPUTE + NETWORKING

#### Day 1-2: GCP Console + IAM + Cloud SDK

**Why important:** Identity and access management is foundational. GCP uses Projects for resource organization.

**Exercises:**
```
Create GCP Project
Understand IAM (Identity and Access Management)
Create Service Account
Assign IAM roles (Owner, Editor, Viewer)
Install and configure gcloud CLI
Enable Billing and set budget alerts
Understand resource hierarchy (Organization → Folder → Project)
```

**Hands-on:**

1. **Create project:**
```bash
# List projects
gcloud projects list

# Create new project
gcloud projects create my-learning-project-12345 \
--name="My Learning Project"

# Set default project
gcloud config set project my-learning-project-12345
```

2. **Service Account (for automation):**
```bash
# Create service account
gcloud iam service-accounts create my-service-account \
--display-name="My Service Account"

# Grant roles
gcloud projects add-iam-policy-binding my-learning-project-12345 \
--member="serviceAccount:my-service-account@my-learning-project-12345.iam.gserviceaccount.com" \
--role="roles/compute.admin"

# Create and download key
gcloud iam service-accounts keys create ~/key.json \
--iam-account=my-service-account@my-learning-project-12345.iam.gserviceaccount.com
```

3. **Verify identity:**
```bash
gcloud auth list
gcloud config list
gcloud compute project-info describe --project=my-learning-project-12345
```

**Key concepts:**
- **Project**: Isolated resource container (billing, quotas, permissions)
- **Service Account**: Machine identity (for applications, automation)
- **IAM Role**: Collection of permissions (Owner, Editor, Viewer, custom)
- **gcloud**: Command-line tool for GCP

**GCP IAM concepts used in companies:**
- Workload Identity (Kubernetes service accounts → GCP service accounts)
- Organization policies
- Least privilege access
- Service account impersonation

---

#### Day 3-5: Compute Engine (GCE) - Virtual Machines

**Why important:** Fundamental compute service, equivalent to AWS EC2.

**Exercises:**
```
Create VM instance (e2-micro, Free Tier eligible)
SSH into instance
Configure firewall rules
Attach persistent disk
Create snapshot
Create custom image
Startup script (metadata)
Instance templates
```

**Hands-on Project: Web Server on Compute Engine**

1. **Create VM instance:**
```bash
gcloud compute instances create web-server \
--zone=us-central1-a \
--machine-type=e2-micro \
--image-family=ubuntu-2204-lts \
--image-project=ubuntu-os-cloud \
--boot-disk-size=10GB \
--tags=http-server,https-server \
--metadata=startup-script='#!/bin/bash
apt-get update
apt-get install -y nginx
echo "<h1>Hello from GCP Compute Engine!</h1>" > /var/www/html/index.html
systemctl start nginx
systemctl enable nginx'
```

2. **Create firewall rule:**
```bash
gcloud compute firewall-rules create allow-http \
--allow=tcp:80 \
--source-ranges=0.0.0.0/0 \
--target-tags=http-server \
--description="Allow HTTP traffic"
```

3. **SSH into instance:**
```bash
gcloud compute ssh web-server --zone=us-central1-a
```

4. **Get external IP and test:**
```bash
# Get IP
gcloud compute instances describe web-server \
--zone=us-central1-a \
--format='get(networkInterfaces[0].accessConfigs[0].natIP)'

# Test
curl http://<EXTERNAL_IP>
```

5. **Create snapshot:**
```bash
gcloud compute disks snapshot web-server \
--zone=us-central1-a \
--snapshot-names=web-server-snapshot-1
```

**Key concepts:**
- **Machine Type**: CPU/RAM configuration (e2-micro = 2 vCPUs, 1 GB RAM)
- **Image**: OS template (Ubuntu, CentOS, Windows)
- **Persistent Disk**: Block storage (standard, SSD)
- **Preemptible VM**: Cheaper, but can be terminated (like AWS Spot)
- **Instance Group**: Collection of VMs (managed or unmanaged)

**GCE concepts used in companies:**
- Managed Instance Groups (MIG) for auto-scaling
- Load balancers (regional, global)
- Live migration (zero downtime maintenance)
- Custom machine types

---

#### Day 6-7: VPC (Virtual Private Cloud) + Networking

**Why important:** Network isolation, critical for production environments.

**Exercises:**
```
Create custom VPC network
Create subnets (regional)
Configure firewall rules
Cloud NAT (for private instances)
VPC Peering
Cloud Router (dynamic routing)
Understand Shared VPC
```

**Hands-on: Build Custom Network**

```
VPC: my-custom-vpc
Subnet: web-subnet (us-central1, 10.0.1.0/24)
Firewall: Allow HTTP/HTTPS
Subnet: db-subnet (us-central1, 10.0.2.0/24)
Firewall: Allow internal traffic only
```

**Commands:**
```bash
# Create VPC
gcloud compute networks create my-custom-vpc \
--subnet-mode=custom

# Create subnets
gcloud compute networks subnets create web-subnet \
--network=my-custom-vpc \
--region=us-central1 \
--range=10.0.1.0/24

gcloud compute networks subnets create db-subnet \
--network=my-custom-vpc \
--region=us-central1 \
--range=10.0.2.0/24

# Firewall rule (allow HTTP)
gcloud compute firewall-rules create allow-http-custom \
--network=my-custom-vpc \
--allow=tcp:80,tcp:443 \
--source-ranges=0.0.0.0/0 \
--target-tags=web-server

# Cloud NAT (for private instances to reach internet)
gcloud compute routers create my-router \
--network=my-custom-vpc \
--region=us-central1

gcloud compute routers nats create my-nat \
--router=my-router \
--region=us-central1 \
--auto-allocate-nat-external-ips \
--nat-all-subnet-ip-ranges
```

**Key concepts:**
- **VPC**: Isolated virtual network
- **Subnet**: Regional IP range within VPC
- **Firewall Rules**: Ingress/egress traffic control
- **Cloud NAT**: Outbound internet access for private instances
- **VPC Peering**: Connect two VPCs
- **Shared VPC**: Multi-project network sharing

---

### Week 2: STORAGE + DATABASE + CONTAINERS

#### Day 1-2: Cloud Storage (Object Storage)

**Why important:** GCP's equivalent to AWS S3. Used for backups, logs, artifacts.

**Exercises:**
```
Create Cloud Storage bucket
Upload/download files (gsutil)
Configure bucket lifecycle policies
Enable versioning
Set IAM permissions on bucket
Host static website
Signed URLs (temporary access)
```

**Hands-on: Static Website on Cloud Storage**

1. **Create bucket:**
```bash
# Bucket names must be globally unique
gsutil mb -c STANDARD -l us-central1 gs://my-static-website-12345/
```

2. **Upload website:**
```bash
echo "<h1>Hello from Cloud Storage!</h1>" > index.html
echo "<h1>404 Not Found</h1>" > 404.html

gsutil cp index.html gs://my-static-website-12345/
gsutil cp 404.html gs://my-static-website-12345/
```

3. **Configure as website:**
```bash
gsutil web set -m index.html -e 404.html gs://my-static-website-12345/
```

4. **Make public:**
```bash
gsutil iam ch allUsers:objectViewer gs://my-static-website-12345/
```

5. **Access:**
```
https://storage.googleapis.com/my-static-website-12345/index.html
```

6. **Lifecycle policy (delete old objects):**
```bash
cat > lifecycle.json << 'EOF'
{
"lifecycle": {
"rule": [
{
"action": {"type": "Delete"},
"condition": {"age": 30}
}
]
}
}
EOF

gsutil lifecycle set lifecycle.json gs://my-static-website-12345/
```

**Key concepts:**
- **Bucket**: Container for objects
- **Storage Classes**: Standard, Nearline, Coldline, Archive
- **Lifecycle**: Automatic deletion/archival
- **Versioning**: Keep old versions of objects
- **Signed URL**: Temporary access URL

**Cloud Storage concepts used in companies:**
- Artifact storage (build outputs, container images)
- Log aggregation
- Data lake
- Cross-region replication

---

#### Day 3-4: Cloud SQL (Managed Relational Database)

**Why important:** Managed MySQL, PostgreSQL, SQL Server. Used in production.

**Exercises:**
```
Create Cloud SQL instance (PostgreSQL/MySQL)
Configure authorized networks
Connect with Cloud SQL Proxy
Create database and tables
Configure automated backups
Create manual snapshot
Understand High Availability (HA) configuration
```

**Hands-on: PostgreSQL on Cloud SQL**

1. **Create Cloud SQL instance:**
```bash
gcloud sql instances create my-postgres \
--database-version=POSTGRES_15 \
--tier=db-f1-micro \
--region=us-central1 \
--root-password=MySecurePassword123
```

2. **Get connection info:**
```bash
gcloud sql instances describe my-postgres
```

3. **Connect using Cloud SQL Proxy:**
```bash
# Download proxy
wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy
chmod +x cloud_sql_proxy

# Run proxy
./cloud_sql_proxy -instances=PROJECT_ID:us-central1:my-postgres=tcp:5432 &

# Connect
psql -h localhost -U postgres
```

4. **Database operations:**
```sql
CREATE DATABASE testdb;
\c testdb
CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(100), email VARCHAR(100));
INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');
SELECT * FROM users;
```

5. **Automated backups:**
```bash
gcloud sql instances patch my-postgres \
--backup-start-time=03:00
```

**Key concepts:**
- **Cloud SQL**: Managed MySQL, PostgreSQL, SQL Server
- **HA configuration**: Regional HA with automatic failover
- **Read replicas**: Scale read operations
- **Cloud SQL Proxy**: Secure connection without whitelisting IPs
- **Automated backups**: Daily backups with point-in-time recovery

---

#### Day 5-7: Google Kubernetes Engine (GKE)

**Why important:** Managed Kubernetes, heavily used in enterprises. GCP's strength.

**Exercises:**
```
Create GKE cluster
Deploy containerized application
Expose service with LoadBalancer
Configure autoscaling (HPA, Cluster Autoscaler)
Rolling updates
Workload Identity (GKE → GCP service accounts)
```

**Hands-on: Deploy Nginx on GKE**

1. **Create GKE cluster:**
```bash
gcloud container clusters create my-cluster \
--zone=us-central1-a \
--num-nodes=2 \
--machine-type=e2-medium \
--enable-autoscaling \
--min-nodes=1 \
--max-nodes=5
```

2. **Get credentials:**
```bash
gcloud container clusters get-credentials my-cluster --zone=us-central1-a
kubectl get nodes
```

3. **Deploy application:**
```yaml
# nginx-deployment.yaml
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
type: LoadBalancer
selector:
app: nginx
ports:
- protocol: TCP
port: 80
targetPort: 80
```

4. **Apply and access:**
```bash
kubectl apply -f nginx-deployment.yaml
kubectl get services
# Get EXTERNAL-IP
curl http://<EXTERNAL-IP>
```

5. **Horizontal Pod Autoscaler:**
```bash
kubectl autoscale deployment nginx --cpu-percent=50 --min=3 --max=10
```

**Key concepts:**
- **GKE**: Managed Kubernetes
- **Node Pool**: Group of nodes with same configuration
- **Cluster Autoscaler**: Add/remove nodes based on demand
- **HPA**: Horizontal Pod Autoscaler (scale pods)
- **Workload Identity**: Kubernetes SA → GCP service account
- **Autopilot**: Fully managed GKE (GCP manages nodes)

**GKE concepts used in companies:**
- Multi-cluster management
- Service mesh (Anthos Service Mesh / Istio)
- GitOps (ArgoCD, Flux)
- Config Connector (manage GCP resources via Kubernetes)

---

### Week 3: MONITORING + SECURITY + SERVERLESS

#### Day 1-2: Cloud Monitoring & Logging (formerly Stackdriver)

**Why important:** Unified monitoring and logging for GCP resources.

**Exercises:**
```
View metrics (CPU, memory, disk)
Create alerting policies
View logs in Cloud Logging
Create log-based metrics
Build custom dashboards
Set up uptime checks
```

**Hands-on: Monitoring Setup**

1. **View Compute Engine metrics:**
```bash
# List metrics
gcloud monitoring metrics-descriptors list \
--filter="metric.type:compute.googleapis.com"

# Query CPU utilization
gcloud monitoring time-series list \
--filter='metric.type="compute.googleapis.com/instance/cpu/utilization"' \
--interval-start-time="2024-03-18T00:00:00Z" \
--interval-end-time="2024-03-18T23:59:59Z"
```

2. **Create alerting policy:**
```bash
# Alert if CPU > 80% for 5 minutes
gcloud alpha monitoring policies create \
--notification-channels=CHANNEL_ID \
--display-name="High CPU Alert" \
--condition-display-name="CPU > 80%" \
--condition-threshold-value=0.8 \
--condition-threshold-duration=300s
```

3. **View logs:**
```bash
# View Compute Engine logs
gcloud logging read "resource.type=gce_instance" \
--limit=50 \
--format=json

# Filter errors
gcloud logging read "severity>=ERROR" --limit=10
```

4. **Export logs to Cloud Storage:**
```bash
gcloud logging sinks create my-sink \
storage.googleapis.com/my-log-bucket \
--log-filter='resource.type="gce_instance"'
```

**Key concepts:**
- **Cloud Monitoring**: Metrics collection and alerting
- **Cloud Logging**: Centralized log management
- **Log-based metrics**: Create metrics from log entries
- **Uptime checks**: Monitor endpoint availability
- **Notification channels**: Email, SMS, Slack, PagerDuty

---

#### Day 3-4: Secret Manager + Cloud KMS

**Why important:** Secure secrets management and encryption key management.

**Exercises:**
```
Create secret in Secret Manager
Access secret from Compute Engine
Create encryption key in Cloud KMS
Encrypt/decrypt data with KMS
Configure IAM permissions for secrets
```

**Hands-on: Database Password Management**

1. **Create secret:**
```bash
echo -n "MyDatabasePassword123" | \
gcloud secrets create db-password \
--data-file=- \
--replication-policy="automatic"
```

2. **Grant access to service account:**
```bash
gcloud secrets add-iam-policy-binding db-password \
--member="serviceAccount:my-service-account@PROJECT_ID.iam.gserviceaccount.com" \
--role="roles/secretmanager.secretAccessor"
```

3. **Access secret (from application):**
```python
from google.cloud import secretmanager

client = secretmanager.SecretManagerServiceClient()
name = "projects/PROJECT_ID/secrets/db-password/versions/latest"
response = client.access_secret_version(request={"name": name})
password = response.payload.data.decode("UTF-8")
print(f"Password: {password}")
```

4. **Cloud KMS (encryption keys):**
```bash
# Create keyring
gcloud kms keyrings create my-keyring --location=global

# Create key
gcloud kms keys create my-key \
--location=global \
--keyring=my-keyring \
--purpose=encryption

# Encrypt file
gcloud kms encrypt \
--location=global \
--keyring=my-keyring \
--key=my-key \
--plaintext-file=secret.txt \
--ciphertext-file=secret.txt.enc
```

**Key concepts:**
- **Secret Manager**: Store API keys, passwords, certificates
- **Cloud KMS**: Encryption key management
- **Key rotation**: Automatic key rotation
- **Envelope encryption**: Data encrypted with DEK, DEK encrypted with KEK

---

#### Day 5-7: Cloud Functions (Serverless) + Cloud Run

**Why important:** Event-driven automation and serverless containers.

**Exercises:**
```
Create Cloud Function (HTTP trigger)
Cloud Storage trigger function
Pub/Sub trigger function
Deploy containerized app to Cloud Run
Cloud Run autoscaling
Cloud Scheduler (cron jobs)
```

**Hands-on 1: Image Resize Function (Cloud Storage trigger)**

1. **Function code (Python):**
```python
# main.py
from google.cloud import storage
from PIL import Image
import io

def resize_image(event, context):
"""Triggered by Cloud Storage upload."""
bucket_name = event['bucket']
file_name = event['name']

if not file_name.startswith('uploads/'):
return

storage_client = storage.Client()
bucket = storage_client.bucket(bucket_name)
blob = bucket.blob(file_name)

# Download image
image_data = blob.download_as_bytes()
image = Image.open(io.BytesIO(image_data))

# Resize
image.thumbnail((200, 200))

# Upload thumbnail
output = io.BytesIO()
image.save(output, format='JPEG')
output.seek(0)

thumbnail_blob = bucket.blob(f'thumbnails/{file_name.split("/")[-1]}')
thumbnail_blob.upload_from_file(output, content_type='image/jpeg')

print(f'Resized {file_name}')
```

2. **Deploy function:**
```bash
gcloud functions deploy resize_image \
--runtime=python39 \
--trigger-bucket=my-image-bucket \
--entry-point=resize_image
```

3. **Test:**
```bash
gsutil cp test-image.jpg gs://my-image-bucket/uploads/
# Function runs automatically
gsutil ls gs://my-image-bucket/thumbnails/
```

**Hands-on 2: API on Cloud Run**

1. **Containerized app (Flask):**
```python
# app.py
from flask import Flask, jsonify
import os

app = Flask(__name__)

@app.route('/')
def hello():
return jsonify({"message": "Hello from Cloud Run!"})

@app.route('/health')
def health():
return jsonify({"status": "healthy"})

if __name__ == '__main__':
app.run(host='0.0.0.0', port=int(os.environ.get('PORT', 8080)))
```

2. **Dockerfile:**
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
CMD ["python", "app.py"]
```

3. **Build and deploy:**
```bash
# Build with Cloud Build
gcloud builds submit --tag gcr.io/PROJECT_ID/my-api

# Deploy to Cloud Run
gcloud run deploy my-api \
--image gcr.io/PROJECT_ID/my-api \
--platform managed \
--region us-central1 \
--allow-unauthenticated
```

**Key concepts:**
- **Cloud Functions**: Event-driven serverless functions
- **Cloud Run**: Serverless containers (any language)
- **Pub/Sub**: Asynchronous messaging
- **Cloud Scheduler**: Cron jobs in the cloud
- **Eventarc**: Event routing to Cloud Run

---

### Week 4: ADVANCED + DATA + CI/CD

#### Day 1-2: BigQuery (Data Warehouse)

**Why important:** Serverless, highly scalable analytics database.

**Exercises:**
```
Create dataset
Load data from Cloud Storage
Run SQL queries
Partitioned tables
Scheduled queries
Data Studio visualization
```

**Hands-on: Analyze Web Logs with BigQuery**

1. **Create dataset:**
```bash
bq mk --dataset PROJECT_ID:analytics
```

2. **Load CSV data:**
```bash
bq load \
--source_format=CSV \
--skip_leading_rows=1 \
analytics.web_logs \
gs://my-bucket/access-logs.csv \
timestamp:TIMESTAMP,ip:STRING,method:STRING,path:STRING,status:INTEGER
```

3. **Query data:**
```sql
-- Top 10 IPs by request count
SELECT ip, COUNT(*) as requests
FROM `PROJECT_ID.analytics.web_logs`
GROUP BY ip
ORDER BY requests DESC
LIMIT 10;

-- Error rate by hour
SELECT
EXTRACT(HOUR FROM timestamp) as hour,
COUNTIF(status >= 400) / COUNT(*) * 100 as error_rate
FROM `PROJECT_ID.analytics.web_logs`
GROUP BY hour
ORDER BY hour;
```

4. **Scheduled query:**
```bash
bq query --use_legacy_sql=false \
--destination_table=analytics.daily_summary \
--schedule='every 24 hours' \
"SELECT DATE(timestamp) as date, COUNT(*) as requests
FROM \`PROJECT_ID.analytics.web_logs\`
GROUP BY date"
```

**Key concepts:**
- **BigQuery**: Serverless data warehouse
- **Partitioning**: Improve query performance
- **Clustering**: Optimize queries on specific columns
- **Streaming inserts**: Real-time data ingestion
- **Data Studio**: BI visualization tool

---

#### Day 3-4: Cloud Build + Artifact Registry (CI/CD)

**Why important:** Native CI/CD for GCP, container registry.

**Exercises:**
```
Create Artifact Registry repository
Push Docker image to Artifact Registry
Create Cloud Build trigger
Build Docker image with Cloud Build
Deploy to Cloud Run from Cloud Build
Multi-stage build pipeline
```

**Hands-on: CI/CD Pipeline**

1. **Create Artifact Registry:**
```bash
gcloud artifacts repositories create my-repo \
--repository-format=docker \
--location=us-central1
```

2. **Cloud Build configuration (cloudbuild.yaml):**
```yaml
steps:
# Build Docker image
- name: 'gcr.io/cloud-builders/docker'
args: ['build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repo/my-app:$COMMIT_SHA', '.']

# Push to Artifact Registry
- name: 'gcr.io/cloud-builders/docker'
args: ['push', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repo/my-app:$COMMIT_SHA']

# Deploy to Cloud Run
- name: 'gcr.io/google.com/cloudsdktool/cloud-sdk'
entrypoint: gcloud
args:
- 'run'
- 'deploy'
- 'my-app'
- '--image=us-central1-docker.pkg.dev/$PROJECT_ID/my-repo/my-app:$COMMIT_SHA'
- '--region=us-central1'
- '--platform=managed'

images:
- 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repo/my-app:$COMMIT_SHA'
```

3. **Trigger build:**
```bash
gcloud builds submit --config cloudbuild.yaml .
```

4. **Automated trigger (GitHub):**
```bash
gcloud builds triggers create github \
--repo-name=my-repo \
--repo-owner=my-github-user \
--branch-pattern="^main$" \
--build-config=cloudbuild.yaml
```

**Key concepts:**
- **Cloud Build**: Serverless CI/CD
- **Artifact Registry**: Docker and language package repository
- **Build triggers**: Automatic builds on Git push
- **Substitution variables**: $PROJECT_ID, $COMMIT_SHA, $BRANCH_NAME

---

#### Day 5-7: Terraform + GCP (Infrastructure as Code)

**Exercises:**
```
Terraform GCP provider
Create VPC with Terraform
Deploy Compute Engine instances
Create Cloud Storage buckets
GKE cluster with Terraform
State management (Cloud Storage backend)
```

**Hands-on: Terraform GCP**

1. **Provider configuration:**
```hcl
# main.tf
terraform {
required_providers {
google = {
source = "hashicorp/google"
version = "~> 5.0"
}
}
backend "gcs" {
bucket = "my-terraform-state"
prefix = "terraform/state"
}
}

provider "google" {
project = var.project_id
region = var.region
}

variable "project_id" {
description = "GCP Project ID"
}

variable "region" {
default = "us-central1"
}
```

2. **Create VPC and VM:**
```hcl
# network.tf
resource "google_compute_network" "vpc" {
name = "my-vpc"
auto_create_subnetworks = false
}

resource "google_compute_subnetwork" "subnet" {
name = "my-subnet"
ip_cidr_range = "10.0.1.0/24"
region = var.region
network = google_compute_network.vpc.id
}

# compute.tf
resource "google_compute_instance" "web" {
name = "web-server"
machine_type = "e2-micro"
zone = "${var.region}-a"

boot_disk {
initialize_params {
image = "ubuntu-os-cloud/ubuntu-2204-lts"
}
}

network_interface {
subnetwork = google_compute_subnetwork.subnet.id
access_config {}
}

metadata_startup_script = file("startup.sh")

tags = ["http-server"]
}
```

3. **Deploy:**
```bash
terraform init
terraform plan -var="project_id=my-project-12345"
terraform apply -var="project_id=my-project-12345"
```

---

## GCP Services Priority (For Corporate Environment)

| Priority | Service | Why important |
|----------|---------|---------------|
| ** HIGH** | **IAM** | Access management |
| ** HIGH** | **Compute Engine** | Virtual machines |
| ** HIGH** | **VPC** | Networking |
| ** HIGH** | **GKE** | Kubernetes (widely used) |
| ** HIGH** | **Cloud Storage** | Object storage |
| ** MEDIUM** | **Cloud SQL** | Managed databases |
| ** MEDIUM** | **Cloud Monitoring/Logging** | Observability |
| ** MEDIUM** | **Secret Manager** | Secrets management |
| ** MEDIUM** | **Cloud Build** | CI/CD |
| ** LOW** | **Cloud Functions** | Serverless automation |
| ** LOW** | **BigQuery** | Data analytics |

---

## Learning Resources

**Official GCP:**
- Free Tier: https://cloud.google.com/free
- Documentation: https://cloud.google.com/docs
- Architecture Center: https://cloud.google.com/architecture
- Qwiklabs: https://www.qwiklabs.com/

**Hands-on Labs:**
- Google Cloud Skills Boost: https://www.cloudskillsboost.google/
- Codelabs: https://codelabs.developers.google.com/
- GCP Free Tier: https://cloud.google.com/free

**YouTube:**
- Google Cloud Tech (official)
- GCP podcast
- Fireship (GCP tutorials)

---

## COST MANAGEMENT

### Free Tier:

| Service | Free Tier | Duration |
|---------|-----------|----------|
| **Trial Credit** | $300 credit | 90 days |
| **Compute Engine** | 1 e2-micro instance/month | Always Free |
| **Cloud Storage** | 5 GB standard storage | Always Free |
| **Cloud Functions** | 2M invocations/month | Always Free |
| **Cloud Run** | 2M requests/month | Always Free |
| **BigQuery** | 1 TB queries/month | Always Free |
| **Cloud Build** | 120 build-minutes/day | Always Free |

### Cost Protection:

1. **Set budget alerts:**
```bash
gcloud billing budgets create \
--billing-account=BILLING_ACCOUNT_ID \
--display-name="Monthly Budget" \
--budget-amount=50 \
--threshold-rule=percent=50 \
--threshold-rule=percent=90
```

2. **Monitor costs:**
- Cloud Console → Billing → Cost Table
- Enable cost breakdown by project/service

3. **Clean up resources:**
```bash
# Delete GKE cluster
gcloud container clusters delete my-cluster --zone=us-central1-a

# Delete Compute instances
gcloud compute instances delete web-server --zone=us-central1-a

# Delete Cloud SQL
gcloud sql instances delete my-postgres

# Delete Cloud Storage bucket
gsutil rm -r gs://my-bucket/
```

---

## Weekly Checklist

### Week 1:
- [ ] GCP project created
- [ ] gcloud CLI configured
- [ ] Service account created
- [ ] VM instance launched and accessed
- [ ] Custom VPC created
- [ ] Budget alert set

### Week 2:
- [ ] Cloud Storage bucket created
- [ ] Static website hosted
- [ ] Cloud SQL instance created
- [ ] GKE cluster deployed
- [ ] Application running on GKE

### Week 3:
- [ ] Cloud Monitoring dashboard created
- [ ] Alerting policy configured
- [ ] Secret Manager secret created
- [ ] Cloud Function deployed
- [ ] Cloud Run service deployed

### Week 4:
- [ ] BigQuery dataset and queries
- [ ] Cloud Build pipeline created
- [ ] Artifact Registry repository
- [ ] Terraform configuration written
- [ ] Infrastructure deployed with Terraform

---

**Ready?** Start with **Week 1, Day 1-2: IAM + Cloud SDK**! 
