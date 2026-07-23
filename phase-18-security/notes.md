# ☁️ Phase 18 — Security

## 🎯 Goal

By the end of this phase, you should be able to confidently say:

> **"I can secure my Terraform infrastructure by protecting secrets, enforcing security policies, scanning Infrastructure as Code for vulnerabilities, and ensuring compliance before deployment."**

This phase focuses on **Infrastructure as Code (IaC) Security**. Instead of simply deploying cloud resources, you'll learn how to protect sensitive information, detect vulnerabilities, enforce organizational policies, and maintain compliance with security standards.

---

# 📖 1. Secret Management

## What is Secret Management?

Secret Management is the practice of securely storing sensitive information instead of hardcoding it inside Terraform code.

### Examples of Secrets

- AWS Access Keys
- Database Passwords
- API Keys
- SSH Private Keys
- Access Tokens
- Certificates

---

## ❌ Bad Example

```hcl
provider "aws" {
  access_key = "AKIAxxxxxxxx"
  secret_key = "abcd123456"
}
```

Anyone with access to the repository can view your credentials.

---

## ✅ Better Methods

Instead of hardcoding credentials, use one of the following:

- Environment Variables
- AWS IAM Roles
- AWS Secrets Manager
- AWS Systems Manager Parameter Store
- HashiCorp Vault

### Example (Environment Variables)

```bash
export AWS_ACCESS_KEY_ID=xxxxxxxx
export AWS_SECRET_ACCESS_KEY=xxxxxxxx
```

Terraform automatically detects these variables.

---

## 🎯 Goal

Never store secrets inside:

- `.tf` files
- Git repositories
- GitHub repositories
- Public source code

---

# 📖 2. Sentinel

## What is Sentinel?

Sentinel is **HashiCorp's Policy as Code framework**.

It validates Terraform plans before they are applied, ensuring infrastructure follows your organization's security and governance rules.

---

## Example Policies

### ✅ Allow

- Approved AWS regions
- Required resource tags
- Encryption enabled
- Approved EC2 instance types

### ❌ Deny

- Public S3 buckets
- Unencrypted EBS volumes
- Open Security Groups
- Large or unauthorized EC2 instance types

---

### Example

A developer creates:

- Security Group
- Port **22**
- Source **0.0.0.0/0**

Sentinel evaluates the Terraform plan and blocks the deployment because SSH is exposed to the entire internet.

---

## 🎯 Purpose

Prevent insecure infrastructure from being deployed.

---

# 📖 3. Compliance

## What is Compliance?

Compliance ensures your infrastructure follows organizational, legal, and industry security standards.

---

## Common Standards

- PCI DSS
- HIPAA
- SOC 2
- ISO 27001
- CIS Benchmarks

---

## Example Rules

- All storage must be encrypted.
- Logging must be enabled.
- IAM users cannot have administrator privileges.
- Public databases are prohibited.

---

## 🎯 Goal

Maintain secure, auditable, and policy-compliant infrastructure.

---

# 📖 4. Trivy

## What is Trivy?

Trivy is an open-source security scanner developed by **Aqua Security**.

It scans for:

- Vulnerabilities
- Misconfigurations
- Secrets
- Container Images
- Terraform files
- Kubernetes configurations

---

## Example

```bash
trivy config .
```

Possible output:

```text
HIGH
S3 bucket allows public access

MEDIUM
Encryption disabled
```

---

## 🎯 Purpose

Identify security issues before deployment.

---

# 📖 5. KICS

## What is KICS?

**KICS (Keeping Infrastructure as Code Secure)** scans Infrastructure as Code files for security vulnerabilities.

---

## Supported Technologies

- Terraform
- CloudFormation
- Kubernetes
- Docker
- ARM Templates
- Bicep

---

## Example

```bash
kics scan -p .
```

Possible findings:

- Open Security Groups
- Public Storage Buckets
- Missing Encryption
- Weak IAM Policies

---

## 🎯 Goal

Detect insecure Terraform configurations before deployment.

---

# 📖 6. Terrascan

## What is Terrascan?

Terrascan is an Infrastructure as Code security scanner that analyzes Terraform code for security issues before deployment.

---

## Example

```bash
terrascan scan
```

Possible findings:

- Public S3 Bucket
- Unrestricted SSH Access
- Missing Encryption
- IAM Wildcard Permissions

---

## 🎯 Goal

Catch security misconfigurations early in the development process.

---

# 📖 7. Checkov

## What is Checkov?

Checkov is an Infrastructure as Code security scanner developed by **Prisma Cloud**.

It scans:

- Terraform
- Kubernetes
- CloudFormation
- Docker
- GitHub Actions
- AWS
- Azure
- Google Cloud Platform (GCP)

---

## Example

```bash
checkov -d .
```

Possible output:

```text
FAILED

S3 Bucket
Encryption disabled

FAILED

Security Group
Port 22 open to the Internet
```

---

## 🎯 Goal

Automatically detect security, compliance, and best-practice violations before infrastructure is deployed.

---

# 📊 Summary

| Topic | Purpose |
|---------|---------|
| Secret Management | Store credentials securely instead of hardcoding them. |
| Sentinel | Enforce security and organizational policies before Terraform applies changes. |
| Compliance | Ensure infrastructure follows security standards and regulations. |
| Trivy | Scan Terraform, containers, and Kubernetes for vulnerabilities and misconfigurations. |
| KICS | Analyze Infrastructure as Code for security issues. |
| Terrascan | Detect Terraform security misconfigurations before deployment. |
| Checkov | Scan Infrastructure as Code for security, compliance, and best-practice violations. |

---

# 🔄 How These Topics Fit Together

```text
                 Write Terraform Code
                          │
                          ▼
                 Secret Management
                          │
                          ▼
     Security Scanners (Trivy / KICS /
       Terrascan / Checkov)
                          │
                          ▼
             Compliance Validation
                          │
                          ▼
         Sentinel Policy Enforcement
                          │
                          ▼
               Terraform Apply
                          │
                          ▼
              Secure Infrastructure
```

---

# 🎓 Key Takeaways

- **Protect secrets** by using secure storage solutions instead of hardcoding credentials.
- **Scan Terraform code** regularly with tools like Trivy, KICS, Terrascan, and Checkov.
- **Enforce policies** using Sentinel to prevent unsafe infrastructure from being deployed.
- **Meet compliance requirements** by following recognized security standards.
- **Shift security left** by detecting vulnerabilities before infrastructure reaches production.

> **Security should be integrated into every stage of the Infrastructure as Code lifecycle—not added after deployment.**
