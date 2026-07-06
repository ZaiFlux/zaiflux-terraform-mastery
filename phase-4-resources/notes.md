# PHASE 4 — Resources (2 Weeks)

## Objective
Master how Terraform manages infrastructure through resources, dependencies, and meta-arguments.

---

# Topics

- Resources
- Lifecycle
- Dependencies
- Meta Arguments

---

# Study

## `depends_on`

Learn when Terraform needs manual dependency definitions.

### Example

```hcl
resource "aws_instance" "web" {
  depends_on = [
    aws_internet_gateway.igw
  ]
}
```

### Understand

- Explicit dependency
- Why Terraform sometimes cannot infer relationships

---

## `count`

Create multiple identical resources.

### Example

```hcl
resource "aws_instance" "web" {
  count = 3

  ami           = "ami-xxxx"
  instance_type = "t2.micro"
}
```

### Produces

```text
aws_instance.web[0]
aws_instance.web[1]
aws_instance.web[2]
```

---

## `for_each`

Create resources from maps or sets.

### Example

```hcl
resource "aws_subnet" "public" {

  for_each = {
    subnet1 = "10.0.1.0/24"
    subnet2 = "10.0.2.0/24"
  }

  cidr_block = each.value
}
```

### Produces

```text
aws_subnet.public["subnet1"]
aws_subnet.public["subnet2"]
```

---

## `provider`

Configure cloud providers.

### Example

```hcl
provider "aws" {
  region = "ap-southeast-1"
}
```

### Understand

- Provider configuration
- Multiple providers
- Provider aliases

---

## `lifecycle`

Control resource behavior.

### Prevent Destruction

```hcl
lifecycle {
  prevent_destroy = true
}
```

### Replace Safely

```hcl
lifecycle {
  create_before_destroy = true
}
```

### Ignore Drift

```hcl
lifecycle {
  ignore_changes = [
    tags
  ]
}
```

---

# Concepts to Understand

## Implicit Dependency

Terraform automatically detects dependencies.

```hcl
vpc_id = aws_vpc.main.id
```

Terraform understands:

```text
VPC
↓
Subnet
```

No `depends_on` required.

---

## Explicit Dependency

Terraform needs help.

```hcl
depends_on = [
  aws_internet_gateway.igw
]
```

Terraform now forces ordering.

---

## Creation Order

Terraform builds infrastructure in dependency order.

```text
VPC
↓
Subnet
↓
IGW
↓
Route Table
↓
Security Group
↓
EC2
```

---

## Destroy Order

Terraform destroys in reverse order.

```text
EC2
↓
Security Group
↓
Route Table
↓
IGW
↓
Subnet
↓
VPC
```

---

## Resource Graph

Terraform internally creates a graph.

```text
VPC
├── Subnet A
├── Subnet B
├── IGW
├── Route Table
├── Security Group
└── EC2
```

Terraform uses this graph to determine:

- What to create first
- What can run in parallel
- What to destroy first

---

# Project

Build everything yourself.

```text
VPC
├── Public Subnet 1
├── Public Subnet 2
├── Internet Gateway
├── Route Table
├── Route Association
├── Security Group
└── EC2
```

---

# Practice Routine

## First Build

```bash
terraform init
terraform plan
terraform apply
```

---

## Destroy

```bash
terraform destroy
```

Observe the destroy order.

---

## Rebuild

```bash
terraform apply
```

---

## Repeat

Keep rebuilding until you can answer:

- Why was this created first?
- Why was this destroyed last?
- Is this implicit or explicit dependency?
- When should I use `count`?
- When should I use `for_each`?
- When should I use `depends_on`?

---

# Mastery Checklist

- [ ] Resources
- [ ] depends_on
- [ ] count
- [ ] for_each
- [ ] provider
- [ ] lifecycle
- [ ] Implicit dependency
- [ ] Explicit dependency
- [ ] Creation order
- [ ] Destroy order
- [ ] Resource graph
- [ ] VPC
- [ ] Subnets
- [ ] IGW
- [ ] Route Tables
- [ ] Security Groups
- [ ] EC2
- [ ] Destroy
- [ ] Rebuild
- [ ] Repeat

---

## Goal

Complete this checklist without notes, and you've mastered **Phase 4 – Resources**.
