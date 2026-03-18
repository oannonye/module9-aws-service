     # CD - Deploy Application from Jenkins Pipeline to EC2 Instance (Docker-Based Deployment)

## DevOps Continuous Deployment Project

This project demonstrates how to implement a Continuous Deployment (CD) pipeline that automatically deploys a Dockerized application from a Jenkins pipeline to an AWS EC2 instance. The pipeline connects securely to the EC2 server via SSH, pulls the latest Docker image from Docker Hub, and runs the container, making the application accessible externally.

---

## Technologies Used

AWS  
EC2  
Jenkins  
Docker  
Linux  
Git  
Node.js  
Docker Hub

---

## Project Objectives

Prepare an EC2 instance for Docker-based deployment  
Configure secure SSH access between Jenkins and EC2  
Extend CI pipeline with automated deployment step  
Pull and run Docker image on EC2 from Jenkins  
Expose application via EC2 public IP  
Enable fully automated deployment workflow

---

## Architecture Overview

Developer → Git Repository → Jenkins CI Pipeline → Docker Hub → Jenkins CD Step → EC2 Instance → Docker Container → Web Application

---

## Step 1: Prepare EC2 Instance for Deployment

1. Launch an EC2 instance (Amazon Linux)
2. Configure Security Group:
    - Allow SSH (Port 22)
    - Allow application port (e.g., 3080)
3. Connect to EC2:

```bash
ssh -i your-key.pem ec2-user@your-ec2-public-ip
```

4. Install Docker:

```bash
sudo yum update
sudo yum install docker
sudo systemctl start docker

```

5. Add ec2-user to Docker group:

```bash
sudo usermod -aG docker $USER
```

---

## Step 2: Create SSH Credentials in Jenkins

1. Go to Jenkins Dashboard
2. Navigate to:

Manage Jenkins → Credentials → Global → Add Credentials

3. Configure:

Kind: SSH Username with private key  
Username: ec2-user  
Private Key: Paste contents of your `.pem` key  
ID: ec2-ssh-key

Save the credentials.

---

## Step 3: Configure Security Group for Jenkins Server

In AWS EC2 Security Group:

Allow inbound traffic for Jenkins Server on:

Port 22 → SSH  


---

## Step 4: Extend Jenkins Pipeline with Deployment Stage

Add a deployment stage to your existing Jenkins pipeline.

Example Jenkinsfile:

```groovy

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@$EC2_HOST '
                        docker pull $IMAGE_NAME:$IMAGE_TAG &&
                        docker stop node-app || true &&
                        docker rm node-app || true &&
                        docker run -d -p 80:3000 --name node-app $IMAGE_NAME:$IMAGE_TAG
                    '
                    """
                }
            }
        }
```

---

## Step 5: Access Deployed Application

Open browser:

```
http://your-ec2-public-ip
```

Or if using custom port:

```
http://your-ec2-public-ip:3080
```

---

## Deployment Workflow Summary

1. Jenkins triggers CD deployment stage
2. Jenkins connects to EC2 via SSH
3. EC2 pulls latest image and runs container
4. Application is updated automatically

---

## Benefits

Fully automated deployment process  
Faster release cycles  
Eliminates manual deployment steps  
Consistent Docker-based deployments  
Scalable and repeatable infrastructure

---

## Security Best Practices

Use SSH key authentication instead of passwords  
Restrict EC2 security group access  
Store credentials securely in Jenkins  
Avoid exposing unnecessary ports  
Use private Docker repositories where necessary

---

