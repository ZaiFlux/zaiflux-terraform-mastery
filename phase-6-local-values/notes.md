# Phase 6 — Local Values

## What are Local Values?

A **Local Value** is a named expression that stores a value **inside your Terraform configuration**.

Think of it like a variable, but with one important difference:

- **Variables** → Values come from the user or another file.
- **Locals** → Values are created and managed by your Terraform code.

### Analogy

- **Variable** = Someone asks you your name.
- **Local** = You create a nickname for yourself.

The nickname isn't provided by someone else—you define it.

---

# Basic Syntax

```hcl
locals {
  project = "portfolio"
}
```

Now you can use it anywhere:

```hcl
tags = {
  Name = local.project
}
```

Notice:

```hcl
local.project
```

**Not**

```hcl
var.project
```

because it is a **local value**, not a variable.

---

# Why Use Locals?

Suppose your project name appears 20 times.

Without locals:

```hcl
resource "aws_vpc" "main" {
  tags = {
    Name = "portfolio"
  }
}

resource "aws_subnet" "public" {
  tags = {
    Name = "portfolio"
  }
}

resource "aws_security_group" "web" {
  tags = {
    Name = "portfolio"
  }
}
```

If you change your project name, you must edit every occurrence.

Instead:

```hcl
locals {
  project = "portfolio"
}
```

Now:

```hcl
tags = {
  Name = local.project
}
```

Change it once:

```hcl
locals {
  project = "cloud-lab"
}
```

Everything updates automatically.

---

# Multiple Local Values

```hcl
locals {
  project = "portfolio"
  env     = "dev"
  owner   = "Rodhel"
}
```

Access them like this:

```hcl
local.project
local.env
local.owner
```

---

# Naming Standards

Terraform doesn't enforce naming rules, but these are common best practices:

✅ **Use lowercase**

```text
project
```

✅ **Separate words with underscores**

```text
project_name
```

✅ **Keep names meaningful**

```text
environment
```

Avoid:

```text
x
abc
test123
```

because they don't describe their purpose.

---

# Example

```hcl
locals {
  project_name = "portfolio"
  environment  = "development"
}
```

Then:

```hcl
tags = {
  Project = local.project_name
  Env     = local.environment
}
```

---

# Computed Values

This is where locals become especially useful.

They can be built from other values.

Example:

```hcl
locals {
  project = "portfolio"
  env     = "dev"

  full_name = "${local.project}-${local.env}"
}
```

Terraform computes:

```text
portfolio-dev
```

You can use it like:

```hcl
tags = {
  Name = local.full_name
}
```

---

# Another Computed Example

```hcl
locals {
  first = "Cloud"
  last  = "Engineer"

  title = "${local.first} ${local.last}"
}
```

Result:

```text
Cloud Engineer
```

---

# Combining Variables and Locals

Variables often come from the user:

```hcl
variable "environment" {
  default = "dev"
}
```

Local values can build on them:

```hcl
locals {
  project = "portfolio"

  name = "${local.project}-${var.environment}"
}
```

If:

```text
environment = dev
```

Result:

```text
portfolio-dev
```

If:

```text
environment = prod
```

Result:

```text
portfolio-prod
```

---

# Real AWS Example

```hcl
locals {
  project = "portfolio"
  env     = "dev"

  common_tags = {
    Project = local.project
    Env     = local.env
  }
}
```

Use it like this:

```hcl
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

  tags = local.common_tags
}
```

You can reuse the same tags across many resources without copying them.

---

# Why This Is Useful

Without locals:

```text
Project = "portfolio"

Env = "dev"
```

repeated everywhere.

With locals:

```hcl
tags = local.common_tags
```

Your configuration becomes:

- Cleaner
- Easier to maintain
- Less error-prone
- More reusable

---

# Summary

| Variables | Locals |
|-----------|--------|
| Input from the user | Defined within the Terraform code |
| Access with `var.name` | Access with `local.name` |
| Can change between deployments | Used for internal reuse and computed values |

---

# Best Practices

- Use locals for values that are reused in many places.
- Give locals descriptive, lowercase names with underscores.
- Use locals to build computed values (such as resource names).
- Store common tags in a single local map to keep resources consistent.
- Use variables for inputs and locals for values derived from those inputs.
