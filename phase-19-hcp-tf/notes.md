# PHASE 19 — HCP Terraform

## What is HCP Terraform?

**HCP Terraform** (formerly **Terraform Cloud**) is a cloud platform from HashiCorp that helps you manage, automate, and collaborate on Terraform infrastructure.

Instead of running everything from your own computer, you can manage your infrastructure through a centralized platform.

---

## Without HCP Terraform

```text
Your Laptop
      │
terraform apply
      │
      ▼
AWS
```

Everything happens on your local machine:

- State file is stored locally.
- You run `terraform plan` and `terraform apply`.
- Collaboration is difficult because others don't automatically share your state.

---

## With HCP Terraform

```text
Your Laptop
      │
      ▼
HCP Terraform
      │
      ▼
AWS
```

HCP Terraform becomes the central service that can:

- Store Terraform state remotely.
- Execute Terraform runs.
- Connect to GitHub.
- Manage multiple environments.
- Enable team collaboration.
- Secure secrets and variables.
- Enforce policies and approvals.

---

# Why Was HCP Terraform Created?

Imagine three Cloud Engineers working on the same AWS infrastructure.

## Without HCP Terraform

```text
Alice's Laptop

Bob's Laptop

Carol's Laptop
```

Each engineer has a different state file.

### Problems

- State file conflicts
- Accidental overwrites
- Difficult collaboration
- No centralized history
- Secrets may be stored locally

---

## With HCP Terraform

```text
Alice
      \
Bob ----> HCP Terraform ----> AWS
      /
Carol
```

### Benefits

- One shared remote state
- Version history of infrastructure changes
- Secure variable storage
- Team collaboration
- Consistent workflow

---

# The Five Topics in Phase 19

These topics build a complete HCP Terraform workflow.

---

# 1. Authentication

## Purpose

Prove your identity to HCP Terraform.

### You will learn how to:

- Create an HCP Terraform account
- Generate an API token
- Authenticate the Terraform CLI
- Connect your local machine to HCP Terraform

> **Note:** Without authentication, you cannot use HCP Terraform features.

---

# 2. Workspaces

## Purpose

Organize infrastructure into separate environments.

A workspace contains:

- Terraform state
- Variables
- Outputs
- Run history

### Example

```text
Organization
│
├── dev
├── staging
└── production
```

Each workspace manages its own infrastructure independently.

---

# 3. VCS Integration

## Purpose

Connect HCP Terraform to a Version Control System (VCS) such as GitHub.

### Workflow

```text
GitHub
   │
Push Code
   ▼
HCP Terraform
   │
Automatic Plan
   │
Review
   │
Apply
```

Every Git push can automatically trigger a Terraform run.

---

# 4. Enterprise Features

These capabilities help teams manage infrastructure at scale.

### Features

- Remote execution
- Remote state management
- Role-Based Access Control (RBAC)
- Variable sets
- Notifications
- Team collaboration
- Audit history

> These features are especially useful in production environments.

---

# 5. Run Tasks

Run Tasks let you insert automated checks into the Terraform workflow.

### Example Workflow

```text
Git Push
     │
Terraform Plan
     │
Run Task
     │
Security Check
     │
Cost Check
     │
Compliance Check
     │
Terraform Apply
```

This helps catch issues before infrastructure changes are applied.

---

# How the Topics Connect

```text
Authentication
        │
        ▼
Workspaces
        │
        ▼
VCS Integration
        │
        ▼
Enterprise Features
        │
        ▼
Run Tasks
```

Each topic builds on the previous one, creating a complete, production-ready Infrastructure as Code (IaC) workflow.

---

# Phase 19 Summary

By the end of this phase, you will understand how to:

- Authenticate with HCP Terraform
- Organize infrastructure using workspaces
- Integrate Terraform with GitHub
- Use enterprise collaboration features
- Automate validation with Run Tasks

These concepts form the foundation of a modern, production-ready Terraform workflow used by professional Cloud Engineers and DevOps teams.
