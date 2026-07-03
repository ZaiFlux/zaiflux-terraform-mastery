# Phase 1 - Terraform Introduction

# Terraform Overview

## What is Terraform?

Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp.

It allows engineers to define, provision, and manage cloud infrastructure using code instead of manually creating resources through web interfaces.

Terraform uses declarative configuration files written in HCL (HashiCorp Configuration Language) to describe the desired state of infrastructure.

### Example resources managed by Terraform

- Virtual machines
- Networks and subnets
- Storage buckets
- Databases
- Load balancers
- Kubernetes clusters

---

# Infrastructure as Code (IaC)

Infrastructure as Code is the practice of managing and provisioning infrastructure through configuration files rather than manual processes.

Instead of clicking through cloud dashboards, infrastructure is described in code files that can be:

- Stored in Git repositories
- Reviewed by teams
- Version controlled
- Reused
- Automatically deployed

### Example

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

Terraform reads this code and creates the required infrastructure automatically.

---

# Configuration as Code (CaC)

Configuration as Code refers to managing system and application configurations using code.

Examples include:

- Server configurations
- Security settings
- User permissions
- Application parameters

Terraform primarily focuses on Infrastructure as Code, while tools such as:

- Ansible
- Chef
- Puppet

are commonly used for Configuration as Code.

Terraform can work together with these tools to fully automate environments.

---

# Benefits of Terraform

### Automation

Infrastructure can be created and updated automatically.

### Consistency

Every environment is built the same way, reducing configuration drift.

### Version Control

Infrastructure changes can be tracked using Git.

### Reusability

Configurations can be reused across multiple projects.

### Scalability

Deploy infrastructure quickly across many environments.

### Multi-Cloud Support

Terraform supports providers such as:

- Amazon Web Services (AWS)
- Microsoft Azure
- Google Cloud Platform (GCP)

using a single workflow.

---

# Use Cases

Terraform is commonly used for:

### Cloud Infrastructure Provisioning

Creating servers, databases, storage, and networking resources.

### Multi-Environment Deployment

Managing development, staging, and production environments.

### Disaster Recovery

Rebuilding infrastructure quickly from code.

### CI/CD Pipelines

Automating infrastructure deployment within DevOps workflows.

### Kubernetes Management

Provisioning clusters and related cloud resources.

---

# Why Terraform?

Terraform provides several advantages:

- Infrastructure is defined as code.
- Changes are repeatable and predictable.
- Teams can collaborate through version control.
- Infrastructure deployments become automated.
- Supports multiple cloud providers.
- `terraform plan` previews changes before deployment.
- Infrastructure can be recreated easily if failures occur.

### In short

Terraform makes infrastructure provisioning faster, safer, and easier to manage at scale.

---

# Why Not Use the AWS Console?

| AWS Console | Terraform |
|------------|-----------|
| Manual clicks | Automated deployments |
| Hard to track changes | Version controlled |
| Difficult to reproduce environments | Reproducible environments |
| Error-prone | Consistent deployments |
| Time-consuming | Faster provisioning |
| Limited collaboration | Team collaboration through Git |

Example:

```bash
terraform apply
```

Terraform creates all resources automatically.

---

# What Problems Does IaC Solve?

## 1. Manual Errors

Humans make mistakes when configuring resources manually.

IaC ensures infrastructure is deployed consistently every time.

## 2. Configuration Drift

Terraform detects differences and restores the desired state.

## 3. Lack of Documentation

Terraform code serves as living documentation.

## 4. Slow Provisioning

Terraform can provision environments in minutes.

## 5. Difficult Disaster Recovery

Infrastructure can be recreated directly from code.

## 6. Collaboration Challenges

Terraform configurations can be stored in Git repositories, reviewed through pull requests, and tracked over time.

---

# Summary

Terraform is an Infrastructure as Code tool that automates the provisioning and management of cloud infrastructure, enabling consistency, scalability, collaboration, and repeatable deployments while reducing manual errors and operational overhead.
