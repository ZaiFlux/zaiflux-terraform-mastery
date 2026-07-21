# PHASE 16 — Testing (Terraform Focus)

## 🎯 Goal

The goal of Phase 16 is to make sure your Terraform infrastructure is **correct, secure, and reliable before production**.

You already know how to build:

```text
Terraform
   │
   ▼
AWS Resources
   │
   ▼
VPC, Subnet, EC2, Security Group
```

Now you will learn how to **test** what you built.

---

# 1. Unit Testing

## Purpose

Test the **smallest part** of your Terraform code.

Think:

> "Is this single Terraform component correct?"

### Example Project Structure

```text
modules/
└── vpc/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

Terraform:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

### Unit Test

Checks whether the VPC has the correct CIDR block.

**Expected**

```text
10.0.0.0/16
```

**Result**

```text
PASS ✅
```

### What Do You Test?

#### VPC

- ✅ CIDR Block
- ✅ Tags
- ✅ Enable DNS
- ✅ Region

#### Subnet

- ✅ CIDR Block
- ✅ Availability Zone
- ✅ Public/Private Setting

#### Security Group

- ✅ Correct Ports
- ✅ No Dangerous Rules

---

# 2. Contract Testing

## Purpose

Test communication between Terraform modules.

A module is like a small service.

### Example

```text
VPC Module
Output:
vpc_id
      │
      ▼
EC2 Module
Input:
vpc_id
```

The contract is:

**VPC Module promises**

> "I will provide a VPC ID."

**EC2 Module says**

> "I need a VPC ID."

### Example

VPC Module

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

EC2 Module

```hcl
module "ec2" {
  vpc_id = module.vpc.vpc_id
}
```

### Test

Is EC2 receiving the correct VPC ID?

- ✅ YES → Continue
- ❌ NO → Stop deployment

---

# 3. Integration Testing

## Purpose

Test multiple AWS resources working together.

Instead of testing one resource, Integration Testing verifies the entire connection.

Example from your Terraform AWS Network Project:

```text
VPC
 │
 ▼
Subnet
 │
 ▼
Route Table
 │
 ▼
Internet Gateway
 │
 ▼
EC2
```

Question:

> Can these resources work together?

### Example

EC2 should have Internet access.

Expected network flow:

```text
EC2
 │
 ▼
Subnet
 │
 ▼
Route Table
 │
 ▼
Internet Gateway
 │
 ▼
Internet
```

If the Route Table is incorrect:

```text
EC2
 │
 ▼
Subnet
 │
 ▼
❌ Route Table
 │
 ▼
Internet
```

Result:

```text
FAIL ❌
```

### Typical Integration Testing Workflow

```text
terraform apply
        │
        ▼
Create Test Infrastructure
        │
        ▼
Run Tests
        │
        ▼
terraform destroy
```

---

# 4. End-to-End Testing (E2E)

## Purpose

Test the **entire environment** just like a real user would.

Example architecture:

```text
User
 │
 ▼
Load Balancer
 │
 ▼
EC2
 │
 ▼
Application
 │
 ▼
Database
```

E2E asks:

> Can a user actually use this system?

### Example Test

Open:

```text
https://mywebsite.com
```

Expected response:

```text
HTTP 200 OK
```

Meaning:

- ✅ DNS works
- ✅ Load Balancer works
- ✅ EC2 works
- ✅ Application works

---

# 5. Testing Terraform Modules

A common Terraform project structure:

```text
terraform-project/
├── modules/
│   ├── vpc/
│   ├── subnet/
│   ├── ec2/
│   └── security-group/
│
├── environments/
│   ├── dev/
│   └── production/
│
└── tests/
```

Each module should be tested independently.

## Example

### VPC Module Test

Input

```text
CIDR = 10.0.0.0/16
```

Expected

- ✅ VPC Created
- ✅ Correct CIDR

---

### EC2 Module Test

Input

- AMI
- Instance Type
- Subnet

Expected

- ✅ EC2 Running
- ✅ Correct Subnet Attached

---

# Terraform Testing Tools

---

# 1. Terratest

## What is it?

Terratest is a **Go testing framework** for Terraform.

It tests **real AWS infrastructure**.

Workflow:

```text
Terraform Code
      │
      ▼
Terratest
      │
      ▼
AWS
      │
      ▼
Verify Result
```

Example:

```text
main.tf

tests/
└── vpc_test.go
```

Test Flow

1. `terraform apply`
2. Check if the VPC exists
3. `terraform destroy`

Terratest can verify:

- ✅ VPC Exists
- ✅ Subnet Exists
- ✅ EC2 Exists
- ✅ Output Values
- ✅ Network Connectivity

---

# 2. Checkov

## What is it?

Checkov is a **security scanner** for Infrastructure as Code.

It checks:

> "Is your infrastructure secure?"

### Example

Bad Terraform:

```hcl
resource "aws_security_group" "example" {

  ingress {
    from_port   = 22
    to_port     = 22
    cidr_blocks = ["0.0.0.0/0"]
  }

}
```

Problem

SSH is open to the entire Internet.

Checkov result:

```text
FAILED ❌

Security Risk Detected
```

Run:

```bash
checkov -d .
```

Example output:

```text
Passed: 80

Failed: 3
```

---

# 3. tfsec

## What is it?

tfsec is a Terraform-specific security scanner.

It focuses entirely on Terraform security risks.

Example:

```hcl
resource "aws_s3_bucket" "data" {

}
```

tfsec checks:

- Is encryption enabled?
- Is public access blocked?
- Is logging enabled?

Run:

```bash
tfsec .
```

Example output:

```text
HIGH

S3 Bucket Encryption Missing
```

---

# Checkov vs tfsec

| Feature | Checkov | tfsec |
|----------|----------|--------|
| Main Purpose | Security Scanning | Terraform Security Scanning |
| Supports | Terraform, Kubernetes, Docker | Terraform |
| Finds | Compliance Issues | Terraform Risks |
| Used in CI/CD | ✅ Yes | ✅ Yes |

---

# Phase 16 Practical Project

Use your previous **terraform-aws-network** project containing:

- VPC
- Subnet
- Internet Gateway
- Route Table
- Security Group
- EC2

---

## Step 1 — Validate Terraform

```bash
terraform validate
```

---

## Step 2 — Run Security Scan

```bash
tfsec .
```

---

## Step 3 — Run Compliance Scan

```bash
checkov -d .
```

---

## Step 4 — Infrastructure Testing

Create:

```text
tests/
└── network_test.go
```

Terratest should verify:

- ✅ VPC Created
- ✅ Subnet Created
- ✅ EC2 Running
- ✅ Security Group Attached

---

# Final Understanding

Phase 16 teaches:

```text
BUILD
 │
 ▼
Terraform
 │
 ▼
TEST
 │
 ├── Unit Testing
 ├── Contract Testing
 ├── Integration Testing
 └── End-to-End Testing
 │
 ▼
SECURITY CHECK
 │
 ├── Terratest
 ├── Checkov
 └── tfsec
 │
 ▼
Production Ready Infrastructure
```

---

# Portfolio Outcome

Before Phase 16:

> "I can create AWS infrastructure."

After Phase 16:

> **"I can create, test, secure, and deploy production-ready infrastructure using Terraform."**

This demonstrates the practical skills expected of a **Cloud Engineer**, making your Terraform projects more reliable, secure, and suitable for production environments.
