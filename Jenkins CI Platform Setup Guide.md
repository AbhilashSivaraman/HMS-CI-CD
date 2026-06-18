# Jenkins CI Platform Setup Guide (AWS Staging)

## Overview

Before creating another EKS cluster, ArgoCD deployment, or ALB, establish a solid CI foundation.

This guide provisions a Jenkins CI server with Docker support on AWS EC2.

---

# Infrastructure

## AWS Account

**Environment:** Staging

### EC2 Instance

| Parameter     | Value               |
| ------------- | ------------------- |
| Name          | `jenkins-sonarqube` |
| OS            | RHEL 9              |
| Instance Type | `t3.medium`         |
| Storage       | 30 GB               |

> **Note:** `t3.micro` or `t2.micro` instances are not recommended and may struggle under Jenkins workloads.

---

# Security Group Configuration

Create a Security Group with the following inbound rules:

| Port | Service | Source         |
| ---- | ------- | -------------- |
| 22   | SSH     | Your Public IP |
| 8080 | Jenkins | Your Public IP |

---

# Connect to Server

SSH into the EC2 instance:

```bash
ssh -i <key.pem> ec2-user@<EC2-PUBLIC-IP>
```

---

# System Update

Update all packages:

```bash
sudo dnf update -y
```

---

# Install Java 21

Install OpenJDK 21:

```bash
sudo dnf install java-21-openjdk java-21-openjdk-devel -y
```

Verify installation:

```bash
java -version
```

Expected output:

```text
openjdk version "21.x.x"
```

---

# Install Git

```bash
sudo dnf install git -y
```

Verify:

```bash
git --version
```

---

# Install Docker

## Add Docker Repository

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
```

## Install Docker Packages

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

## Enable and Start Docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

## Verify Docker

```bash
docker ps
```

Expected output:

```text
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES
```

---

# Install Jenkins

## Install wget

```bash
sudo dnf install wget -y
```

## Add Jenkins Repository

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

## Import Jenkins Key

```bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

## Install Jenkins

```bash
sudo dnf install jenkins -y
```

## Enable and Start Jenkins

```bash
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

## Verify Jenkins Service

```bash
sudo systemctl status jenkins
```

---

# Access Jenkins

Open Jenkins in your browser:

```text
http://<EC2-PUBLIC-IP>:8080
```

---

# Get Initial Admin Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password and complete the Jenkins setup wizard.

---

# Jenkins Initial Setup

During setup:

1. Install Plugins
2. Create Admin User
3. Configure Jenkins URL
4. Finish Setup

---

# Jenkins Logs

To monitor Jenkins logs:

```bash
sudo journalctl -u jenkins -f
```

---

# Install Required Jenkins Plugins

Navigate to:

```text
Manage Jenkins
└── Plugins
```

Install the following plugins:

* Docker
* Docker Pipeline
* Git
* Pipeline
* Credentials Binding
* Sonar Scanner
* Email Extension
* Gitlab
* Matrix Authorization

Restart Jenkins if prompted.

---

# Install Apache Maven 3.9.6

## Step 1: Download Maven

```bash
wget https://archive.apache.org/dist/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz
```

---

## Step 2: Extract Archive

```bash
tar -xvzf apache-maven-3.9.6-bin.tar.gz
```

---

## Step 3: Move Maven to /opt

```bash
sudo mv apache-maven-3.9.6 /opt/maven
```

---

## Install Nano Editor

```bash
sudo dnf install nano -y
```

---

## Step 4: Configure Environment Variables

Create Maven profile:

```bash
sudo nano /etc/profile.d/maven.sh
```

Add:

```bash
export M2_HOME=/opt/maven
export PATH=$M2_HOME/bin:$PATH
```

Save and exit.

---

## Step 5: Load Environment

```bash
source /etc/profile.d/maven.sh
```

---

## Step 6: Verify Maven Installation

```bash
mvn -version
```

Expected output:

```text
Apache Maven 3.9.6
Java version: 21
```

---

# Configure Maven in Jenkins

Navigate to:

```text
Manage Jenkins
└── Tools
    └── Maven Installations
```

Add Maven:

| Field      | Value       |
| ---------- | ----------- |
| Name       | Maven-3.9.6 |
| MAVEN_HOME | /opt/maven  |

Save configuration.

---

# Allow Jenkins to Use Docker

Add Jenkins user to Docker group:

```bash
sudo usermod -aG docker jenkins
```

---

# Restart Services

```bash
sudo systemctl restart docker
sudo systemctl restart jenkins
```

---

# Change Jenkins Shell to Bash

```bash
sudo usermod -s /bin/bash jenkins
```

---

# Verify Jenkins User Shell

```bash
grep jenkins /etc/passwd
```

Expected output:

```text
jenkins:x:992:991:Jenkins Automation Server:/var/lib/jenkins:/bin/bash
```

---

# Verify Docker Access from Jenkins

Switch to Jenkins user:

```bash
sudo su - jenkins
```

Run:

```bash
docker ps
```

If Docker responds successfully without permission errors, Jenkins is ready to build Docker images.

---

# Validation Checklist

## Java

```bash
java -version
```

Expected:

```text
Java 21
```

---

## Git

```bash
git --version
```

Expected:

```text
git version x.x.x
```

---

## Docker

```bash
docker ps
```

Expected:

```text
No permission errors
```

---

## Jenkins

```bash
sudo systemctl status jenkins
```

Expected:

```text
active (running)
```

---

## Maven

```bash
mvn -version
```

Expected:

```text
Apache Maven 3.9.6
Java 21
```

---

# Final Outcome

After completing this guide, the server will have:

* RHEL 9
* Java 21
* Git
* Docker Engine
* Docker Compose Plugin
* Jenkins
* Maven 3.9.6
* Docker-enabled Jenkins User
* CI/CD Foundation Ready

This environment is now ready for:

* GitHub Integration
* Jenkins Pipelines
* Docker Image Builds
* SonarQube Integration
* ECR Push Operations
* EKS Deployment Automation
* ArgoCD Integration