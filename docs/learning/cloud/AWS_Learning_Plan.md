# AWS Quick Reference

## Goals

1. **Understanding AWS basics**
2. **Using key services** (that you'll use in corporate environments)
3. **Leveraging Free Tier** (12 months free)
4. **Combining with Terraform** later

---

## Prerequisites

- AWS Free Tier account: https://aws.amazon.com/free/
- AWS CLI installed and configured
- Basic Linux/bash knowledge
- Credit card (won't charge if you stay within Free Tier)

---

## 4-WEEK LEARNING PLAN

### Week 1: BASICS + IAM + EC2

#### Day 1-2: AWS Console + IAM (Identity and Access Management)

**Why important:** All AWS usage starts with IAM. You'll use this in the company too.

**Exercises:**
```
 Get familiar with AWS Console
 Create IAM User
 Create IAM Group (Developers, DevOps, ReadOnly)
 Understand IAM Policies (Managed vs Inline)
 Create IAM Role for EC2
 Set up MFA (Multi-Factor Authentication)
 Generate Access Key (for CLI)
```

**Hands-on:**
1. IAM Dashboard → Users → Add User
2. Create a "devops-user" with programmatic access
3. Attach "AdministratorAccess" policy (for learning!)
4. Generate Access Key
5. Configure AWS CLI:
 ```bash
 aws configure
 aws sts get-caller-identity # Verification
 ```

**Key concepts:**
- **User**: Person or application
- **Group**: Collection of users
- **Role**: Temporary permissions (for EC2, Lambda, etc.)
- **Policy**: JSON document (what can be done)

**IAM concepts used in companies:**
- Service Accounts (for automation)
- Cross-account roles
- Least privilege principle

---

#### Day 3-5: EC2 (Elastic Compute Cloud)

**Why important:** Fundamental compute service. VMs in the cloud.

**Exercises:**
```
 Launch EC2 instance (t2.micro, Free Tier)
 Configure Security Group (firewall)
 Use SSH keys
 Attach EBS Volume (disk)
 Elastic IP (static IP)
 Create Snapshot (backup)
 Create AMI (Amazon Machine Image)
 User Data script (automatic setup on boot)
```

**Hands-on Project: Web Server on EC2**

1. **Launch instance:**
 ```bash
 # Using AWS CLI (later with Terraform too!)
 aws ec2 run-instances \
 --image-id ami-0c55b159cbfafe1f0 \
 --instance-type t2.micro \
 --key-name MyKeyPair \
 --security-group-ids sg-123456 \
 --user-data file://webserver-setup.sh
 ```

2. **User Data script (webserver-setup.sh):**
 ```bash
 #!/bin/bash
 yum update -y
 yum install -y httpd
 systemctl start httpd
 systemctl enable httpd
 echo "<h1>Hello from AWS EC2!</h1>" > /var/www/html/index.html
 ```

3. **Security Group:**
 - SSH (22) - only from your IP
 - HTTP (80) - from anywhere

4. **Verification:**
 ```bash
 # Get Public IP
 aws ec2 describe-instances --query 'Reservations[*].Instances[*].[PublicIpAddress]' --output text

 # Test web server
 curl http://<PUBLIC_IP>
 ```

**Key concepts:**
- **AMI**: Template for VM (OS + software)
- **Instance Type**: CPU, RAM, Network (t2.micro = 1 vCPU, 1 GB RAM)
- **EBS**: Block storage (persistent disk)
- **Security Group**: Stateful firewall
- **Key Pair**: SSH key

**EC2 concepts used in companies:**
- Auto Scaling Groups (automatic scaling)
- Load Balancers (load distribution)
- Spot Instances (cheaper, but interruptible)

---

#### Day 6-7: VPC (Virtual Private Cloud) Basics

**Why important:** Network isolation, critical in corporate environments.

**Exercises:**
```
 Create VPC
 Subnets (public, private)
 Internet Gateway
 Configure Route Table
 Network ACL vs Security Group difference
 VPC Peering (optional)
```

**Hands-on: Build Your Own VPC**

```
VPC: 10.0.0.0/16
 Public Subnet: 10.0.1.0/24 (web tier)
 Internet Gateway → Route Table
 Private Subnet: 10.0.2.0/24 (database tier)
 NAT Gateway (outbound only, not inbound)
```

**Key concepts:**
- **VPC**: Isolated network in AWS
- **Subnet**: Network segment within VPC
- **Internet Gateway**: VPC → Internet
- **NAT Gateway**: Private subnet → Internet (one-way)
- **Route Table**: Where traffic goes

---

### Week 2: STORAGE + DATABASE + NETWORKING

#### Day 1-2: S3 (Simple Storage Service)

**Why important:** Object storage for artifacts, backups, logs. Used extensively in companies.

**Exercises:**
```
 Create S3 Bucket
 Upload file (CLI + Console)
 Enable Versioning
 Lifecycle policy (automatic archiving/deletion)
 S3 bucket policy (access management)
 Static website hosting
 Server-side encryption
```

**Hands-on: Static Website on S3**

1. **Create bucket:**
 ```bash
 aws s3 mb s3://my-static-website-12345
 ```

2. **Upload website files:**
 ```bash
 echo "<h1>Hello from S3!</h1>" > index.html
 aws s3 cp index.html s3://my-static-website-12345/ --acl public-read
 ```

3. **Enable static website hosting:**
 ```bash
 aws s3 website s3://my-static-website-12345/ \
 --index-document index.html
 ```

4. **Bucket policy (public read):**
 ```json
 {
 "Version": "2012-10-17",
 "Statement": [{
 "Effect": "Allow",
 "Principal": "*",
 "Action": "s3:GetObject",
 "Resource": "arn:aws:s3:::my-static-website-12345/*"
 }]
 }
 ```

**Key concepts:**
- **Bucket**: Container for files
- **Object**: File in S3 (max 5 TB)
- **Versioning**: Store file versions
- **Lifecycle**: Automatic archiving/deletion
- **Storage Classes**: Standard, IA, Glacier (cost optimization)

**S3 concepts used in companies:**
- Artifact storage (build outputs)
- Log aggregation
- Backup storage
- Cross-region replication

---

#### Day 3-4: RDS (Relational Database Service)

**Why important:** Managed database, used in companies (PostgreSQL, MySQL).

**Exercises:**
```
 Create RDS instance (PostgreSQL/MySQL, Free Tier)
 Security Group configuration (accessible only from VPC)
 Connect with psql/mysql CLI
 Create database
 Configure automated backups
 Create snapshot (manual backup)
 Understand Multi-AZ deployment (HA)
```

**Hands-on: PostgreSQL RDS**

1. **Create RDS instance:**
 ```bash
 aws rds create-db-instance \
 --db-instance-identifier mydb \
 --db-instance-class db.t3.micro \
 --engine postgres \
 --master-username admin \
 --master-user-password MyPassword123 \
 --allocated-storage 20 \
 --vpc-security-group-ids sg-123456
 ```

2. **Connect:**
 ```bash
 # Get endpoint
 aws rds describe-db-instances \
 --db-instance-identifier mydb \
 --query 'DBInstances[0].Endpoint.Address' --output text

 # Connect
 psql -h <ENDPOINT> -U admin -d postgres
 ```

3. **Database operations:**
 ```sql
 CREATE DATABASE testdb;
 \c testdb
 CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(100));
 INSERT INTO users (name) VALUES ('Alice'), ('Bob');
 SELECT * FROM users;
 ```

**Key concepts:**
- **DB Instance**: Managed database VM
- **Multi-AZ**: High Availability (failover)
- **Read Replica**: Read scaling
- **Automated Backup**: Daily automatic backup
- **Snapshot**: Manual backup

---

#### Day 5-7: ELB (Elastic Load Balancing) + Auto Scaling

**Why important:** Critical in production environments (HA, scaling).

**Exercises:**
```
 Create Application Load Balancer (ALB)
 Configure Target Group
 Set up Health Check
 Create Launch Template
 Configure Auto Scaling Group
 Scaling Policy (CPU-based)
```

**Hands-on: Scalable Web Server**

```
Internet
 ↓
Application Load Balancer (ALB)
 ↓
Auto Scaling Group (2-5 instances)
 ↓
EC2 Instances (t2.micro)
```

1. **Launch Template:**
 - AMI: Amazon Linux 2
 - User Data: nginx installation
 - Security Group: HTTP (80)

2. **Auto Scaling Group:**
 - Min: 2
 - Desired: 2
 - Max: 5
 - Scaling Policy: CPU > 70% → add instance

3. **Load Balancer:**
 - Type: Application
 - Listener: HTTP:80
 - Target Group: ASG instances

**Key concepts:**
- **Load Balancer**: Distribute traffic
- **Target Group**: Collection of backend servers
- **Health Check**: Is the server alive?
- **Auto Scaling**: Automatic scaling based on load

---

### Week 3: MONITORING + LOGGING + SECURITY

#### Day 1-2: CloudWatch (Monitoring & Logging)

**Why important:** This is the foundation of monitoring in AWS for companies.

**Exercises:**
```
 View CloudWatch Metrics (EC2 CPU, Network)
 Create CloudWatch Alarm (CPU > 80%)
 CloudWatch Logs - send EC2 logs
 Log Group + Log Stream
 Create CloudWatch Dashboard
 SNS topic (email notification)
```

**Hands-on: Monitoring Setup**

1. **EC2 Metrics Dashboard:**
 - CPU Utilization
 - Network In/Out
 - Disk I/O

2. **Create alarm:**
 ```bash
 aws cloudwatch put-metric-alarm \
 --alarm-name cpu-high \
 --alarm-description "CPU > 80%" \
 --metric-name CPUUtilization \
 --namespace AWS/EC2 \
 --statistic Average \
 --period 300 \
 --threshold 80 \
 --comparison-operator GreaterThanThreshold \
 --evaluation-periods 2
 ```

3. **Install CloudWatch Logs Agent on EC2:**
 ```bash
 sudo yum install -y amazon-cloudwatch-agent
 # Configuration: /var/log/messages → CloudWatch
 ```

**Key concepts:**
- **Metrics**: Numerical data (CPU%, Memory%)
- **Logs**: Text log entries
- **Alarms**: Alert based on metrics
- **Dashboard**: Visualization

---

#### Day 3-4: CloudTrail + Config (Audit & Compliance)

**Why important:** Required in companies (who did what, when).

**Exercises:**
```
 Enable CloudTrail (API call logging)
 View CloudTrail logs in S3
 Set up AWS Config (resource compliance)
 Config Rules (e.g., S3 encryption required)
```

**Key concepts:**
- **CloudTrail**: Audit log (all API calls)
- **Config**: Resource configuration tracking
- **Config Rules**: Compliance checking

---

#### Day 5-7: Secrets Manager + Parameter Store + KMS

**Why important:** Sensitive data management in companies (passwords, API keys).

**Exercises:**
```
 Secrets Manager - store database password
 Parameter Store - store configuration
 KMS - create encryption key
 S3 bucket encryption with KMS
 Retrieve secret from EC2 (with IAM role)
```

**Hands-on: Database Password Management**

1. **Create secret:**
 ```bash
 aws secretsmanager create-secret \
 --name prod/db/password \
 --secret-string "MySecurePassword123"
 ```

2. **Retrieve secret (from EC2):**
 ```bash
 aws secretsmanager get-secret-value \
 --secret-id prod/db/password \
 --query SecretString --output text
 ```

3. **IAM Role for EC2:**
 - Policy: SecretsManagerReadWrite
 - Attached to EC2

**Key concepts:**
- **Secrets Manager**: Sensitive data (passwords)
- **Parameter Store**: Configuration (can be non-sensitive)
- **KMS**: Encryption key management
- **Encryption at rest**: Data encrypted when stored

---

### Week 4: ADVANCED + CONTAINERIZATION + SERVERLESS

#### Day 1-3: ECS (Elastic Container Service) + ECR

**Why important:** Container orchestration, used in companies (alongside Kubernetes).

**Exercises:**
```
 Create ECR repository (Docker registry)
 Push Docker image to ECR
 Create ECS Cluster
 Task Definition (container config)
 Start ECS Service
 Fargate vs EC2 launch type
```

**Hands-on: Nginx Container on ECS**

1. **ECR repository:**
 ```bash
 aws ecr create-repository --repository-name my-nginx
 ```

2. **Push Docker image:**
 ```bash
 # Login
 aws ecr get-login-password | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

 # Build & tag
 docker build -t my-nginx .
 docker tag my-nginx:latest <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/my-nginx:latest

 # Push
 docker push <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/my-nginx:latest
 ```

3. **ECS Task Definition + Service:**
 - Cluster: my-cluster
 - Task: 1 container (nginx image from ECR)
 - Service: 2 tasks (desired count)
 - Load Balancer: ALB

**Key concepts:**
- **ECR**: Docker registry (private)
- **ECS Cluster**: Container execution environment
- **Task Definition**: Container configuration
- **Service**: Task management (scaling, health check)
- **Fargate**: Serverless containers (no EC2 management)

---

#### Day 4-5: Lambda (Serverless)

**Why important:** Event-driven automation, used in companies.

**Exercises:**
```
 Create Lambda function (Python/Node.js)
 S3 trigger (runs on file upload)
 CloudWatch Events trigger (schedule)
 Lambda environment variables
 Lambda layers (dependencies)
 Lambda + DynamoDB (NoSQL database)
```

**Hands-on: Image Resize Lambda (S3 trigger)**

1. **Lambda function (Python):**
 ```python
 import boto3
 from PIL import Image
 import io

 s3 = boto3.client('s3')

 def lambda_handler(event, context):
 # Parse S3 event
 bucket = event['Records'][0]['s3']['bucket']['name']
 key = event['Records'][0]['s3']['object']['key']

 # Download image
 response = s3.get_object(Bucket=bucket, Key=key)
 img = Image.open(response['Body'])

 # Resize
 img.thumbnail((200, 200))

 # Upload resized
 buffer = io.BytesIO()
 img.save(buffer, 'JPEG')
 buffer.seek(0)

 s3.put_object(
 Bucket=bucket,
 Key=f'thumbnails/{key}',
 Body=buffer
 )

 return {'statusCode': 200}
 ```

2. **S3 trigger configuration:**
 - Event: s3:ObjectCreated:*
 - Prefix: uploads/

3. **Testing:**
 ```bash
 aws s3 cp test-image.jpg s3://my-bucket/uploads/
 # Lambda runs automatically
 aws s3 ls s3://my-bucket/thumbnails/
 ```

**Key concepts:**
- **Lambda**: Serverless function (run code without servers)
- **Trigger**: What starts the Lambda (S3, API Gateway, CloudWatch)
- **Execution Role**: IAM role for Lambda
- **Cold Start**: First invocation is slower

---

#### Day 6-7: API Gateway + DynamoDB (Serverless API)

**Exercises:**
```
 Create DynamoDB table
 API Gateway REST API
 Lambda integration
 API Key + Usage Plan
 CORS configuration
```

**Hands-on: TODO API (Serverless)**

```
API Gateway (REST API)
 ↓
Lambda Function (CRUD operations)
 ↓
DynamoDB (NoSQL table)
```

**Final project:**
- GET /todos - list
- POST /todos - new todo
- PUT /todos/{id} - update
- DELETE /todos/{id} - delete

---

## AWS Services Priority (For Corporate Environment)

| Priority | Service | Why important for your company |
|----------|---------|-------------------------------|
| ** HIGH** | **IAM** | All access management |
| ** HIGH** | **EC2** | Compute basics |
| ** HIGH** | **S3** | Artifact storage, backups |
| ** HIGH** | **VPC** | Network isolation |
| ** MEDIUM** | **RDS** | Database (if used) |
| ** MEDIUM** | **CloudWatch** | Monitoring & Logging |
| ** MEDIUM** | **ECS/ECR** | Container orchestration |
| ** MEDIUM** | **Lambda** | Automation, event-driven |
| ** LOW** | **API Gateway** | If building APIs |
| ** LOW** | **DynamoDB** | NoSQL (if used) |

---

## Learning Resources

### Official AWS Documentation:
- AWS Free Tier: https://aws.amazon.com/free/
- AWS CLI Reference: https://docs.aws.amazon.com/cli/
- AWS Well-Architected: https://aws.amazon.com/architecture/well-architected/

### Hands-on Labs:
- AWS Workshops: https://workshops.aws/
- AWS Skill Builder: https://skillbuilder.aws/ (free courses)
- Qwiklabs AWS: https://www.qwiklabs.com/catalog?cloud%5B%5D=AWS

### YouTube Channels:
- AWS Online Tech Talks
- Stephane Maarek (AWS Certified courses)
- FreeCodeCamp AWS tutorials

---

## COST MANAGEMENT - VERY IMPORTANT!

### Free Tier Limits:

| Service | Free Tier Limit | Time Limit |
|---------|----------------|-----------|
| **EC2** | 750 hrs/mo (t2.micro) | 12 months |
| **RDS** | 750 hrs/mo (db.t2.micro) | 12 months |
| **S3** | 5 GB storage, 20k GET, 2k PUT | 12 months |
| **Lambda** | 1M requests/mo, 400k GB-sec | FOREVER |
| **CloudWatch** | 10 custom metrics, 10 alarms | FOREVER |
| **DynamoDB** | 25 GB storage, 25 RCU/WCU | FOREVER |

### Cost Protection:

1. **Set up billing alert:**
 ```bash
 aws cloudwatch put-metric-alarm \
 --alarm-name billing-alert \
 --alarm-description "Alert if bill > $5" \
 --metric-name EstimatedCharges \
 --namespace AWS/Billing \
 --statistic Maximum \
 --period 21600 \
 --threshold 5 \
 --comparison-operator GreaterThanThreshold
 ```

2. **Create budget:**
 - AWS Console → Billing → Budgets
 - Monthly budget: $10
 - Alert: 80% ($8)

3. **Use Cost Explorer:**
 - Daily check: how much it costs

4. **ALWAYS destroy what you don't use:**
 ```bash
 # EC2 instances
 aws ec2 terminate-instances --instance-ids i-xxxxx

 # RDS
 aws rds delete-db-instance --db-instance-identifier mydb --skip-final-snapshot

 # S3 bucket
 aws s3 rb s3://my-bucket --force
 ```

---

## Weekly Checklist

### Week 1:
- [ ] IAM user + access key created
- [ ] AWS CLI configured
- [ ] EC2 instance launched and SSH connected
- [ ] Security Group configured
- [ ] Web server installed via User Data script
- [ ] VPC + Subnet created
- [ ] Billing alert set ($5)

### Week 2:
- [ ] S3 bucket created
- [ ] Static website on S3
- [ ] RDS PostgreSQL instance created
- [ ] Connected with psql
- [ ] Load Balancer + Auto Scaling Group
- [ ] Lifecycle policy on S3

### Week 3:
- [ ] CloudWatch dashboard
- [ ] CloudWatch alarm (CPU)
- [ ] CloudWatch Logs agent on EC2
- [ ] Secrets Manager secret created
- [ ] KMS key created
- [ ] CloudTrail enabled

### Week 4:
- [ ] Docker image pushed to ECR
- [ ] ECS service started
- [ ] Lambda function created (S3 trigger)
- [ ] API Gateway + Lambda integration
- [ ] DynamoDB table + CRUD operations

---

## Next Step: Terraform + AWS

After completing the 4 weeks **manually** (Console + CLI), start **automating with Terraform**:

```
~/terraform-practice/20-aws-ec2/ # Already exists!
~/terraform-practice/21-aws-s3/ # S3 + CloudFront
~/terraform-practice/22-aws-rds/ # RDS + VPC
~/terraform-practice/23-aws-ecs/ # ECS + ECR + ALB
~/terraform-practice/24-aws-lambda/ # Lambda + API Gateway
```

---

**Ready?** Start with **Week 1, Day 1-2: IAM**! 
