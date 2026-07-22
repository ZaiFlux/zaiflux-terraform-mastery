# Phase 17 — Scaling Terraform

**Estimated Duration:** 1 Week

## Overview

This phase focuses on using Terraform in large, production environments where infrastructure consists of many resources, multiple engineers collaborate, and deployments are automated through CI/CD pipelines.

---

# 1. Splitting Large States

## What It Is

Instead of storing all infrastructure in a single Terraform state file (`terraform.tfstate`), divide the infrastructure into multiple state files.

## Why It's Needed

A single state file becomes difficult to manage when it contains hundreds or thousands of resources.

Common problems include:

- Slow `terraform plan`
- Slow `terraform apply`
- Higher risk of accidental changes
- Merge conflicts in team environments
- Entire infrastructure depends on one state file

## Example

Instead of:

```text
terraform-project/
└── terraform.tfstate
```

Use:

```text
network/
└── terraform.tfstate

compute/
└── terraform.tfstate

database/
└── terraform.tfstate

storage/
└── terraform.tfstate
```

Each component is managed independently.

---

# 2. Parallelism

## What It Is

Terraform creates resources simultaneously whenever there are no dependencies between them.

## Example

```text
VPC
├── Subnet A
├── Subnet B
├── Subnet C
└── Subnet D
```

Since each subnet depends only on the VPC (and not on each other), Terraform creates all four subnets in parallel.

## Default Behavior

Terraform creates up to **10 resources simultaneously**.

```bash
terraform apply
```

## Change the Parallelism Limit

```bash
terraform apply -parallelism=20
```

This allows Terraform to create up to **20 resources** at the same time.

---

# 3. Deployment Workflow

## What It Is

A standard process for safely deploying infrastructure.

Instead of directly running:

```bash
terraform apply
```

Professional teams follow a structured deployment workflow.

## Deployment Workflow

```text
Write Code
      │
      ▼
terraform fmt
      │
      ▼
terraform validate
      │
      ▼
terraform plan
      │
      ▼
Code Review
      │
      ▼
CI/CD Pipeline
      │
      ▼
terraform apply
```

## Purpose

- Catch formatting issues
- Validate Terraform configuration
- Preview infrastructure changes
- Review changes before deployment
- Reduce production mistakes

---

# 4. Terragrunt

## What It Is

Terragrunt is a wrapper around Terraform that helps manage multiple Terraform projects by reducing duplicated configuration.

## Without Terragrunt

Every project contains repeated configuration files such as:

```text
provider.tf
backend.tf
versions.tf
variables.tf
```

These files are duplicated across multiple projects and environments.

## With Terragrunt

Shared configuration is written once and inherited by each environment.

Example:

```text
live/
├── dev/
├── staging/
└── prod/

modules/
├── network/
├── compute/
└── database/
```

Each environment inherits the common configuration.

## Benefits

- Less duplicated code
- Easier remote state management
- Simpler multi-environment deployments
- Cleaner project structure

---

# 5. Infracost

## What It Is

Infracost is a tool that estimates the cost of Terraform infrastructure before deployment.

Terraform tells you which resources will be created or modified:

```text
Resources to add:
+ EC2
+ RDS
```

However, Terraform does **not** estimate the cloud cost.

Infracost analyzes the Terraform plan and estimates the expected monthly cost.

## Example

```text
Current Monthly Cost:
$120

New Monthly Cost:
$185

Difference:
+$65/month
```

## Benefits

- Estimate cloud costs before deployment
- Prevent unexpected expenses
- Integrate cost checks into CI/CD pipelines

---

# Summary

| Topic | Purpose |
|--------|---------|
| **Splitting Large States** | Divide infrastructure into smaller, independent Terraform state files. |
| **Parallelism** | Create independent resources simultaneously to speed up deployments. |
| **Deployment Workflow** | Use a safe process (`fmt → validate → plan → review → apply`) for infrastructure changes. |
| **Terragrunt** | Simplify management of multiple Terraform projects and environments by reducing duplicated configuration. |
| **Infracost** | Estimate cloud infrastructure costs from Terraform plans before applying changes. |

---

## Conclusion

These five topics form the core of **Phase 17 – Scaling Terraform** and introduce practices commonly used in real-world, enterprise Terraform deployments. By understanding state management, resource parallelism, deployment workflows, Terragrunt, and Infracost, you will be better prepared to manage large-scale infrastructure efficiently, collaborate with teams, and maintain reliable, cost-aware cloud environments.
