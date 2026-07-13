# Phase 10 – Terraform State Management
\
    │
    ▼
AWS Resources Created
    │
    ▼
Example:

### Laptop A

Contains:

```text
terraform.tfstate
```

### Laptop B

No state file.

Problems:

- Duplicate resources
- State conflicts
- Broken deployments

This is why production teams use:

> **Remote State**

---

# Remote State

Instead of storing state on your laptop, store it in an **Amazon S3 bucket**.

Instead of:

```text
Laptop
```

State is stored in:

```text
AWS S3 Bucket
```

Diagram:

```text
Engineer
    │
    ▼
Terraform
    │
    ▼
S3 Bucket
    │
    ▼
terraform.tfstate
```

Benefits:

- Shared
- Backed up
- Secure
- Team collaboration

---

# S3 Backend

Terraform backend configuration:

```hcl
terraform {
  backend "s3" {
    bucket = "terraform-state-zai"
    key    = "prod/terraform.tfstate"
    region = "ap-southeast-1"
  }
}
```

### Explanation

**Question:** `bucket = ?`

**Answer:**

Where the Terraform state file is stored.

---

**Question:** `key = ?`

**Answer:**

The path of the state file inside the bucket.

---

**Question:** `region = ?`

**Answer:**

The AWS Region where the bucket exists.

---

# State Encryption

Never store Terraform state unencrypted.

Why?

State may contain:

- Passwords
- Secrets
- ARNs
- IP addresses
- Infrastructure metadata

Enable encryption:

```hcl
terraform {
  backend "s3" {
    bucket  = "terraform-state-zai"
    key     = "prod/terraform.tfstate"
    region  = "ap-southeast-1"
    encrypt = true
  }
}
```

### Question

What does:

```hcl
encrypt = true
```

mean?

### Answer

S3 encrypts the state file at rest.

---

# Versioning

Enable versioning on the S3 bucket.

Why?

Suppose the state becomes corrupted.

Without versioning:

```text
State Lost
```

With versioning:

```text
Version 1
Version 2
Version 3
```

You can restore any previous version.

Example:

```hcl
resource "aws_s3_bucket_versioning" "state" {
  bucket = aws_s3_bucket.state.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

---

# State Locking

Imagine:

Engineer A:

```text
terraform apply
```

At the same time...

Engineer B:

```text
terraform apply
```

Result:

```text
State Corruption
```

Terraform prevents this using:

> **State Locking**

---

# DynamoDB Locking

Terraform creates a lock entry.

Example:

```text
Lock ID:
terraform-prod-lock
```

While one person is applying changes:

```text
LOCKED
```

Other users receive:

```text
Error acquiring state lock
```

This prevents corruption.

---

# DynamoDB Table

```hcl
resource "aws_dynamodb_table" "terraform_lock" {
  name         = "terraform-locks"
  billing_mode = "PAY_PER_REQUEST"

  hash_key = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```

---

# Backend With Locking

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state-zai"
    key            = "prod/terraform.tfstate"
    region         = "ap-southeast-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

### Question

What is:

```hcl
dynamodb_table = "terraform-locks"
```

used for?

### Answer

Terraform state locking.

---

# `terraform state list`

Displays every resource tracked in the state file.

```bash
terraform state list
```

Example:

```text
aws_vpc.main
aws_subnet.public
aws_instance.web
```

### Question

Purpose?

### Answer

Shows all tracked resources.

---

# `terraform state show`

Displays information about a specific resource.

```bash
terraform state show aws_instance.web
```

Example output:

- AMI
- ID
- Tags
- Private IP

### Question

Purpose?

### Answer

Displays resource details stored in Terraform state.

---

# `terraform state rm`

Removes a resource from Terraform state only.

```bash
terraform state rm aws_instance.web
```

Important:

- AWS resource remains
- Only removed from Terraform state

### Question

Does it delete the AWS resource?

### Answer

No.

---

# `terraform state mv`

Renames a resource inside Terraform state.

Before:

```hcl
resource "aws_instance" "web" {}
```

After:

```hcl
resource "aws_instance" "frontend" {}
```

Move the state:

```bash
terraform state mv aws_instance.web aws_instance.frontend
```

### Question

Why?

### Answer

Avoids recreating the resource.

---

# `terraform import`

Import an existing AWS resource into Terraform state.

Example:

```bash
terraform import aws_s3_bucket.logs my-logs-bucket
```

### Question

Does this create a new resource?

### Answer

No.

---

### Question

What does it do?

### Answer

Adds an existing resource into Terraform state.

---

# `terraform state pull`

Downloads the remote state.

```bash
terraform state pull
```

### Question

Purpose?

### Answer

Reads the current remote state.

---

# `terraform state push`

Uploads a state file manually.

```bash
terraform state push terraform.tfstate
```

### Question

Common use?

### Answer

Rare emergency recovery.

---

# `terraform force-unlock`

Sometimes a lock remains.

Example:

- Laptop crashes
- State remains locked

Unlock manually:

```bash
terraform force-unlock LOCK_ID
```

### Question

Danger?

### Answer

Can corrupt state if another `terraform apply` is still running.

---

# Sensitive Data

Avoid:

```hcl
output "db_password" {
  value = var.password
}
```

Better:

```hcl
output "db_password" {
  value     = var.password
  sensitive = true
}
```

### Question

Purpose?

### Answer

Hides sensitive values from Terraform output.

---

# Split State Files

Large organizations separate infrastructure into multiple state files.

Example:

```text
network/
compute/
database/
monitoring/
```

Each directory has its own:

```text
terraform.tfstate
```

Benefits:

- Faster plans
- Smaller blast radius
- Easier team ownership

---

# Production Best Practices

- ✅ Use an S3 backend
- ✅ Enable encryption
- ✅ Enable versioning
- ✅ Use DynamoDB state locking
- ✅ Never commit `terraform.tfstate` to GitHub
- ✅ Mark secrets as sensitive
- ✅ Separate environments:
  - dev
  - staging
  - prod
- ✅ Split large infrastructures into multiple state files

---

# Phase 10 Project

## Objective

Build a production-ready Terraform backend.

Architecture:

```text
AWS
│
├── S3 Bucket
│     ├── Terraform State
│     ├── Encryption
│     └── Versioning
│
└── DynamoDB Table
      └── State Locking
```

---

## Resources to Create

- `aws_s3_bucket`
- `aws_s3_bucket_versioning`
- `aws_dynamodb_table`

---

## Configure Backend

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state-zai"
    key            = "prod/terraform.tfstate"
    region         = "ap-southeast-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

---

# Goal

After completing this phase, you'll understand how professional Cloud Engineers and DevOps teams safely manage Terraform state in production using:

- Remote State
- S3 Backend
- Encryption
- Versioning
- DynamoDB State Locking
- State Management Commands
- Production Best Practices
