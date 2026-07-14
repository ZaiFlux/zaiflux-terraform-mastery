# PHASE 2 — Installation & Environment Setup (Terraform)

---

## 1. Installing Terraform

The easiest way to install Terraform is from the official website:

**Terraform Downloads**

### Verify Installation

Open your terminal and run:

```bash
terraform version
```

Expected output:

```bash
Terraform v1.x.x
on windows_amd64
```

If you see the version number, Terraform is installed successfully.

---

## 2. Terraform Registry

The Terraform Registry is a public repository containing:

- Providers
- Modules
- Documentation

Think of it as an **App Store for Terraform providers**.

Official Registry:

**Terraform Registry**

Example providers:

- AWS
- Azure
- Google Cloud
- Kubernetes
- Docker

AWS Provider page:

**AWS Provider Registry**

---

## 3. Providers

A provider is a plugin that allows Terraform to communicate with a platform or service.

### Examples

| Provider | Purpose |
|----------|---------|
| AWS | Manage AWS resources |
| Azure | Manage Azure resources |
| Google | Manage GCP resources |
| Kubernetes | Manage clusters |
| Docker | Manage containers |

Terraform itself doesn't know how to create EC2 instances.

The AWS Provider teaches Terraform how to interact with AWS APIs.

### Example

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

---

## 4. Plugins

Providers are downloaded as plugins.

When you execute:

```bash
terraform init
```

Terraform downloads the AWS provider plugin automatically.

Example:

```bash
Initializing provider plugins...
Installing hashicorp/aws...
Installed hashicorp/aws v6.x.x
```

---

## 5. Provider Versions

Terraform allows version constraints.

### Examples

#### Exact version

```hcl
version = "6.1.0"
```

#### Any 6.x version

```hcl
version = "~> 6.0"
```

#### Greater than or equal

```hcl
version = ">= 6.0"
```

#### Between versions

```hcl
version = ">= 5.0, < 7.0"
```

Using versions is important because provider updates can introduce breaking changes.

---

## 6. Project Initialization

Create a project folder:

```bash
mkdir terraform-lab
cd terraform-lab
```

Create a file named:

```text
main.tf
```

Inside it:

```hcl
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

## 7. Initialize Terraform

Run:

```bash
terraform init
```

Terraform will:

- ✔ Read configuration files
- ✔ Check required providers
- ✔ Download provider plugins
- ✔ Create the `.terraform` directory
- ✔ Generate `.terraform.lock.hcl`

Example output:

```bash
Initializing the backend...
Initializing provider plugins...

- Finding hashicorp/aws versions...
- Installing hashicorp/aws...
- Installed hashicorp/aws

Terraform has been successfully initialized!
```

---

## 8. Understanding `.terraform`

After initialization:

```text
terraform-lab/
├── main.tf
├── .terraform/
└── .terraform.lock.hcl
```

The `.terraform` folder contains:

- Downloaded providers
- Plugins
- Metadata
- Caches

Example:

```text
.terraform/
└── providers/
```

You normally do **not** edit this folder manually.

It is often excluded from Git repositories.

Example `.gitignore`:

```gitignore
.terraform/
```

---

## 9. Understanding `.terraform.lock.hcl`

Terraform automatically creates:

```text
.terraform.lock.hcl
```

This file records:

- Provider versions
- Checksums
- Dependency information

Example:

```hcl
provider "registry.terraform.io/hashicorp/aws" {
  version = "6.2.0"
}
```

### Benefits

- Consistent deployments
- Reproducible environments
- Team members use identical provider versions

Similar to:

- `package-lock.json` in Node.js
- `requirements.txt` in Python

---

# Mini Project — Install AWS Provider

## Step 1

Create a folder:

```bash
mkdir terraform-aws-lab
cd terraform-aws-lab
```

## Step 2

Create `main.tf`

```hcl
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

## Step 3

Check Terraform installation:

```bash
terraform version
```

## Step 4

Initialize the project:

```bash
terraform init
```

Expected result:

```bash
Terraform has been successfully initialized!
```

---

# Commands to Remember

### `terraform version`

Displays the installed Terraform version.

```bash
terraform version
```

### `terraform init`

Initializes the project and downloads providers.

```bash
terraform init
```

---


By the end of this phase, you should be able to answer:

- What is a Terraform Provider?
- What is the Terraform Registry?
- What happens during `terraform init`?
- What is the purpose of `.terraform`?
- Why do we use `.terraform.lock.hcl`?
- Why should provider versions be pinned?
