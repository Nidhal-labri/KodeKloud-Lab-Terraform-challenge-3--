# 🚀 KodeKloud Lab -- Terraform Challenge 3

This repository contains the solution for **KodeKloud Terraform Challenge 3**.  
In this challenge, we will implement a simple **EC2 instance** with some preinstalled packages.

------------------------------------------------------------------------

## 🗺️ Architecture Diagram

<img width="938" height="593" alt="image" src="https://github.com/user-attachments/assets/ac764fa4-a770-4910-a1bd-218352b8c354" />

------------------------------------------------------------------------

## 🛠️ Deployment Steps

### ✅ Step 1 -- Install Terraform (version 1.1.5) on the `iac-server`

1. Check if Terraform is installed:

```bash
terraform --version
```

If no output is returned, Terraform is not installed.

2. Update the OS, ensure `unzip` is available, then download and install Terraform v1.1.5:

```bash
apt update
apt install unzip -y
curl -L -o /tmp/terraform_1.1.5_linux_amd64.zip https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_linux_amd64.zip
unzip -d /usr/local/bin /tmp/terraform_1.1.5_linux_amd64.zip
```

------------------------------------------------------------------------

### ✅ Step 2 -- Declare Variables

Variables will be referenced later in **provider.tf** (already exists) and **citadel.tf**, so we declare them in `variable.tf`.

First, move to the project path:

```bash
cd /root/terraform-challenges/project-citadel
```

Now create `variable.tf` with the following content:

```hcl
variable "ami" {
  type    = string
  default = "ami-06178cf087598769c"
}

variable "region" {
  type    = string
  default = "eu-west-2"
}

variable "instance_type" {
  type    = string
  default = "m5.large"
}
```

Initialize the provider:

```bash
terraform init
```

------------------------------------------------------------------------

### ✅ Step 3 -- Create Terraform Resources

Use `vi` or any text editor to create the following files under: `/root/terraform-challenges/project-citadel`

#### 1. `ssh-key-citadel.tf`

```hcl
resource "aws_key_pair" "citadel-key" {
  key_name   = "citadel"
  public_key = file("/root/terraform-challenges/project-citadel/.ssh/ec2-connect-key.pub")
}
```

#### 2. `citadel.tf` (EC2 Instance)

```hcl
resource "aws_instance" "citadel" {
  ami           = var.ami
  instance_type = var.instance_type
  key_name      = aws_key_pair.citadel-key.key_name
  user_data     = file("/root/terraform-challenges/project-citadel/install-nginx.sh")
}
```

#### 3. `ec2_eip.tf` (Elastic IP)

```hcl
resource "aws_eip" "eip" {
  vpc      = true
  instance = aws_instance.citadel.id
  provisioner "local-exec" {
    command = "echo ${self.public_dns} >> /root/citadel_public_dns.txt"
  }
}
```

------------------------------------------------------------------------

### ✅ Step 4 -- Deploy and Verify

Deploy with:

```bash
terraform plan
terraform apply
```

Verify deployment with:

```bash
terraform show
```
Or simply use the lab's **check button**.

<img width="1919" height="815" alt="image" src="https://github.com/user-attachments/assets/1e29154f-11c3-405b-b226-e8f7733d442e" />

**🎉 Congratulations -- We have successfully completed Terraform Challenge 3!**

------------------------------------------------------------------------

## 🧠 Contributions

Contributions, corrections, and improvements are welcome.  
Feel free to open a **pull request** or create an **issue**.

------------------------------------------------------------------------

## ✍️ Author

Made with 💻 by **Nidhal Labri**  
🔗 [LinkedIn](https://www.linkedin.com/in/nidhal-labri/)
