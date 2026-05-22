# Terraform Practice Projects

This directory contains all Terraform practice projects - from local machine to cloud providers.

## 📂 Project Structure

```
terraform-practice/
├── README.md                        # This file
├── TERRAFORM_CHEATSHEET.md          # Complete reference
├── QUICK_REFERENCE.md               # Quick overview
│
├── 01-local-files/                  #  BEGINNER - Local files
│   ├── main.tf
│   └── README.md
│
├── 02-podman/                       #  BEGINNER - Podman containers
│   ├── main.tf
│   ├── variables.tf
│   ├── terraform.tfvars.example
│   └── README.md
│
├── 03-null-provider/                #  INTERMEDIATE - Run scripts
│   ├── main.tf
│   └── README.md
│
├── 10-kvm-local/                    #  LOCAL VM - KVM/QEMU
│   ├── main.tf
│   ├── variables.tf
│   └── README.md
│
├── 20-aws-ec2/                      #  CLOUD - AWS EC2
│   ├── main.tf
│   ├── variables.tf
│   └── README.md
│
└── 30-gcp-vm/                       #  CLOUD - Google Cloud VM
    ├── main.tf
    ├── variables.tf
    └── README.md
```

##  Learning Path

### Week 1 - BASICS (LOCAL, SAFE)

#### Day 1-2: Local Files
```bash
cd 01-local-files
cat README.md  # Read it
terraform init
terraform plan
terraform apply
terraform destroy
```
**What you learn:** init, plan, apply, destroy, resource, output

#### Day 3-4: Podman
```bash
cd 02-podman
cat README.md
terraform init
terraform plan
terraform apply
# Test: curl http://localhost:8080
terraform destroy
```
**What you learn:** provider, variable, docker/podman integration

#### Day 5-7: KVM Virtual Machine
```bash
cd 10-kvm-local
cat README.md
terraform init
terraform plan
terraform apply
# Wait 2-3 minutes
terraform output vm_ip
ssh ubuntu@<VM_IP>
terraform destroy
```
**What you learn:** cloud-init, networking, data source, dependencies

---

### Week 2 - CLOUD BASICS

#### Day 1-3: AWS EC2 Free Tier
```bash
# 1. AWS account registration
# 2. AWS CLI configuration

cd 20-aws-ec2
cat README.md  # IMPORTANT: read about costs!
export TF_VAR_project_id="terraform-practice"  # Or terraform.tfvars

terraform init
terraform plan  # Check the preview!
terraform apply

# Test
curl $(terraform output -raw web_url)

# IMPORTANT: Destroy!
terraform destroy
```
**What you learn:** VPC, Security Group, AWS provider, Free Tier

#### Day 4-7: Google Cloud VM Always Free
```bash
# 1. GCP account registration
# 2. gcloud CLI installation and configuration

cd 30-gcp-vm
cat README.md
export TF_VAR_project_id="YOUR_GCP_PROJECT_ID"

terraform init
terraform plan
terraform apply

# Test
curl $(terraform output -raw web_url)

# Cleanup
terraform destroy
```
**What you learn:** GCP provider, firewall rules, static IP

---

### Week 3 - ADVANCED CONCEPTS

- **Modules:** Reusable components
- **State management:** Remote backend (S3, GCS)
- **Workspaces:** dev/staging/prod environments
- **For_each:** Managing multiple resources
- **Data sources:** Querying existing infrastructure

---

##  Quick Start - START NOW!

### If you're completely new:
```bash
cd ~/terraform-practice/01-local-files
cat README.md
terraform init
terraform apply
```

### If you have Terraform experience:
```bash
cd ~/terraform-practice/10-kvm-local
cat README.md
terraform init
terraform apply
```

### If you want to practice cloud (after registration):
```bash
# AWS:
cd ~/terraform-practice/20-aws-ec2

# GCP:
cd ~/terraform-practice/30-gcp-vm
```

---

## 💰 Cost Comparison

| Project | Cost | Free Tier | Time Limit | Recommended |
|---------|------|-----------|------------|-------------|
| **01-local-files** | $0 |  | ∞ | For beginners |
| **02-podman** | $0 |  | ∞ | For beginners |
| **10-kvm-local** | $0 |  | ∞ | **Best for practice!** |
| **20-aws-ec2** | $0* |  | 12 months | For cloud learning |
| **30-gcp-vm** | $0* |  | Always Free! | **Free forever!** |

*Staying within Free Tier limits

---

## ⚠ IMPORTANT WARNINGS

### AWS
-  Free Tier: 750 hours/month (12 months)
- ⚠ **ALWAYS destroy after practice!**
- ⚠ Set up Billing Alert at $1!
- ⚠ Only use t2.micro instances!

### Google Cloud
-  Always Free: 1 e2-micro (forever!)
-  $300 credit for 90 days
- ⚠ Only in us-central1, us-west1, us-east1 regions!
- ⚠ Set up Budget Alert!

### KVM (Local)
-  Completely free
-  No time limit
-  **Best for practice!**
- ⚠ Uses laptop resources

---

##  Documentation

### Terraform general:
- `TERRAFORM_CHEATSHEET.md` - Complete reference (all keywords)
- `QUICK_REFERENCE.md` - Quick overview (most important things)

### Project-specific:
- Every project has a detailed `README.md`
- Installation guide
- Usage examples
- Troubleshooting
- Practice exercises

---

##  Learning Tips

### 1. Start small
```bash
# DON'T:
terraform apply  # Immediately

# DO:
terraform plan   # Check it first!
terraform apply  # Then apply
```

### 2. Read the plan output
```bash
terraform plan
# + create (new)
# ~ modify (change)
# - destroy (delete)
# -/+ destroy and create (replace)
```

### 3. Use variables
```hcl
# DON'T:
resource "..." "..." {
  port = 8080
}

# DO:
variable "port" { default = 8080 }
resource "..." "..." {
  port = var.port
}
```

### 4. Output important information
```hcl
output "vm_ip" {
  value = resource.type.name.ip
}
```

### 5. ALWAYS destroy after practice
```bash
terraform destroy
# Otherwise:
# - KVM: Laptop resource waste
# - AWS/GCP: Costs incur!
```

---

##  Troubleshooting

### "command not found: terraform"
```bash
# Install it:
sudo dnf install terraform
```

### "Error: Failed to query available provider packages"
```bash
# Proxy or network issue
# Check internet connection
```

### "Error: Unsupported argument"
```bash
# Syntax error
terraform validate  # Check
terraform fmt       # Format
```

### KVM/Libvirt issues
```bash
# Libvirtd not running:
sudo systemctl start libvirtd

# User not in libvirt group:
sudo usermod -aG libvirt $USER
# Log out and back in!
```

### AWS Credentials
```bash
# Not configured:
aws configure

# Or:
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
```

### GCP Credentials
```bash
# Not logged in:
gcloud auth application-default login

# API not enabled:
gcloud services enable compute.googleapis.com
```

---

##  Next Steps After Practice

1. **Create modules** - Reusable components
2. **Remote state** - S3/GCS backend
3. **CI/CD integration** - GitHub Actions, GitLab CI
4. **Kubernetes provider** - Manage K8s resources
5. **Helm provider** - Helm charts with Terraform
6. **Terragrunt** - DRY Terraform config

---

## 📞 Help

If you get stuck:

1. **README.md** - Every project has detailed description
2. **CHEATSHEET.md** - Terraform reference
3. **Official docs**: https://developer.hashicorp.com/terraform/docs
4. **Provider docs**:
   - AWS: https://registry.terraform.io/providers/hashicorp/aws/latest/docs
   - GCP: https://registry.terraform.io/providers/hashicorp/google/latest/docs
   - Libvirt: https://registry.terraform.io/providers/dmacvicar/libvirt/latest/docs

---

##  Checklist - Before Starting

- [ ] Terraform installed (`terraform version`)
- [ ] Git initialized (optional, but recommended)
- [ ] SSH key created (`~/.ssh/id_rsa.pub`)
- [ ] Chosen provider prerequisites (KVM/AWS/GCP)
- [ ] README.md read
- [ ] Always check plan output before apply
- [ ] Destroy resources after practice

---

**Happy practicing!** 

If you have questions, check the CHEATSHEET.md or project README!
