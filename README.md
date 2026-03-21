# Create Repository on AWS and Push to Private Docker Registry (Amazon ECR)

## DevOps Container Registry Project

This project demonstrates how to create a private Docker registry using Amazon Elastic Container Registry (ECR) and push a Docker image to it. Amazon ECR is a fully managed container registry that makes it easy to store, manage, and deploy Docker container images securely.

---

## Technologies Used

Docker  
Amazon ECR (Elastic Container Registry)  
AWS CLI

---

## Project Objectives

Create a private Docker repository on AWS ECR  
Authenticate Docker with AWS ECR  
Tag Docker image for ECR repository  
Push Docker image to private registry  
Verify image upload in ECR

---

## Architecture Overview

Local Machine → Docker Build → AWS ECR Repository → Stored Container Image

---

## Prerequisites

AWS account  
AWS CLI installed and configured  
Docker installed and running  
IAM user with ECR permissions

---

## Step 1: Configure AWS CLI

Ensure AWS CLI is configured:

```bash
aws configure
```

Provide:

Access Key  
Secret Access Key  
Region (e.g., eu-west-2)  
Output format (json)

Verify configuration:

```bash
aws sts get-caller-identity
```

---

## Step 2: Create ECR Repository

Create a new private repository:

```bash
aws ecr create-repository \
--repository-name my-app-repo 
```

Example output includes repository URI:

```
123456789012.dkr.ecr.eu-west-2.amazonaws.com/my-app-repo
```

---

## Step 3: Build Docker Image

```bash
docker build -t my-app .
```

---

## Step 4: Tag Docker Image for ECR

Tag the image using the ECR repository URI:

```bash
docker tag my-app:latest 123456789012.dkr.ecr.eu-west-2.amazonaws.com/my-app-repo:latest
```

---

## Step 5: Push Docker Image to ECR

```bash
docker push 123456789012.dkr.ecr.eu-west-2.amazonaws.com/my-app-repo:latest
```

---

## Step 7: Verify Image in ECR

List images in the repository:

```bash
aws ecr list-images \
--repository-name my-app-repo 
```

---

## Workflow Summary

1. Configure AWS CLI
2. Create ECR repository
3. Build Docker image
4. Tag image with ECR repository URI
5. Push image to ECR
6. Verify image exists in repository

---

## Common Issues and Fixes

### Invalid Security Token

Ensure AWS credentials are correctly configured:

```bash
aws configure
```

---

### Region Mismatch

Ensure all commands use the same region:

```bash
--region eu-west-2
```

---

## IAM Permissions Required

```json
{
  "Effect": "Allow",
  "Action": [
    "ecr:GetAuthorizationToken",
    "ecr:BatchCheckLayerAvailability",
    "ecr:PutImage",
    "ecr:InitiateLayerUpload",
    "ecr:UploadLayerPart",
    "ecr:CompleteLayerUpload"
  ],
  "Resource": "*"
}
```

---

