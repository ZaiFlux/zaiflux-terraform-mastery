# Terraform Data Sources and `templatefile()`

## 1. Data Sources

### What is a Data Source?

A **data source** lets Terraform **read information that already exists**.

It **does not create anything**.

Think of it like asking AWS a question.

Terraform asks:

> "Hey AWS, what's the latest Ubuntu AMI?"

AWS replies:

> "The latest Ubuntu AMI is `ami-0123456789abcdef`."

Terraform stores that answer and lets you use it later.

---

## Example

```hcl
data "aws_ami" "ubuntu" {
  most_recent = true
}
```

### Break It Down

### `data`

Means:

> "I'm reading information."

---

### `aws_ami`

Means:

> "I want information about an Amazon Machine Image (AMI)."

---

### `ubuntu`

This is simply the **name** you chose.

For example, these are also valid:

```hcl
data "aws_ami" "linux" {
```

or

```hcl
data "aws_ami" "my_image" {
```

Terraform doesn't care what you call it.

The name is only used so you can reference it later in your configuration.

---

### `most_recent = true`

Means:

> "If there are many Ubuntu AMIs, give me the newest one."

---

## Real-Life Analogy

Imagine asking a librarian:

> "Give me the newest edition of this book."

The librarian doesn't print a new book.

They simply look up the newest edition.

A Terraform data source works the same way—it only retrieves existing information.

---

# 2. `templatefile()`

Imagine you have a text file.

Example:

### `hello.tftpl`

```text
Hello ${name}
```

Notice:

```text
${name}
```

It isn't filled in yet.

It's just a **placeholder**.

---

Now Terraform reads that file:

```hcl
templatefile("hello.tftpl", {
  name = "Rodhel"
})
```

Terraform replaces:

```text
${name}
```

with

```text
Rodhel
```

Final result:

```text
Hello Rodhel
```

---

## Another Example

### Template

```text
Welcome ${user}
Region: ${region}
```

### Terraform

```hcl
templatefile("welcome.tftpl", {
  user   = "Rodhel"
  region = "ap-southeast-1"
})
```

### Result

```text
Welcome Rodhel
Region: ap-southeast-1
```

---

## Why Use `templatefile()`?

Instead of writing a long script directly inside `main.tf`, you can keep it in a separate file.

Example:

### `install.sh.tftpl`

```bash
#!/bin/bash
echo "Hello ${name}"
```

Terraform:

```hcl
user_data = templatefile(
  "install.sh.tftpl",
  {
    name = "Rodhel"
  }
)
```

Terraform generates:

```bash
#!/bin/bash
echo "Hello Rodhel"
```

That generated script is then sent to an EC2 instance as its startup script using the `user_data` argument.

---

# Key Ideas to Remember

| Topic | Simple Meaning |
|--------|----------------|
| **Data Source** | Ask AWS for information that already exists. |
| **`templatefile()`** | Read a file and replace placeholders like `${name}` with actual values. |

---

# Quick Summary

- **Data sources** retrieve existing resources or information from a provider without creating anything.
- Use data sources when you need values such as the latest AMI, an existing VPC, or an availability zone.
- **`templatefile()`** loads a template file and substitutes variables with real values.
- It's commonly used for startup scripts (`user_data`), configuration files, and other dynamically generated text.
- Keeping templates in separate files makes Terraform configurations cleaner and easier to maintain.
