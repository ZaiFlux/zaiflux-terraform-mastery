# Phase 9 — Terraform Deployment Deep Dive

## Goal

Learn exactly how Terraform thinks while deploying infrastructure.

### Architecture

```text
VPC
│
└── Public Subnet
      │
      └── Outputs
```

> **Note:** No EC2 instances yet. The goal is to focus purely on;
>
> - `terraform init`
> - `terraform plan`
> - `terraform graph`
> - `terraform apply`
> - `terraform show`
> - `terraform output`
> - `terraform destroy`

---

# Lab 1 — Create the Project

Create a new project folder:

```bash
mkdir terraform-phase9
cd terraform-phase9
```

Create the Terraform configuration file:

```bash
nano main.tf
```

Paste the following configuration:

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
  region = "ap-southeast-1"
}

resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "phase9-vpc"
  }

}

resource "aws_subnet" "public" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.1.0/24"

  availability_zone = "ap-southeast-1a"

  tags = {
    Name = "phase9-public"
  }

}

output "vpc_id" {
  value = aws_vpc.main.id
}

output "subnet_id" {
  value = aws_subnet.public.id
}
```

Save the file:

```text
CTRL + O
ENTER
CTRL + X
```

---

# Lab 2 — Initialize Terraform

Run:

```bash
terraform init
```

## What happened?

Terraform downloaded:

- AWS Provider
- Required dependencies
- Lock file

Check the directory:

```bash
ls -la
```

Expected output:

```text
.terraform/
.terraform.lock.hcl
main.tf
```

---

# Lab 3 — Create an Execution Plan

Run:

```bash
terraform plan
```

Expected:

```text
Terraform will perform the following actions:

+ aws_vpc.main
+ aws_subnet.public

Plan:
2 to add
0 to change
0 to destroy
```

**Do not apply yet.**

### Question

**Why is the subnet listed second?**

Because:

```hcl
vpc_id = aws_vpc.main.id
```

Terraform automatically discovers that the subnet depends on the VPC.

Internally it builds this dependency graph:

```text
VPC
 │
 ▼
Subnet
```

This is Terraform's dependency graph.

---

# Lab 4 — View the Dependency Graph

Run:

```bash
terraform graph
```

Example output:

```text
digraph {

aws_vpc.main

aws_subnet.public

}
```

Terraform is showing:

```text
VPC
 │
 ▼
Subnet
```

### Optional Visualization

Install Graphviz:

```bash
sudo apt install graphviz
```

Generate an image:

```bash
terraform graph | dot -Tpng > graph.png
```

Verify:

```bash
ls
```

Expected:

```text
graph.png
```

Open the image to visualize the dependency graph.

---

# Lab 5 — Deploy the Infrastructure

Run:

```bash
terraform apply
```

Terraform asks:

```text
Do you want to perform these actions?
```

Type:

```text
yes
```

Observe the creation order:

1. `aws_vpc.main`
2. `aws_subnet.public`

### Question

**Why is the VPC created first?**

Because Terraform already computed the dependency graph:

```text
VPC
 │
 ▼
Subnet
```

The dependency graph controls deployment order.

---

# Lab 6 — Inspect the State File

Check the directory:

```bash
ls
```

You should now see:

```text
terraform.tfstate
```

Open the file:

```bash
cat terraform.tfstate
```

or

```bash
nano terraform.tfstate
```

Inside you'll find entries similar to:

```text
aws_vpc.main

id = vpc-xxxxxxxx

aws_subnet.public

id = subnet-xxxxxxxx
```

Terraform now remembers:

- The VPC it created
- The VPC ID
- The subnet it created
- The subnet ID

Think of `terraform.tfstate` as Terraform's memory.

---

# Lab 7 — View Outputs

Display all outputs:

```bash
terraform output
```

Example:

```text
vpc_id = vpc-12345
subnet_id = subnet-56789
```

Display a single output:

```bash
terraform output vpc_id
```

Example:

```text
vpc-12345
```

### Why use outputs?

Outputs expose important values for:

- Other Terraform modules
- Other engineers
- Automation pipelines

Common outputs include:

- VPC ID
- Subnet ID
- Public IP
- DNS Name
- ARN

---

# Lab 8 — Inspect the Current State

Run:

```bash
terraform show
```

Terraform displays everything it currently knows.

Example:

```text
resource "aws_vpc" "main"

id = vpc-12345

cidr_block = 10.0.0.0/16

resource "aws_subnet" "public"

id = subnet-67890
```

Remember:

> `terraform show` = Inspect the current Terraform state.

---

# Lab 9 — Modify the Infrastructure

Edit the subnet CIDR block:

Change:

```hcl
cidr_block = "10.0.1.0/24"
```

to

```hcl
cidr_block = "10.0.2.0/24"
```

Save the file.

Run:

```bash
terraform plan
```

Terraform may display:

```text
~ aws_subnet.public

cidr_block

10.0.1.0/24

↓

10.0.2.0/24
```

or

```text
-/+ aws_subnet.public
```

Meaning:

- Destroy the existing subnet
- Create a new subnet

Terraform detected a difference between:

- Desired State
- Actual State

---

# Lab 10 — Destroy the Infrastructure

Run:

```bash
terraform destroy
```

Terraform shows:

```text
Destroy aws_subnet.public

Destroy aws_vpc.main

Plan:

0 to add
0 to change
2 to destroy
```

Notice the order.

### Creation

```text
VPC
 │
 ▼
Subnet
```

### Deletion

```text
Subnet
 │
 ▼
VPC
```

Terraform reverses the dependency graph because a subnet cannot exist without its VPC.

---

# Challenge Lab

Add an Internet Gateway:

```hcl
resource "aws_internet_gateway" "igw" {

  vpc_id = aws_vpc.main.id

}
```

Run:

```bash
terraform plan
```

Ask yourself:

Which dependency graph does Terraform build?

Option A:

```text
VPC
 │
 ├── Internet Gateway
 │
 └── Subnet
```

or Option B:

```text
VPC
 │
 ▼
Subnet
 │
 ▼
Internet Gateway
```

Explain **why** Terraform chose that order.

---

# Phase 9 Daily Practice

## Day 1

```bash
terraform init
terraform plan
```

---

## Day 2

```bash
terraform apply
terraform output
```

---

## Day 3

```bash
terraform show
terraform graph
```

---

## Day 4

Modify the infrastructure.

```bash
terraform plan
```

Observe the changes.

---

## Day 5

Add another resource.

```bash
terraform graph
```

Understand the updated dependency graph.

---

## Day 6

Destroy the infrastructure.

```bash
terraform destroy
```

Observe the reverse dependency order.

---

## Day 7 — Self-Assessment

Explain these concepts aloud without looking at your notes:

1. What is an execution plan?
2. What is a dependency graph?
3. What is `terraform.tfstate`?
4. Why does Terraform create the VPC before the subnet?
5. Why does Terraform destroy the subnet before the VPC?

---

# Success Criteria

If you can confidently explain each of these concepts from memory, you've truly learned **Phase 9**—not just memorized Terraform commands.
