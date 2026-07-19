# ☁️ PHASE 15 — CI/CD Integration

## 🎯 Goal

By the end of this phase, you should be able to confidently say:

> "Whenever I push code to GitHub, my pipeline automatically checks formatting, validates Terraform, scans for mistakes, creates a Terraform plan, and, with approval, deploys the infrastructure."

This is how many modern Cloud Engineering and DevOps teams manage infrastructure.

---

# 📖 What is CI/CD?

CI/CD stands for **Continuous Integration** and **Continuous Deployment (or Delivery)**.

---

# 🔄 Continuous Integration (CI)

Every time you push code, GitHub automatically checks whether your Terraform code is correct.

### Workflow

```text
Edit Terraform Code
        │
        ▼
git add .
        │
        ▼
git commit
        │
        ▼
git push
        │
        ▼
GitHub Actions Starts
        │
        ▼
Checks Terraform Code
        │
        ▼
Reports Success or Failure
```

Instead of manually checking everything, GitHub does it automatically.

---

# 🚀 Continuous Deployment (CD)

After all checks pass, GitHub can automatically deploy your infrastructure.

### Workflow

```text
Push Code
    │
    ▼
Pipeline Runs
    │
    ▼
All Checks Pass
    │
    ▼
terraform apply
    │
    ▼
AWS Infrastructure Updated
```

---

# 💼 Why Companies Use CI/CD

## ❌ Without CI/CD

Every developer deploys manually.

```text
Developer A
terraform apply

Developer B
terraform apply

Developer C
terraform apply
```

Problems:

- No deployment history
- Inconsistent infrastructure
- Higher chance of mistakes
- Difficult collaboration

---

## ✅ With CI/CD

Everything goes through one automated pipeline.

```text
GitHub
   │
   ▼
Checks
   │
   ▼
Approval
   │
   ▼
Deployment
```

Benefits:

- Consistent deployments
- Automated validation
- Deployment history
- Better collaboration
- Safer production changes

---

# 🛠 CI/CD Tools

## GitHub Actions

The most popular CI/CD tool for GitHub repositories.

```text
Git Push
    │
    ▼
GitHub Actions
    │
    ▼
Runs Workflow
```

**Best choice for your portfolio.**

---

## GitLab CI

Integrated directly into GitLab.

---

## CircleCI

A third-party CI/CD platform commonly used by startups.

---

## Jenkins

One of the oldest and most widely used CI/CD tools.

Typically self-hosted and still common in enterprise environments.

---

# 🔁 CI/CD Pipeline Overview

---

# Stage 1 — Commit

Write your Terraform code.

```hcl
resource "aws_vpc" "main" {
```

Commit your changes.

```bash
git add .
git commit -m "Added VPC"
git push
```

The pipeline starts automatically.

---

# Stage 2 — Terraform Format

Purpose:

Automatically checks whether your Terraform code follows the standard formatting.

Command:

```bash
terraform fmt
```

### Bad Formatting

```hcl
resource "aws_vpc""main"{
cidr_block="10.0.0.0/16"
}
```

### Good Formatting

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

If formatting is incorrect, the pipeline fails.

---

# Stage 3 — Terraform Validate

Checks Terraform syntax.

Command:

```bash
terraform validate
```

Example error:

```text
Unknown argument

cidrblock
```

If validation fails, the pipeline stops.

---

# Stage 4 — TFLint

## What is TFLint?

TFLint is a Terraform linter.

It detects:

- Bad practices
- Deprecated syntax
- Provider issues
- Configuration mistakes
- Unused variables

Command:

```bash
tflint
```

---

# Stage 5 — Security Scan

One of the most important stages.

Example:

```hcl
password = "mypassword123"
```

A security scanner can detect hardcoded secrets.

Another example:

```text
Security Group

Ingress

0.0.0.0/0

Port 22
```

Opening SSH to the entire internet is a security risk.

Popular security scanners:

- Checkov
- tfsec
- Trivy

---

# Stage 6 — Terraform Plan

The pipeline generates an execution plan without changing AWS.

Command:

```bash
terraform plan
```

Example output:

```text
+ create VPC
+ create subnet
+ create EC2
```

No infrastructure changes are made yet.

---

# Stage 7 — Approval

Production deployments usually require manual approval.

```text
Manager
    │
    ▼
Reviews Plan
    │
    ▼
Clicks Approve
    │
    ▼
Deployment Starts
```

This prevents accidental production deployments.

---

# Stage 8 — Terraform Apply

Finally, Terraform applies the approved changes.

```bash
terraform apply
```

AWS infrastructure is updated.

Deployment complete.

---

# 📌 Complete Pipeline

```text
Developer
    │
    ▼
git push
    │
    ▼
GitHub Actions
    │
    ▼
terraform fmt
    │
    ▼
terraform validate
    │
    ▼
TFLint
    │
    ▼
Security Scan
    │
    ▼
terraform plan
    │
    ▼
Manual Approval
    │
    ▼
terraform apply
```

---

# ⚙️ How GitHub Actions Works

Inside your repository, create:

```text
.github/
└── workflows/
    └── terraform.yml
```

GitHub automatically detects workflow files placed in the `workflows` directory.

---

# 📄 Basic Workflow Structure

```yaml
name: Terraform CI

on:
  push:
    branches:
      - main

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - Checkout repository

      - Install Terraform

      - Terraform Init

      - Terraform Format

      - Terraform Validate

      - TFLint

      - Security Scan

      - Terraform Plan
```

Each step runs sequentially.

If one step fails, the workflow stops.

---

# 🔐 AWS Authentication

The pipeline needs permission to manage AWS resources.

Instead of hardcoding credentials, store them as **GitHub Secrets**.

Example secret names:

```text
AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY
```

The workflow reads these secrets securely during execution.

> **Best Practice:** Production environments typically use short-lived credentials through **OpenID Connect (OIDC)** instead of long-lived access keys.

---

# 📁 Portfolio Project Structure

```text
terraform-cicd-project/
│
├── .github/
│   └── workflows/
│       └── terraform.yml
│
├── main.tf
├── variables.tf
├── outputs.tf
├── provider.tf
├── versions.tf
└── README.md
```

---

# 🔄 Expected Workflow

```text
git push
    │
    ▼
GitHub Actions
    │
    ▼
terraform fmt -check
    │
    ▼
terraform init
    │
    ▼
terraform validate
    │
    ▼
TFLint
    │
    ▼
Checkov
    │
    ▼
terraform plan
    │
    ▼
Manual Approval (Optional)
    │
    ▼
terraform apply
```

---

# 📅 Suggested Learning Schedule (2 Weeks)

## Week 1

1. Learn GitHub Actions fundamentals.
2. Create a workflow triggered on every push.
3. Add `terraform fmt -check`.
4. Add `terraform init`.
5. Add `terraform validate`.

---

## Week 2

1. Integrate TFLint.
2. Add Checkov for security scanning.
3. Generate a Terraform plan.
4. Configure GitHub Secrets for AWS credentials.
5. Add a protected environment with manual approval before deployment.

---

# 🎯 End Goal

By completing this phase, you'll have a portfolio project demonstrating an end-to-end Terraform CI/CD pipeline.

The project showcases:

- Infrastructure as Code (Terraform)
- GitHub Actions
- Automated validation
- Code formatting
- Terraform linting
- Security scanning
- Deployment planning
- Secure AWS authentication
- Manual production approval
- Automated infrastructure deployment

This type of automation is highly valued by recruiters and hiring managers because it demonstrates real-world Cloud Engineering and DevOps practices used to deploy infrastructure safely, consistently, and at scale.
