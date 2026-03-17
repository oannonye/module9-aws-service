# Deploy Web Application on EC2 Instance (Manual Deployment)

## DevOps Deployment Project

This project demonstrates how to manually deploy a containerized web application on an AWS EC2 instance using Docker. It covers provisioning an EC2 instance, installing Docker, pulling an image from a private Docker repository, and running the container with external access enabled.

---

## Technologies Used

AWS  
EC2  
Docker  
Linux

---

## Project Objectives

Create and configure an EC2 instance on AWS  
Install Docker on a remote Linux server  
Pull a Docker image from a private repository  
Run a Docker container on EC2  
Expose the application to external users

---

## Architecture Overview

User → Internet → EC2 Instance → Docker Container → Web Application

---

## Step 1: Create EC2 Instance

1. Log in to AWS Management Console
2. Navigate to EC2 Dashboard
3. Click **Launch Instance**
4. Configure instance:
    - AMI: Ubuntu Server (or Amazon Linux)
    - Instance Type: t2.micro (free tier eligible)
    - Key Pair: Create or select existing key pair
5. Configure Security Group:
    - Allow SSH (Port 22)
    - Allow Custom Port (e.g., 3080 if your app uses it)
6. Launch the instance

---

## Step 2: Connect to EC2 Instance

Use SSH to connect to the instance:

```bash
ssh -i your-key.pem ec2-user@your-ec2-public-ip
```

---

## Step 3: Update Server Packages

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Step 4: Install Docker on EC2 Instance

```bash
sudo apt install docker.io -y
```

Start and enable Docker:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Verify Docker installation:

```bash
docker --version
```

(Optional) Add user to Docker group:

```bash
sudo usermod -aG docker $USER
```

Log out and log back in for changes to take effect.

---

## Step 5: Authenticate to Private Docker Repository

Log in to DockerHub (or private registry):

```bash
docker login
```

Enter your DockerHub username and password when prompted.

---

## Step 6: Pull Docker Image from Private Repository

```bash
docker pull yourdockerhubusername/your-image:tag
```

---

## Step 7: Run Docker Container

Run the container and map ports:

```bash
docker run -d -p 3080:3080 yourdockerhubusername/your-image:tag
```



---

## Step 8: Verify Running Container

```bash
docker ps
```

Check logs if needed:

```bash
docker logs <container_id>
```

---

## Step 9: Access Application in Browser

Open your browser and navigate to:


```
http://your-ec2-public-ip:3080
```

---

## Deployment Workflow Summary

1. Create EC2 instance on AWS
2. Connect via SSH
3. Install Docker
4. Authenticate to Docker repository
5. Pull Docker image
6. Run container
7. Expose application via EC2 public IP

---

## Security Considerations

Use SSH key-based authentication  
Restrict security group inbound rules  
Avoid exposing unnecessary ports  
Use private Docker repositories for sensitive images

