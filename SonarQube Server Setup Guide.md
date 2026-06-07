# SonarQube Server Setup Guide (AWS Staging)

## Overview

This guide provisions a dedicated SonarQube server on AWS EC2 using Docker.

The SonarQube server will be used for:

* Static Code Analysis
* Code Quality Gates
* Security Vulnerability Detection
* Jenkins Integration
* CI/CD Quality Validation

---

# Infrastructure

## AWS Account

**Environment:** Staging

---

# Create SonarQube EC2 Instance

Navigate to:

```text
AWS Console
└── EC2
    └── Launch Instance
```

## Instance Configuration

| Parameter     | Value          |
| ------------- | -------------- |
| Name          | `sonar-server` |
| OS            | RHEL 9         |
| Instance Type | `t3.medium`    |
| Storage       | 30 GB gp3      |

> **Note:** SonarQube requires more memory than lightweight EC2 instances can provide. A `t3.medium` is the recommended minimum for lab and staging environments.

---

# Security Group Configuration

Create a dedicated Security Group for SonarQube.

## Inbound Rules

| Type       | Port | Source                 |
| ---------- | ---- | ---------------------- |
| SSH        | 22   | Your Public IP         |
| Custom TCP | 9000 | Jenkins Security Group |
| Custom TCP | 9000 | Your Public IP         |

### Purpose

* Port **22** → Server administration
* Port **9000** → SonarQube Web UI
* Jenkins must be able to communicate with SonarQube over port **9000**

---

# Connect to Server

SSH into the instance:

```bash
ssh -i <key.pem> ec2-user@<SONAR_PUBLIC_IP>
```

---

# Update Server Packages

Update all installed packages:

```bash
sudo dnf update -y
```

---

# Install Java 17

## Why Java 17?

SonarQube LTS (2025/2026) runs on Java 17.

Install Java 17:

```bash
sudo dnf install java-17-openjdk java-17-openjdk-devel -y
```

Verify installation:

```bash
java --version
```

Expected output:

```text
openjdk 17.x.x
```

---

# Install Docker

## Add Docker Repository

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
```

---

## Install Docker Packages

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

---

## Enable Docker Service

```bash
sudo systemctl enable docker
```

---

## Start Docker Service

```bash
sudo systemctl start docker
```

---

## Add Current User to Docker Group

```bash
sudo usermod -aG docker ec2-user
```

> **Important:** Group membership changes will not take effect until you log out and reconnect.

---

## Reconnect to the Server

Exit the current session:

```bash
exit
```

Reconnect:

```bash
ssh -i <key.pem> ec2-user@<SONAR_PUBLIC_IP>
```

---

## Verify Docker Access

Run:

```bash
docker ps
```

Expected output:

```text
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES
```

No permission errors should be displayed.

---

# Deploy SonarQube Using Docker

Pull and run the SonarQube LTS Community Edition container:

```bash
docker run -d \
  --name sonarqube \
  -p 9000:9000 \
  sonarqube:lts-community
```

---

# Verify Container Status

Check running containers:

```bash
docker ps
```

Expected output:

```text
CONTAINER ID   IMAGE                     STATUS
xxxxxxxxxx     sonarqube:lts-community   Up
```

---

# Check SonarQube Logs

If startup takes longer than expected, monitor logs:

```bash
docker logs -f sonarqube
```

> SonarQube may take several minutes to complete its initial startup.

---

# Access SonarQube

Open the following URL in your browser:

```text
http://<SONAR_PUBLIC_IP>:9000
```

---

# Default Login Credentials

| Username | Password |
| -------- | -------- |
| admin    | admin    |

Login using the default credentials.

---

# Change Default Password

After the first login, SonarQube will prompt you to change the default password.

Choose a strong password and store it securely.

---

# Validation Checklist

## Verify Java

```bash
java --version
```

Expected:

```text
Java 17
```

---

## Verify Docker

```bash
docker ps
```

Expected:

```text
No permission errors
```

---

## Verify SonarQube Container

```bash
docker ps
```

Expected:

```text
sonarqube:lts-community
```

---

## Verify Web Access

Open:

```text
http://<SONAR_PUBLIC_IP>:9000
```

Expected:

```text
SonarQube Login Page
```

---

# Network Connectivity Test from Jenkins Server

From the Jenkins EC2 instance:

```bash
curl http://<SONAR_PRIVATE_IP>:9000
```

or

```bash
curl http://<SONAR_PUBLIC_IP>:9000
```

Expected response:

```html
<!doctype html>
<html>
...
```

This confirms Jenkins can communicate with SonarQube.

---

# Final Outcome

After completing this guide, the SonarQube server will have:

* RHEL 9
* Java 17
* Docker Engine
* SonarQube LTS Community Edition
* Web UI on Port 9000
* Jenkins Connectivity Enabled
* Code Quality Analysis Platform Ready

This environment is now ready for:

* Jenkins Integration
* Sonar Scanner Configuration
* Quality Gates
* Pull Request Analysis
* Security Scanning
* CI/CD Pipeline Enforcement