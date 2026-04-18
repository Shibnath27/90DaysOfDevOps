# 🚀 Terraform Capstone: Multi-Environment Infrastructure with Workspaces & Modules

## 📌 Overview

This project is the **final capstone of my Terraform journey**, where I built a **production-style multi-environment AWS infrastructure** using:

* Terraform Workspaces
* Custom Modules
* Remote Backend (S3 + DynamoDB)
* Environment-based configuration (dev, staging, prod)

👉 One codebase → Multiple isolated environments

---

## 🎯 What I Built

* ✅ 3 Environments: **dev, staging, prod**
* ✅ Separate VPC per environment
* ✅ Custom Security Groups with dynamic rules
* ✅ EC2 instances with environment-based sizing
* ✅ Fully modular Terraform architecture
* ✅ Workspace-based environment isolation

---

## 🏗️ Project Structure

```
terraweek-capstone/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── locals.tf
│
├── dev.tfvars
├── staging.tfvars
├── prod.tfvars
│
├── .gitignore
│
└── modules/
    ├── vpc/
    ├── security-group/
    └── ec2-instance/
```

---

## ⚙️ Key Concepts Used

### 🔹 Terraform Workspaces

* Used to manage **multiple environments with same code**
* Each workspace has its **own isolated state**

```
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod
```

---

### 🔹 Workspace Behavior

| Feature               | Behavior                                       |
| --------------------- | ---------------------------------------------- |
| `terraform.workspace` | Returns current environment (dev/staging/prod) |
| State Storage         | Separate state per workspace                   |
| Isolation             | Full environment separation                    |

---

## 🧩 Modules Breakdown

### 1. VPC Module

Creates:

* VPC
* Subnet
* Internet Gateway
* Route Table

**Inputs:**

* CIDR
* Subnet CIDR
* Environment
* Project Name

**Outputs:**

* VPC ID
* Subnet ID

---

### 2. Security Group Module

* Dynamic ingress rules using list input
* Allows all outbound traffic

**Inputs:**

* VPC ID
* Ports list

---

### 3. EC2 Module

* Deploys EC2 instance
* Environment-based naming

**Outputs:**

* Instance ID
* Public IP

---

## 🌍 Environment Configurations

### 🔹 dev.tfvars

```
vpc_cidr      = "10.0.0.0/16"
subnet_cidr   = "10.0.1.0/24"
instance_type = "t2.micro"
ingress_ports = [22, 80]
```

---

### 🔹 staging.tfvars

```
vpc_cidr      = "10.1.0.0/16"
subnet_cidr   = "10.1.1.0/24"
instance_type = "t2.small"
ingress_ports = [22, 80, 443]
```

---

### 🔹 prod.tfvars

```
vpc_cidr      = "10.2.0.0/16"
subnet_cidr   = "10.2.1.0/24"
instance_type = "t3.small"
ingress_ports = [80, 443]
```

👉 Notice:

* No SSH in production
* Larger instance in prod
* Separate CIDRs for isolation

---

## 🔁 Deployment Steps

### 1️⃣ Initialize Terraform

```
terraform init
```

---

### 2️⃣ Create Workspaces

```
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod
```

---

### 3️⃣ Deploy DEV

```
terraform workspace select dev
terraform plan -var-file="dev.tfvars"
terraform apply -var-file="dev.tfvars"
```

---

### 4️⃣ Deploy STAGING

```
terraform workspace select staging
terraform apply -var-file="staging.tfvars"
```

---

### 5️⃣ Deploy PROD

```
terraform workspace select prod
terraform apply -var-file="prod.tfvars"
```

---

## 🔍 Verification

* 3 VPCs created (different CIDR ranges)
* 3 EC2 instances (different sizes)
* Different naming:

  * terraweek-dev-server
  * terraweek-staging-server
  * terraweek-prod-server

---

## 📤 Outputs

```
terraform output
```

Returns:

* VPC ID
* Subnet ID
* Instance Public IP

---

## 🔐 Best Practices Implemented

### ✅ Code Structure

* Separate files for variables, providers, outputs, locals

### ✅ State Management

* Remote backend (S3)
* State locking (DynamoDB)

### ✅ Security

* `.gitignore` for:

  ```
  .terraform/
  *.tfstate
  *.tfvars
  ```

### ✅ Modules

* Reusable, single responsibility
* Clear inputs/outputs

### ✅ Naming Convention

```
<project>-<environment>-<resource>
```

### ✅ Tagging Strategy

```
Project     = terraweek
Environment = dev/staging/prod
ManagedBy   = Terraform
```

---

## ⚠️ Important Learnings

* Workspaces ≠ separate directories
* State is the **source of truth**
* Drift can break infra silently
* Never hardcode values
* Always run:

  ```
  terraform plan
  ```

---

## 🧹 Cleanup

Destroy environments in reverse order:

```
terraform workspace select prod
terraform destroy -var-file="prod.tfvars"

terraform workspace select staging
terraform destroy -var-file="staging.tfvars"

terraform workspace select dev
terraform destroy -var-file="dev.tfvars"
```

---

## 🧠 Key Takeaways

* Terraform scales via **modules + workspaces**
* One codebase can manage multiple environments safely
* Infrastructure should be:

  * Reusable
  * Version-controlled
  * Fully automated

---

