# PHASE 7 — Outputs

Outputs allow Terraform to **display or expose values** from your infrastructure after it has been created.

For example, if AWS generates a VPC ID, Terraform can print it for you.

## Example

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

After running:

```bash
terraform apply
```

Terraform displays:

```text
Outputs:

vpc_id = "vpc-0a12bc34de56"
```

---

# 1. Output Syntax

The basic syntax is:

```hcl
output "output_name" {
  value = VALUE
}
```

## Parts

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

- **output** → Declares an output block.
- **"vpc_id"** → The name of the output.
- **value** → The value Terraform will display.

### Another Example

```hcl
output "subnet_id" {
  value = aws_subnet.public.id
}
```

Result:

```text
Outputs:

subnet_id = subnet-123456
```

---

# 2. Sensitive Outputs

Sometimes outputs contain confidential information such as:

- Passwords
- API Keys
- Tokens
- Database credentials

You don't want Terraform to print these on the screen.

Instead, mark them as **sensitive**.

```hcl
output "db_password" {
  value     = aws_db_instance.main.password
  sensitive = true
}
```

After applying:

```text
Outputs:

db_password = <sensitive>
```

Terraform hides the value instead of displaying it.

## Without `sensitive`

```hcl
output "password" {
  value = "MySecret123"
}
```

Output:

```text
password = MySecret123
```

Anyone looking at the terminal can see it.

---

# 3. Preconditions

A **precondition** checks whether a condition is true before Terraform returns the output.

If the condition fails, Terraform stops and displays an error.

## Example

```hcl
output "public_ip" {
  value = aws_instance.web.public_ip

  precondition {
    condition     = aws_instance.web.public_ip != ""
    error_message = "The instance has no public IP."
  }
}
```

## How It Works

If the instance has a public IP:

```text
public_ip = 54.123.45.67
```

If it doesn't:

```text
Error: The instance has no public IP.
```

---

# Summary

| Topic | Purpose |
|--------|---------|
| **Output Syntax** | Displays useful values after Terraform creates resources. |
| **Sensitive Outputs** | Hides confidential values such as passwords and API keys. |
| **Preconditions** | Validates an output before Terraform returns it, preventing invalid or unexpected values. |
