# Phase 8: Format & Validate

## Overview

**Phase 8: Format & Validate** is all about making your Terraform code **clean, consistent, correct, and professional** before you commit it or run `terraform plan` or `terraform apply`.

Think of it as the **quality control** step in your Terraform workflow.

---

# Why This Phase Matters

- Messy code is hard to read, hard to review, and easy to introduce bugs.
- Unformatted or invalid code can lead to failed plans, merge conflicts, and production issues.
- Running these commands catches problems early before they become expensive.

---

# The Three Tools (In Order)

1. **terraform fmt**
   - Makes code clean and consistent.

2. **terraform validate**
   - Checks whether the Terraform configuration is technically correct.

3. **TFLint**
   - Performs a deeper code review using Terraform best practices.

✅ **Move on when:**
- No syntax errors
- Code is properly formatted
- (Ideally) No TFLint warnings

---

# 1. terraform fmt — The Formatter

## Purpose

Automatically rewrites Terraform files into Terraform's official formatting style.

It fixes:

- Indentation
- Spacing around `=`
- Line breaks
- Block alignment
- Brace placement

> **Important:** It only changes formatting—not your Terraform logic.

---

## Commands

```bash
# Format current directory
terraform fmt

# Format and display modified files
terraform fmt -verbose

# Format all Terraform files recursively
terraform fmt -recursive

# Check formatting without modifying files
terraform fmt -check -recursive
```

---

## Example

### Before

```hcl
resource "aws_instance" "example" { ami = "ami-123"
instance_type = "t2.micro" tags = { Name = "MyServer" } }
```

### After

```hcl
resource "aws_instance" "example" {
  ami           = "ami-123"
  instance_type = "t2.micro"

  tags = {
    Name = "MyServer"
  }
}
```

---

## Pro Tip

Run `terraform fmt` before every Git commit.

Even better:
- Add it to a Git pre-commit hook.
- Run it automatically in your CI/CD pipeline.

---

# 2. terraform validate — The Syntax Checker

## Purpose

Checks whether your Terraform configuration is syntactically correct and internally consistent.

---

## What It Verifies

- Correct syntax
- Missing brackets
- Invalid resource arguments
- Variable type mismatches
- Required arguments exist
- Provider and module compatibility

---

## Important Notes

- Run **after** `terraform init`
- Does **not** verify cloud resources
- Fast and runs locally

---

## Commands

```bash
terraform init
terraform validate
```

---

## Successful Output

```text
Success! The configuration is valid.
```

---

## Error Example

```text
Error: Missing required argument

on main.tf line 5, in resource "aws_instance" "example":
5: resource "aws_instance" "example" {

The argument "ami" is required, but no definition was found.
```

---

## When To Use

Run `terraform validate` before every `terraform plan`.

It catches many copy-paste mistakes within seconds.

---

# 3. TFLint — The Smart Linter

Unlike `terraform fmt` and `terraform validate`, **TFLint** is an external tool.

It checks Terraform code against best practices.

---

## What It Does

- Finds mistakes Terraform may miss
- Detects deprecated arguments
- Checks naming conventions
- Enforces provider best practices
- Supports custom linting rules

---

## Installation

### macOS

```bash
brew install terraform-linters/tap/tflint
```

### Linux

```bash
curl -sSLO https://github.com/terraform-linters/tflint/releases/latest/download/tflint_linux_amd64.zip

unzip tflint_linux_amd64.zip

sudo install tflint /usr/local/bin/
```

---

## Commands

```bash
# Install provider plugins
tflint --init

# Scan current directory
tflint

# Scan recursively
tflint --recursive

# JSON output (great for CI)
tflint --format json
```

---

## Common Issues TFLint Detects

- Deprecated Terraform arguments
- Missing AWS tags
- Insecure security groups
- Naming convention violations
- Provider-specific best practice issues

---

## Configuration

You can customize TFLint by creating:

```text
.tflint.hcl
```

Use it to enable or disable specific rules.

---

# Recommended Workflow

Run these commands inside your Terraform project.

```bash
# Format Terraform files
terraform fmt -recursive

# Initialize providers
terraform init

# Validate configuration
terraform validate

# Install TFLint plugins
tflint --init

# Run TFLint
tflint --recursive
```

If all commands complete successfully, you're ready to run:

```bash
terraform plan
```

---

# Bonus Tips

## CI/CD

Automatically run:

- terraform fmt
- terraform validate
- tflint

inside GitHub Actions, GitLab CI, or other CI/CD pipelines.

---

## Git Pre-Commit Hooks

Prevent unformatted or invalid Terraform code from being committed.

Popular tools:

- pre-commit
- Husky (for JavaScript projects)

---

## VS Code Integration

Install the **Terraform** extension to get:

- Automatic formatting
- Syntax highlighting
- Validation
- Linting
- Auto-completion

---

## Team Consistency

Every team member should run:

```bash
terraform fmt
```

before pushing code.

This keeps Terraform files consistent across the entire project.

---

# Summary

Phase 8 ensures your Terraform code is:

- ✅ Clean
- ✅ Readable
- ✅ Consistent
- ✅ Valid
- ✅ Following best practices

Although this phase usually takes less than **30 seconds**, it can save **hours of debugging** later.

**Remember the workflow:**

```text
terraform fmt
        ↓
terraform init
        ↓
terraform validate
        ↓
tflint
        ↓
terraform plan
```
