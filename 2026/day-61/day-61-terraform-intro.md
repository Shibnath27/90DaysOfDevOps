# 📄 Day 61 – Introduction to Terraform & First AWS Infrastructure

## 🚀 Overview

Today I started my **Infrastructure as Code (IaC)** journey using **Terraform** to provision real AWS resources.

Instead of manually creating infrastructure via the AWS console, I defined everything in code and executed it from the terminal — making infrastructure reproducible, version-controlled, and automated.

---

## 🧠 Task 1: Understanding Infrastructure as Code (IaC)

### 🔹 What is IaC?

Infrastructure as Code (IaC) is the practice of managing and provisioning infrastructure using configuration files instead of manual processes.

Instead of clicking in the AWS console, I define infrastructure (servers, networks, storage) in code.

### 🔹 Why IaC matters in DevOps

* Eliminates manual errors
* Enables version control for infrastructure
* Ens
  ures repeatability across environments
* Makes deployments faster and consistent

### 🔹 Problems solved vs manual provisioning

| Manual Approach    | IaC Approach           |
| ------------------ | ---------------------- |
| Error-prone        | Deterministic          |
| Not reproducible   | Fully reproducible     |
| No version history | Git-based tracking     |
| Slow setup         | Automated provisioning |

### 🔹 Terraform vs Other Tools

* **Terraform** → Declarative, cloud-agnostic
* **AWS CloudFormation** → AWS-specific
* **Ansible** → Procedural, config management
* **Pulumi** → Uses programming languages

### 🔹 Key Concepts

* **Declarative** → Define *what* you want, not *how* to do it
* **Cloud-agnostic** → Works across AWS, Azure, GCP

---

## ⚙️ Task 2: Setup

### 🔹 Terraform Installation

```bash
terraform -version
```

### 🔹 AWS CLI Configuration

```bash
aws configure
aws sts get-caller-identity
```

✔ Verified successful authentication with AWS

---

## 🪣 Task 3: Create an S3 Bucket

### 🔹 main.tf

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "shibnath-terraform-day61-unique"
}
```

### 🔹 Commands Used

```bash
terraform init
terraform plan
terraform apply
```

### 🔍 Observations

* `terraform init` downloaded:

  * AWS provider plugin
  * Created `.terraform/` directory
* `.terraform/` contains:

  * provider binaries
  * dependency metadata

✔ Verified bucket in AWS console

---

## 💻 Task 4: Add EC2 Instance

```hcl
resource "aws_instance" "my_ec2" {
  ami           = "ami-0f5ee92e2d63afc18"
  instance_type = "t2.micro"

  tags = {
    Name = "TerraWeek-Day1"
  }
}
```

### 🔹 Execution

```bash
terraform plan
terraform apply
```

### 🔍 Key Insight

Terraform only created the EC2 instance because:

* It compares desired state (code) with current state (`terraform.tfstate`)
* S3 already exists → no changes needed

✔ Verified EC2 instance in AWS console

---

## 📂 Task 5: Terraform State

### 🔹 Commands

```bash
terraform show
terraform state list
terraform state show aws_s3_bucket.my_bucket
terraform state show aws_instance.my_ec2
```

### 🔹 What state file stores

* Resource IDs
* Metadata
* Dependencies
* Current configuration snapshot

### ⚠️ Important Notes

* Never edit state manually → can corrupt infrastructure mapping
* Do NOT commit state file to Git:

  * Contains sensitive data
  * Causes conflicts in teams

---

## 🔄 Task 6: Modify & Destroy

### 🔹 Change Tag

```hcl
tags = {
  Name = "TerraWeek-Modified"
}
```

### 🔹 Plan Output Symbols

* `+` → Create
* `~` → Modify in-place
* `-` → Destroy

✔ Tag update was **in-place modification (~)**

---

### 🔥 Destroy Infrastructure

```bash
terraform destroy
```

✔ Verified all resources removed from AWS

---

## 🧠 Key Learnings

* Terraform follows a **desired state model**
* State file is the backbone of Terraform operations
* Infrastructure can be:

  * Version-controlled
  * Reproducible
  * Fully automated

---

## 📌 Conclusion

This was my first step into Infrastructure as Code using Terraform. I successfully:

* Provisioned AWS resources via code
* Understood Terraform lifecycle
* Learned how state management works
* Destroyed infrastructure cleanly

---

