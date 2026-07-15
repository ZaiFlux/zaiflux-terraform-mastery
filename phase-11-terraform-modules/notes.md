# PHASE 11 — Terraform Modules

**Estimated Time:** 2 Weeks  
**Difficulty:** 🔴

---

# Learning Objectives

By the end of this phase, you should be able to:

- Explain what a Terraform module is.
- Distinguish between a Root Module and a Child Module.
- Understand what Published Modules are.
- Pass values to modules using Inputs.
- Return values from modules using Outputs.
- Follow Terraform module best practices.
- Understand the standard folder structure for modular Terraform projects.

---

# 1. What is a Module?

## Definition

A **Terraform Module** is a collection of Terraform configuration files (`.tf` files) stored in a directory that performs a specific task.

Instead of placing every resource inside one large `main.tf`, modules organize related resources into separate, reusable components.

For example, instead of writing VPC code in every project, you can create one **Network Module** and reuse it whenever networking resources are needed.

Think of a module as a **reusable building block**.

### Example

```text
Network Module
├── Creates VPC
├── Creates Subnet
├── Creates Route Table
└── Creates Internet Gateway
```

Whenever another project needs networking resources, simply reuse the **Network Module** instead of rewriting the code.

---

## Why Use Modules?

### Without Modules

- Code becomes very long.
- Code is duplicated across projects.
- Maintenance becomes difficult.

### With Modules

- Code is organized.
- Code is reusable.
- Projects are easier to maintain.
- Teams can work on different modules independently.

---

# 2. Root Module

## Definition

The **Root Module** is the main Terraform project directory where Terraform commands are executed.

Whenever you run:

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

you are running them inside the **Root Module**.

---

## Example

```text
terraform-project/
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

This entire folder is the **Root Module**.

---

## Responsibilities of the Root Module

The Root Module:

- Starts the Terraform deployment.
- Calls Child Modules.
- Passes input values to Child Modules.
- Receives outputs from Child Modules.
- Connects all modules together.

Think of the Root Module as the **project manager**.

It does not necessarily create every resource itself.

Instead, it tells other modules what to create.

---

## Example

```hcl
module "network" {
  source = "./modules/network"
}
```

This tells Terraform:

> "Go to the `modules/network` folder and execute the Terraform code located there."

---

## Interview Question

### Question

**What is a Root Module?**

### Answer

The Root Module is the main Terraform project directory where Terraform commands are executed. It serves as the entry point of the Terraform configuration and calls Child Modules.

---

# 3. Child Module

## Definition

A **Child Module** is a reusable Terraform module that is called from the Root Module.

Unlike the Root Module, a Child Module is **not executed directly**.

Instead, the Root Module tells Terraform to use it.

---

## Example

```text
modules/
└── network/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

The **network** folder is a Child Module.

---

## Responsibilities

A Child Module is responsible for **one specific task**.

### Network Module

- Create VPC
- Create Subnets
- Create Internet Gateway

### Security Module

- Create Security Groups
- Configure Firewall Rules

### EC2 Module

- Launch EC2 Instances

Each Child Module should perform **one responsibility only**.

---

## Example

### Root Module

```hcl
module "network" {
  source = "./modules/network"
}
```

Terraform performs the following steps:

```text
Root Module
      │
      ▼
Open modules/network
      │
      ▼
Read main.tf
      │
      ▼
Create AWS resources
      │
      ▼
Return outputs
```

---

## Interview Question

### Question

**What is a Child Module?**

### Answer

A Child Module is a reusable Terraform configuration that is called by the Root Module to create a specific set of infrastructure resources.

---

# 4. Published Modules

## Definition

A **Published Module** is a reusable module that is shared publicly through the **Terraform Registry** or another remote source.

Instead of writing every module yourself, you can use modules created by other developers or organizations.

---

## Terraform Registry

Terraform provides an official registry containing thousands of modules.

Examples include modules for:

- AWS VPC
- EC2
- RDS
- IAM
- ALB
- EKS

These modules are maintained by the community or by organizations.

---

## Example

Instead of:

```hcl
source = "./modules/network"
```

you can use:

```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
}
```

Terraform automatically downloads the module from the Terraform Registry.

---

## Advantages

Published Modules:

- Save development time.
- Follow Terraform best practices.
- Reduce repetitive coding.
- Are tested by the community.

---

## Interview Question

### Question

**What is a Published Module?**

### Answer

A Published Module is a reusable Terraform module that is stored in the Terraform Registry or another remote repository and can be downloaded and used in Terraform projects.

---

# 5. Inputs

## Definition

**Inputs** are variables passed from the Root Module to a Child Module.

They allow modules to be flexible instead of using hardcoded values.

---

## Without Inputs

```hcl
cidr_block = "10.0.0.0/16"
```

The module can only create one VPC.

---

## With Inputs

### Child Module

```hcl
variable "cidr_block" {}
```

### Root Module

```hcl
module "network" {
  source     = "./modules/network"
  cidr_block = "10.0.0.0/16"
}
```

### Resource

```hcl
cidr_block = var.cidr_block
```

---

## Flow

```text
Root Module
      │
      ▼
Input Value
      │
      ▼
Child Module Variable
      │
      ▼
AWS Resource
```

---

## Benefits

Inputs make modules:

- Reusable
- Flexible
- Configurable

---

## Interview Question

### Question

**What are Inputs?**

### Answer

Inputs are variables passed from the Root Module into a Child Module so the module can use different values without modifying its code.

---

# 6. Outputs

## Definition

**Outputs** are values returned by a Child Module after Terraform creates the resources.

Outputs allow other modules or the Root Module to use those values.

---

## Example

The Network Module creates a VPC.

AWS assigns a unique VPC ID.

Example:

```text
vpc-0123456789abcdef
```

The Child Module returns this value.

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

The Root Module can then access it.

```hcl
module.network.vpc_id
```

---

## Flow

```text
Child Module
      │
      ▼
Creates VPC
      │
      ▼
Gets VPC ID
      │
      ▼
Returns Output
      │
      ▼
Root Module Receives Output
```

---

## Benefits

Outputs allow modules to:

- Share information.
- Connect with other modules.
- Avoid manually looking up resource IDs.

---

## Interview Question

### Question

**What are Outputs?**

### Answer

Outputs are values returned by a Child Module after resources are created. They allow the Root Module or other modules to use information such as IDs, IP addresses, or DNS names.

---

# 7. Best Practices

Professional Terraform projects follow these best practices.

---

## 1. One Module, One Responsibility

Each module should focus on one purpose.

Examples:

- Network Module → Networking
- EC2 Module → Compute
- IAM Module → Identity
- RDS Module → Database

---

## 2. Avoid Hardcoding Values

Instead of:

```hcl
instance_type = "t2.micro"
```

use variables.

```hcl
variable "instance_type" {}
```

---

## 3. Use Outputs

Return important information such as:

- VPC IDs
- Security Group IDs
- Instance IDs
- Public IP Addresses

---

## 4. Keep the Root Module Simple

The Root Module should mainly:

- Call modules.
- Pass inputs.
- Receive outputs.

Avoid placing hundreds of resource blocks directly in the Root Module.

---

## 5. Organize Modules Properly

Store reusable modules inside a dedicated `modules` directory.

```text
modules/
├── network/
├── security/
├── ec2/
├── iam/
├── alb/
└── rds/
```

---

## 6. Reuse Modules

Do not copy and paste Terraform code between projects.

Instead, reuse existing modules whenever possible.

---

# Standard Folder Structure

A professional Terraform project often uses the following structure:

```text
terraform/
│
├── modules/
│   ├── network/
│   ├── security/
│   ├── ec2/
│   └── database/
│
├── environments/
│   ├── dev/
│   ├── staging/
│   └── prod/
│
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

---

## Folder Purposes

| Folder | Purpose |
|---------|----------|
| `terraform/` | The main project (Root Module). |
| `modules/` | Stores reusable Child Modules. |
| `network/` | Code for VPC, Subnets, Route Tables, Internet Gateway. |
| `security/` | Code for Security Groups and firewall rules. |
| `ec2/` | Code for EC2 instances. |
| `database/` | Code for RDS and database resources. |
| `environments/` | Stores configurations for different deployment environments. |
| `dev/` | Development environment. |
| `staging/` | Testing environment before production. |
| `prod/` | Production environment used by end users. |

---

# Phase 11 Project Goal

To apply everything you've learned, you'll build reusable modules for:

- **VPC Module** – Creates networking resources.
- **EC2 Module** – Launches EC2 instances.
- **IAM Module** – Creates IAM roles, policies, and instance profiles.
- **ALB Module** – Creates an Application Load Balancer.
- **RDS Module** – Creates a relational database.

Each module will:

- Be reusable.
- Accept Inputs.
- Return Outputs.
- Be called from the Root Module.

The result will be a modular Terraform project similar to what is used in professional cloud engineering environments.
