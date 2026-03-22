# Interacting with AWS CLI

## DevOps AWS CLI Project

This project demonstrates how to install and configure the AWS Command Line Interface (CLI) and use it to interact with AWS services. The project covers creating and managing EC2 instances, security groups, SSH key pairs, and IAM resources such as users, groups, and policies — all via the CLI.

---

## Technologies Used

AWS  
Linux  
AWS CLI

---

## Project Objectives

Install and configure AWS CLI  
Connect CLI to AWS account  
Create EC2 instance using CLI  
Configure Security Groups  
Create SSH key pairs  
Create IAM users, groups, and policies  
List and manage AWS resources using CLI

---

## Architecture Overview

Local Machine / Server → AWS CLI → AWS Services (EC2, IAM, Security Groups, Key Pairs)

---

## Prerequisites

AWS account  
Linux-based system  (Amazon Image)
Internet connection  
IAM user with programmatic access (Access Key & Secret Key)

---

## Step 1: Install AWS CLI

```bash
sudo yum update
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```


Verify installation:

```bash
aws --version
```

---

## Step 2: Configure AWS CLI

```bash
aws configure
```

Provide:

Access Key ID  
Secret Access Key  
Region (e.g., eu-west-2)  
Output format (json)

Verify configuration:

```bash
aws sts get-caller-identity
```

---

## Step 3: Create SSH Key Pair

```bash
aws ec2 create-key-pair \
--key-name my-key-pair \
--query 'KeyMaterial' \
--output text > my-key-pair.pem
```

Set correct permissions:

```bash
chmod 400 my-key-pair.pem
```

---

## Step 4: Create Security Group

```bash
aws ec2 create-security-group \
--group-name my-security-group \
--description "Security group for SSH access" \
--vpc-id <vpc-my-vpc-id>

```

---

## Step 5: Add Inbound Rules to Security Group

Allow SSH (port 22):

```bash
aws ec2 authorize-security-group-ingress \
--group-name my-security-group \
--protocol tcp \
--port 22 \
--cidr <my-ip/32> # To allow only my ip ssh access
```

Allow HTTP (optional):

```bash
aws ec2 authorize-security-group-ingress \
--group-name my-security-group \
--protocol tcp \
--port 80 \
--cidr 0.0.0.0/0 \
```

---

## Step 6: Launch EC2 Instance

```bash
aws ec2 run-instances \
--image-id ami-0c55b159cbfafe1f0 \
--count 1 \
--instance-type t2.micro \
--key-name my-key-pair \
--security-groups my-security-group \
--subnet-id my-subnet-id
```

---

## Step 7: List EC2 Instances

```bash
aws ec2 describe-instances 
```

---

## Step 8: Create IAM User

```bash
aws iam create-user --user-name dev-user
```

---

## Step 9: Create IAM Group

```bash
aws iam create-group --group-name dev-group
```

---

## Step 10: Attach Policy to Group

```bash
aws iam attach-group-policy \
--group-name dev-group \
--policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess
```

---

## Step 11: Add User to Group

```bash
aws iam add-user-to-group \
--user-name dev-user \
--group-name dev-group
```

---

## Step 12: Create Access Keys for User

```bash
aws iam create-access-key --user-name dev-user
```

---

## Step 13: List AWS Resources

List EC2 instances:

```bash
aws ec2 describe-instances
```

List security groups:

```bash
aws ec2 describe-security-groups
```

List IAM users:

```bash
aws iam list-users
```

List IAM groups:

```bash
aws iam list-groups
```

---

## Workflow Summary

1. Install AWS CLI
2. Configure AWS credentials
3. Create SSH key pair
4. Create and configure security group
5. Launch EC2 instance
6. Create IAM user and group
7. Attach policies and permissions
8. List and manage AWS resources

---

## Common Issues and Fixes

### Region Not Specified

```bash
aws ec2 describe-instances --region eu-west-2
```

Or export:

```bash
export AWS_DEFAULT_REGION=eu-west-2
```

---

### Invalid Security Token

Reconfigure credentials:

```bash
aws configure
```

---

### Permission Denied (IAM)

Ensure user has required permissions such as:

EC2FullAccess  
IAMFullAccess

---

## Security Best Practices

Use IAM roles instead of root credentials  
Restrict security group access (avoid 0.0.0.0/0 in production)  
Store credentials securely  
Rotate access keys regularly

---

