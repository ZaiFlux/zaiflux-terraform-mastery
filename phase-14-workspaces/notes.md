# PHASE 14 — Workspaces


---

# 📚 Topics

- Terraform Workspaces

---

# 🛠 Commands

```bash
terraform workspace list
terraform workspace new dev
terraform workspace select prod
terraform workspace show
terraform workspace delete dev
```

---

# 🎯 Goal

Learn how to manage multiple environments (Development, Staging, Production) using a **single Terraform configuration**.

Instead of creating separate folders like:

```text
terraform-dev/
terraform-prod/
terraform-staging/
```

Terraform allows you to use **Workspaces**.

```text
One Terraform Configuration
        │
        ├── dev Workspace
        ├── staging Workspace
        └── prod Workspace
```

Each workspace has its own **Terraform state file**, keeping environments isolated.

---

# Why Use Workspaces?

Imagine you're deploying the same infrastructure for:

- Development
- Testing
- Production

Without workspaces:

```text
project-dev/
project-stage/
project-prod/
```

You duplicate the same Terraform code.

With workspaces:

```text
project/
│
└── main.tf

Workspaces:
- default
- dev
- stage
- prod
```

Only one Terraform project is needed.

---

# How Workspaces Work

```text
Terraform Configuration
          │
          ▼
Current Workspace
          │
          ▼
Terraform State File
```

Example:

If current workspace is:

```text
dev
```

Terraform stores state in:

```text
terraform.tfstate.d/dev/
```

If current workspace is:

```text
prod
```

Terraform stores state in:

```text
terraform.tfstate.d/prod/
```

Resources remain completely separate.

---

# Step 1 — Create Project

```bash
mkdir terraform-phase14
cd terraform-phase14
```

Create:

```text
main.tf
```

---

# Step 2 — Configure AWS Provider

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

---

# Step 3 — Create a Simple Resource

```terraform
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

  tags = {
    Name = terraform.workspace
  }

}
```

Notice:

```terraform
terraform.workspace
```

This is a built-in Terraform variable.

If the workspace is:

```text
dev
```

Terraform creates:

```text
Name = dev
```

If the workspace is:

```text
prod
```

Terraform creates:

```text
Name = prod
```

This is useful for automatically naming resources based on the active environment.

---

# Step 4 — Initialize Terraform

```bash
terraform init
```

---

# Step 5 — List Existing Workspaces

```bash
terraform workspace list
```

Output:

```text
* default
```

The asterisk (`*`) indicates the active workspace.

---

# Step 6 — Create a Development Workspace

```bash
terraform workspace new dev
```

Output:

```text
Created and switched to workspace "dev".
```

Verify:

```bash
terraform workspace list
```

```text
default
* dev
```

---

# Step 7 — Create a Production Workspace

```bash
terraform workspace new prod
```

Check again:

```bash
terraform workspace list
```

```text
default
dev
* prod
```

---

# Step 8 — Switch Workspaces

Switch back to development:

```bash
terraform workspace select dev
```

Output:

```text
Switched to workspace "dev".
```

---

# Step 9 — Show Current Workspace

```bash
terraform workspace show
```

Output:

```text
dev
```

---

# Step 10 — Deploy Resources

Apply in the **dev** workspace:

```bash
terraform apply
```

Terraform creates:

```text
VPC Name = dev
```

Switch to production:

```bash
terraform workspace select prod
```

Apply again:

```bash
terraform apply
```

Terraform creates:

```text
VPC Name = prod
```

The same Terraform code is reused, but each workspace manages separate infrastructure.

---

# Terraform State Files

Terraform automatically creates separate state files.

```text
terraform.tfstate
terraform.tfstate.d/
```

Inside:

```text
terraform.tfstate.d/
├── dev/
│   └── terraform.tfstate
└── prod/
    └── terraform.tfstate
```

Each workspace has its own state file.

---

# Common Workspace Commands

## List Workspaces

```bash
terraform workspace list
```

---

## Create Workspace

```bash
terraform workspace new dev
```

---

## Select Workspace

```bash
terraform workspace select prod
```

---

## Show Current Workspace

```bash
terraform workspace show
```

---

## Delete Workspace

```bash
terraform workspace delete dev
```

> **Note:** You cannot delete the workspace you are currently using. Switch to another workspace first.

---

# Real-World Example

Cloud engineers commonly use workspaces like this:

| Workspace | Purpose |
|------------|----------|
| **dev** | Development and testing |
| **staging** | Final testing before production |
| **prod** | Live production environment |

One Terraform configuration manages multiple isolated environments.

---

# Project Structure

```text
terraform-phase14/
│
├── main.tf
├── .terraform/
├── .terraform.lock.hcl
├── terraform.tfstate
├── terraform.tfstate.backup
└── terraform.tfstate.d/
    ├── dev/
    │   └── terraform.tfstate
    └── prod/
        └── terraform.tfstate
```

---

# Summary

After completing this phase, you should be able to:

- Initialize a Terraform project.
- Create multiple workspaces.
- List available workspaces.
- Switch between workspaces.
- View the active workspace.
- Use `terraform.workspace` in Terraform code.
- Deploy the same infrastructure to different environments.
- Understand how Terraform isolates state files using workspaces.

---

# Key Takeaways

- One Terraform configuration can manage multiple environments.
- Each workspace maintains its own state file.
- `terraform.workspace` allows dynamic resource naming.
- Workspaces reduce code duplication.
- Common environments include **dev**, **staging**, and **prod**.
