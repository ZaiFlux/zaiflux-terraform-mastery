# 🧠 PHASE 5 — Terraform Variables (Reusability Core)

## 💡 Why Variables Matter

Without variables:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

### Problem

- Fixed values
- Not reusable
- Hard to scale

With variables:

```hcl
instance_type = var.instance_type
```

Now you can reuse the same code for:

- Dev
- Staging
- Production

---

# 1. 📦 Input Variables

## 🔹 Basic Syntax

```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

### 🔍 Meaning

| Component | Purpose |
|-----------|---------|
| `variable` | Declares an input |
| `"instance_type"` | Variable name |
| `type` | Data type |
| `default` | Fallback value |

---

## 🔹 Using the Variable

```hcl
resource "aws_instance" "web" {
  instance_type = var.instance_type
}
```

Accessing a variable:

```hcl
var.instance_type
```

---

# 2. 🧬 Type Constraints

Terraform enforces data types to prevent invalid inputs.

## 🔹 String

```hcl
type = string
```

---

## 🔹 Number

```hcl
type = number
```

---

## 🔹 Boolean

```hcl
type = bool
```

---

## 🔹 List

```hcl
type = list(string)
```

Example:

```hcl
variable "azs" {
  type = list(string)

  default = [
    "ap-southeast-1a",
    "ap-southeast-1b"
  ]
}
```

---

## 🔹 Map

```hcl
type = map(string)
```

Example:

```hcl
variable "tags" {
  type = map(string)

  default = {
    Name = "web-server"
    Env  = "dev"
  }
}
```

---

# 3. 🌍 Environment Variables

Terraform variables can be overridden using operating system environment variables.

Format:

```bash
TF_VAR_<variable_name>
```

Example:

```bash
export TF_VAR_instance_type="t3.medium"
```

Apply configuration:

```bash
terraform apply
```

Terraform automatically loads the value.

---

# 4. ⚠️ Validation Rules

Validation rules enforce constraints on variables.

Example:

```hcl
variable "username" {

  type = string

  validation {

    condition = length(var.username) > 3

    error_message = "Username must be more than 3 characters."

  }

}
```

### Behavior

| Condition | Result |
|-----------|--------|
| `length > 3` | ✅ Valid |
| `length <= 3` | ❌ Error |

---

Another example:

```hcl
variable "instance_type" {

  type = string

  validation {

    condition = contains(
      ["t2.micro", "t3.micro"],
      var.instance_type
    )

    error_message = "Only free-tier instance types allowed."

  }

}
```

---

# 5. 🔐 Sensitive Variables

Used for:

- Passwords
- API Keys
- Tokens
- Secrets

Example:

```hcl
variable "db_password" {

  type = string

  sensitive = true

}
```

### What Sensitive Does

- Hides values from logs
- Prevents accidental exposure
- Masks output in Terraform

---

# 6. ❓ Nullable Variables

Allows variables to accept `null`.

```hcl
variable "backup_bucket" {

  type = string

  nullable = true

}
```

Meaning:

- `null` is allowed
- Useful for optional resources
- Helpful for feature toggles

---

# 7. 📁 Variable Definition Files (.tfvars)

Instead of hardcoding defaults inside variables, values can be separated into `.tfvars` files.

## terraform.tfvars

```hcl
instance_type = "t3.micro"

username = "zai"
```

Terraform automatically loads:

```text
terraform.tfvars
```

---

## Custom Variable Files

```bash
terraform apply -var-file="dev.tfvars"
```

Example structure:

```text
dev.tfvars

staging.tfvars

prod.tfvars
```

---

# 🔥 Real-World Example

## variables.tf

```hcl
variable "instance_type" {

  type = string

  default = "t2.micro"

}

variable "environment" {

  type = string

  validation {

    condition = contains(
      ["dev", "staging", "prod"],
      var.environment
    )

    error_message = "Environment must be dev, staging, or prod."

  }

}
```

---

## main.tf

```hcl
resource "aws_instance" "web" {

  instance_type = var.instance_type

  tags = {

    Env = var.environment

  }

}
```

---

## terraform.tfvars

```hcl
instance_type = "t3.micro"

environment = "dev"
```

---

# 🧠 Simple Mental Model

Think of Terraform variables as:

> Inputs to a function

Terraform configuration:

```text
Function
```

Variables:

```text
Parameters
```

`.tfvars`:

```text
Arguments
```

Example:

```python
def deploy(instance_type, environment):
    pass
```

Equivalent Terraform concept:

```text
Terraform Code
       │
       ▼

Variables
(parameters)

       │
       ▼

terraform.tfvars
(arguments)

       │
       ▼

Infrastructure
```

---

# 📌 Phase 5 Checklist

- [ ] Understand input variables
- [ ] Use `var.<name>`
- [ ] Learn type constraints
- [ ] Use lists and maps
- [ ] Override values with `TF_VAR_`
- [ ] Write validation blocks
- [ ] Use sensitive variables
- [ ] Use nullable variables
- [ ] Create `.tfvars` files
- [ ] Separate environments using variable files
- [ ] Apply variables in real AWS resources

---
**Core Principle**

> Terraform variables make infrastructure reusable, scalable, and environment-independent.
