# Phase 3 — HCL Fundamentals

### Goal
Become comfortable writing Terraform configurations from scratch without relying on tutorials.

**Estimated Time:** 5–7 Days

---

# 1. What is HCL?

**HCL** stands for **HashiCorp Configuration Language**.

Terraform uses HCL to describe infrastructure in a human-readable format.

### Example

```hcl
resource "aws_instance" "web" {

  ami           = "ami-123456"
  instance_type = "t2.micro"

}
```

Read it as:

- Create an AWS EC2 instance named **web**
- Use this AMI: `ami-123456`
- Set the instance type to `t2.micro`

---

# 2. HCL Syntax

Terraform configurations are composed of **blocks**.

### General Syntax

```hcl
block_type "label1" "label2" {

  argument = value

}
```

### Example

```hcl
resource "aws_instance" "web" {

  ami = "ami-123456"

  instance_type = "t2.micro"

}
```

### Components

| Element | Description |
|---------|-------------|
| `resource` | Block type |
| `aws_instance` | Resource type |
| `web` | Local resource name |

### Supported Value Types

#### String

```hcl
name = "server1"
```

#### Number

```hcl
count = 3
```

#### Boolean

```hcl
enabled = true
```

#### List

```hcl
ports = [80, 443, 8080]
```

#### Map

```hcl
tags = {
  Environment = "dev"
  Owner       = "Zai"
}
```

---

# 3. Variables

Variables make Terraform code reusable.

Instead of hardcoding values:

```hcl
resource "aws_instance" "web" {

  instance_type = "t2.micro"

}
```

Define a variable:

```hcl
variable "instance_type" {

  type    = string
  default = "t2.micro"

}
```

Reference it inside a resource:

```hcl
resource "aws_instance" "web" {

  instance_type = var.instance_type

}
```

Terraform variables use the syntax:

```hcl
var.<name>
```

Examples:

```hcl
var.region
var.environment
var.instance_type
```

## Variable Types

### String

```hcl
variable "region" {

  type    = string
  default = "us-east-1"

}
```

### Number

```hcl
variable "instance_count" {

  type    = number
  default = 2

}
```

### Boolean

```hcl
variable "enable_monitoring" {

  type    = bool
  default = true

}
```

### List

```hcl
variable "ports" {

  type = list(number)

  default = [
    80,
    443
  ]

}
```

### Map

```hcl
variable "tags" {

  type = map(string)

  default = {

    Environment = "dev"
    Team        = "Cloud"

  }

}
```

---

# 4. Outputs

Outputs display values after Terraform provisions infrastructure.

### Example

```hcl
output "instance_id" {

  value = aws_instance.web.id

}
```

Terraform displays:

```text
instance_id = i-0ab12345cd678
```

### Another Example

```hcl
output "public_ip" {

  value = aws_instance.web.public_ip

}
```

Result:

```text
public_ip = 54.12.34.56
```

### Common Uses for Outputs

- EC2 Instance IDs
- Public IP Addresses
- VPC IDs
- DNS Names
- Load Balancer URLs

---

# 5. Locals

Locals store values that are reused multiple times.

### Without Locals

```hcl
tags = {

  Environment = "dev"

}
```

### With Locals

```hcl
locals {

  environment = "dev"

}
```

Reference locals using:

```hcl
local.<name>
```

Examples:

```hcl
local.environment
local.project
local.owner
```

### Example

```hcl
locals {

  environment = "dev"

  project = "portfolio"

}

resource "aws_instance" "web" {

  tags = {

    Environment = local.environment

    Project = local.project

  }

}
```

---

# Mini Practice Project

Create a file called:

```text
main.tf
```

Add:

```hcl
variable "region" {

  type    = string
  default = "us-east-1"

}

locals {

  environment = "dev"

}

output "deployment_info" {

  value = "${var.region}-${local.environment}"

}
```

Run:

```bash
terraform init

terraform plan
```

Expected output:

```text
deployment_info = "us-east-1-dev"
```

---

# Key Takeaways

| Concept | Syntax |
|---------|--------|
| Variables | `var.name` |
| Locals | `local.name` |
| Resource Attributes | `aws_instance.web.id` |
| Outputs | `output "name"` |
| List | `[1,2,3]` |
| Map | `{ key = value }` |
| String Interpolation | `"${var.region}-${local.environment}"` |
