     # CD - Deploy Application from Jenkins Pipeline to EC2 Instance (Docker-Based Deployment)

## DevOps Continuous Deployment Project

This project demonstrates how to implement a Continuous Deployment (CD) pipeline that automatically deploys a Dockerized Java application from a Jenkins pipeline to an AWS EC2 instance using Docker Compose. The pipeline builds the application using Maven, pushes the Docker image to Docker Hub, and then remotely deploys the updated image on the EC2 server using Docker Compose. To improve maintainability, multiple remote deployment commands are extracted into a separate shell script.

---

## Technologies Used

AWS  
EC2  
Jenkins  
Docker
Docker Compose
Linux  
Git  
Java
Maven
Docker Hub

---

## Project Objectives

Install and configure Docker Compose on EC2 instance  
Define multi-container deployment using docker-compose.yml  
Automate deployment from Jenkins pipeline  
Pull latest Docker image from Docker Hub  
Run and update application using Docker Compose  
Improve pipeline by extracting remote commands into shell script

---

## Architecture Overview

Developer → Git Repository → Jenkins CI Pipeline → Docker Hub → Jenkins CD Stage → EC2 Instance → Docker Compose → Containers → Web Application


---

## Step 1: Prepare EC2 Instance for Deployment

1. Launch an EC2 instance (Amazon Linux)
2. Configure Security Group:
    - Allow SSH (Port 22)
    - Allow application port (e.g., 8080)
3. Connect to EC2:

```bash
ssh -i your-key.pem ec2-user@your-ec2-public-ip
```

4. Install Docker and Docker-Compose:

```bash
sudo yum update
sudo yum install docker
sudo systemctl start docker
sudo yum install docker-compose

```

5. Add ec2-user to Docker group:

```bash
sudo usermod -aG docker $USER
```

---

## Step 2: Create docker-compose.yaml file and commit to repo

Create a docker-compose.yaml and server-cmds.sh files which will be copied to the EC2 file by the Jenkins pipeline

---

---

## Step 3: Create SSH Credentials in Jenkins

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

## Step 4: Configure Security Group for Jenkins Server

In AWS EC2 Security Group:

Allow inbound traffic for Jenkins Server on:

Port 22 → SSH  


---

## Step 5: Extend Jenkins Pipeline with Deployment Stage

Add a deployment stage to your existing Jenkins pipeline.

Example Jenkinsfile:

```groovy
stage("deploy") {
    steps {
        script {
            echo 'deploying docker image to EC2...'

            def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
            def ec2Instance = "ec2-user@13.40.131.215"

            sshagent(['ec2-server-key']) {
                sh "scp server-cmds.sh ${ec2Instance}:/home/ec2-user"
                sh "scp docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
            }
        }
    }
}
```

---

## Step 6: Deployment Process

1. Developer pushes code to Git repository
2. Jenkins builds Java application using Maven
3. Docker image is built and pushed to Docker Hub
4. Jenkins connects to EC2 via SSH, copies docker-compose.yaml and deployment script
5. Deployment script is executed
6. Docker Compose pulls latest image
7. Existing containers are replaced with updated version

---

## Step 7: Access Deployed Application

Open browser:



```
http://your-ec2-public-ip:8080
```

---

## Deployment Workflow Summary

Code Push → Jenkins Build → Docker Image → Docker Hub → Jenkins Deploy → EC2 → Docker Compose → Application Running

---

## Benefits

Simplifies multi-container deployments  
Reusable deployment script improves maintainability  
Automated end-to-end deployment  
Easy rollback using Docker Compose  
Cleaner and more scalable deployment approach

---

## Security Best Practices

Use SSH key authentication  
Restrict EC2 security group ports  
Store credentials securely in Jenkins  
Use private Docker repositories  
Avoid running containers as root

---

