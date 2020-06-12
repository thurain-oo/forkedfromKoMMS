---
title: Terraform with AWS 
author: minn
type: post
date: 2020-06-11T08:51:17+00:00
tags:
  - aws
  - terraform
  - cloud
  - orchestration
  - iaas
---

## Quick Start
Let's start with an empty default VPC on us-east-1.

### Install Terraform
Download and install [terraform](https://www.terraform.io/downloads.html) on your client machine.

### Configure awscli, if it's not done yet
We'll configure it in a separate credential profile called `sandbox`

```shell-session
$ aws --profile sandbox configure
AWS Access Key ID [None]: ENTER-YOUR-ACCESS-KEY-HERE
AWS Secret Access Key [None]: ENTER-YOUR-SECRET-KEY-HERE
Default region name [None]: us-east-1
Default output format [None]: 
```

### Launching an Amazon Linux 2 instance in AWS default VPC in the region us-east-1
Create a file in the project directory. We'll save it as `aws.tf`
```toml
provider "aws" {
  profile    = "sandbox"
  region     = "us-east-1"
}

resource "aws_instance" "web-server" {
  ami           = "ami-09d95fab7fff3776c"
  instance_type = "t2.micro"
}
```
That should be pretty much self-explanatory. 
- We will be using credentials in `sandbox` profile
- Our server will be in us-east-1 region
- We will be using Amazon Linux 2 AMI
- The instance type is t2.micro
- The resource will be referenced in terraform config as "web-server"

Now you should have a file called `aws.tf` in your project directory.

Proceed to initialize terraform.
```shell-session
$ terraform init
```

Check the execution plan (Not mandatory)
```shell-session
$ terraform plan
```
It shows what resources will be created/modified/removed.

Build the infrastructure
```shell-session
$ terraform apply
```
It will show the plan, and you'll need to say `yes` to continue building the infrastructure on AWS. At the end of the execution, you should see something similar to the following:

```shell-session
[...]
aws_instance.web-server: Creating...
aws_instance.web-server: Still creating... [10s elapsed]
aws_instance.web-server: Still creating... [20s elapsed]
aws_instance.web-server: Still creating... [30s elapsed]
aws_instance.web-server: Creation complete after 36s [id=i-03429b9a3aa0827d8]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
If you check on your web console, you should be seeing your instance is running. You can also check with `awscli`.
```shell-session
$ aws --profile sandbox ec2 describe-instances
```

You now have the server instance. But you have no way to login to the instance's shell. We need a SSH public key to be added to the instance during creation, and we did not. We made a mistake. The initial key pair can only be added to the instance when it was created. We'll destroy the infrastructure and re-create again (for demo purposes).

```shell-session
$ terraform destroy
```
Our infrastructure is deleted from AWS.

Let's create an SSH key to be used on the instance and modify the terraform again.
```shell-session
$ ssh-keygen -i rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/demo/.ssh/id_rsa): /Users/demo/.ssh/terraform
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in terraform.
Your public key has been saved in /Users/demo/.ssh/terraform.pub.
The key fingerprint is:
SHA256:xa7A8FNYqX0d8CwwUJGh03MyGrvcNmrzZlxJRXn9qbo
The key's randomart image is:
+---[RSA 3072]----+
|      .oB=.o.. . |
|       =o+ o+ . .|
|    . =o* =ooo  o|
|     +.*.Bo..  ..|
|      B So..  .  |
|     . = .o  .   |
|      o.=.  .    |
|      oo+. .     |
|     ..=.  E.    |
+----[SHA256]-----+
```
We just created SSH keys `terraform` (private key) and `terraform.pub` (public key) in the directory `/Users/demo/.ssh)`. We will need to upload the public key `terraform.pub` to AWS.

Modify the `aws.tf` file by adding the following resource:
```toml
resource "aws_key_pair" "deployer" {
  key_name   = "deployer-key"
  public_key = file("~/.ssh/terraform.pub")
}
```
- We are adding AWS Key Pair (which will be referenced as `deployer`)
- The name of the key will be `deployer-key`
- Use the file `~/.ssh/terraform.pub`

Modify the instance resource in `aws.tf` to use the key
```toml
resource "aws_instance" "web-server" {
  ami           = "ami-09d95fab7fff3776c"
  instance_type = "t2.micro"
  key_name      = aws_key_pair.deployer.id 
```
Check the plan again.
```shell-session
$ terraform plan
```

And create the infrastructure
```shell-session
$ terraform apply
```
Check the instance's IP address.
```shell-session
$ aws --profile sandbox ec2 describe-instances | grep PublicIp
```
Since we only have one instance, we can just grep `PublicIp` and check the value. Otherwise, you'll need to check the whole output and look the Public IP corresponding to your instance. 

Alternatively, you can provision the instance to spit out the IP to the text file in your terraform directory after creation.

Now try to login to your instance.
```shell-session
$ ssh -i ~/.ssh/terraform ec2-user@[Public IP Address]
```
You still won't be able to see the shell, and your SSH connection will eventually time out. It is because the default security group does not allow any connection from any outside IP addresses.

We will need to create a new security group that allows SSH connection from the subnet `0.0.0.0/0` or only from your external IP. For the sake of simplicity (so it's not secure), we will use `0.0.0.0/0`.

Let's add the following security group to the `aws.tf` file.
```toml
resource "aws_security_group" "allow-ssh-sg" {
  name        = "public-ssh-sg"
  description = "Allow incoming ssh traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

And modify the instance resource in `aws.tf` to use the above resource.
```toml
resource "aws_instance" "web-server" {
  ami           = "ami-09d95fab7fff3776c"
  instance_type = "t2.micro"
  key_name               = aws_key_pair.deployer.id
  vpc_security_group_ids = [aws_security_group.ssh-sg.id]
}
```

Proceed to creating the infrastructure. Terraform will only create and modify the changes as needed.
```shell-session
$ terraform apply
```

Try to SSH into the instance.
```shell-session
$ ssh -i ~/.ssh/terraform ec2-user@[Public IP Address]
```

After playing, delete all the infrastructure. You can re-create the infrastructure again anytime with `aws.tf` file.
```shell-session
$ terraform destroy
```
