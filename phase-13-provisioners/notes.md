# Terraform Provisioners

## What does Terraform normally do?

Terraform's primary job is to create and manage infrastructure resources.

For example:

```text
terraform apply
       │
       ▼
Create VPC
Create Subnet
Create Security Group
Create EC2
```

After this process, your EC2 instance exists.

However, it is only a basic Ubuntu server with no additional software installed.

---

# Why do Provisioners exist?

Sometimes, after Terraform creates an EC2 instance, you want it to perform additional tasks automatically.

For example:

### Install Software

```text
terraform apply
       │
       ▼
Create EC2
       │
       ▼
Install Nginx
```

### Copy Files

```text
terraform apply
       │
       ▼
Create EC2
       │
       ▼
Copy Website Files
```

### Run a Shell Script

```text
terraform apply
       │
       ▼
Create EC2
       │
       ▼
Run a Shell Script
```

These additional tasks are performed using **Terraform Provisioners**.

---

# Types of Terraform Provisioners

---

## 1. File Provisioner

### Purpose

The **File Provisioner** copies files from your local computer to a remote machine such as an EC2 instance.

### Example

Suppose your local computer contains:

```text
install.sh
```

Terraform automatically copies it to the EC2 instance:

```text
EC2
└── /home/ubuntu/install.sh
```

---

## 2. Remote-Exec Provisioner

### Purpose

The **Remote-Exec Provisioner** connects to the EC2 instance using SSH and executes Linux commands after the instance has been created.

### Workflow

```text
terraform apply
       │
       ▼
EC2 Created
       │
       ▼
SSH into EC2
       │
       ▼
sudo apt update
sudo apt install nginx -y
```

Without `remote-exec`, you would manually SSH into the EC2 instance and execute these commands yourself.

With `remote-exec`, Terraform performs the steps automatically.

---

## 3. Local-Exec Provisioner

### Purpose

The **Local-Exec Provisioner** executes commands on your **local computer**, not on the AWS resource.

### Example

```text
terraform apply
       │
       ▼
EC2 Created
       │
       ▼
Run on Local Computer:
echo "Deployment Successful"
```

In this example:

- The EC2 instance does **not** execute the command.
- Your laptop or workstation executes it.

---

## 4. Creation-Time Provisioners

### Purpose

Creation-time provisioners execute **after Terraform creates a resource**.

### Example

```text
Create EC2
      │
      ▼
Install Nginx
```

This is useful for automatically configuring a newly created server.

---

## 5. Destroy-Time Provisioners

### Purpose

Destroy-time provisioners execute **before Terraform deletes a resource**.

### Example

```text
terraform destroy
       │
       ▼
Backup Logs
       │
       ▼
Delete EC2
```

This allows cleanup tasks or backups before infrastructure is removed.

---

## 6. Custom Provisioners

### Purpose

Terraform provisioners can execute almost any command required during deployment or cleanup.

### Examples

- Run a Python script
- Run a Bash script
- Execute an AWS CLI command
- Trigger another automation tool
- Perform custom deployment tasks

---

# Real AWS Deployment Workflow

Suppose you are deploying a web server.

```text
terraform apply
        │
        ▼
Create VPC
        │
        ▼
Create Subnet
        │
        ▼
Create Security Group
        │
        ▼
Create EC2
        │
        ├──────────────┐
        │              │
        ▼              ▼
Copy install.sh   Install Nginx
(File)            (Remote-Exec)
        │
        ▼
Print "Deployment Complete"
(Local-Exec)
```

---

# Summary

Terraform's primary responsibility is to create and manage infrastructure resources such as:

- VPCs
- Subnets
- Security Groups
- EC2 Instances
- Databases
- Load Balancers

Provisioners are **optional automation tools** that perform additional tasks after resources are created or before they are destroyed.

Common provisioners include:

| Provisioner | Purpose |
|-------------|---------|
| **File** | Copies files from your local computer to a remote machine. |
| **Remote-Exec** | Executes commands on the remote EC2 instance using SSH. |
| **Local-Exec** | Executes commands on your local computer. |
| **Creation-Time** | Runs immediately after a resource is created. |
| **Destroy-Time** | Runs before a resource is deleted. |
| **Custom Provisioners** | Executes custom scripts or automation commands. |

---

# Key Takeaway

Terraform's main responsibility is **Infrastructure as Code (IaC)**—creating and managing cloud resources.

Provisioners extend Terraform by automating configuration tasks, deployments, file transfers, and cleanup operations after infrastructure has been created or before it is destroyed.

> **Note:** Provisioners should be used sparingly. Whenever possible, prefer cloud-native approaches such as **User Data**, **cloud-init**, or configuration management tools like **Ansible**, as recommended by Terraform best practices.
