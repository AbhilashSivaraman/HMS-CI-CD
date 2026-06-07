# SonarQube + Jenkins Integration Setup Guide

## Overview
This document explains how to integrate SonarQube with Jenkins for code quality analysis including SonarQube token creation, Jenkins configuration, SonarScanner setup, GitLab credentials setup, and first project creation in SonarQube.

# Step 1: Create SonarQube Token

Login to SonarQube:
http://<SONAR_PUBLIC_OR_PRIVATE_IP>:9000

Navigate:
Administrator → My Account → Security

Under Generate Tokens:
Name: jenkins-token  
Type: User Token  

Click Generate and copy the token immediately.

Example:
sqp_xxxxxxxxxxxxxxxxxxxxxxxxx

# Step 2: Configure SonarQube in Jenkins

Open Jenkins:
Manage Jenkins → System

Find:
SonarQube servers

Click Add SonarQube and fill:

Name: SonarQube  
Server URL: http://<SONAR_PRIVATE_IP>:9000  

Use private IP if Jenkins and SonarQube are in same VPC.

Add Credentials:
Kind: Secret text  
Secret: <SonarQube Token>  
ID: sonar-token  
Description: SonarQube Token  

Save and attach this credential under:
Server Authentication Token → sonar-token

# Step 3: Install SonarScanner in Jenkins

Go to:
Manage Jenkins → Tools

Find:
SonarQube Scanner installations

Click Add SonarQube Scanner:

Name: SonarScanner  
Install Automatically: Enabled  
Version: Latest  

Save configuration.

# Step 4: Create SonarQube Project

Open SonarQube:
Projects → Create Project

Project Key: foodcatalogue  
Display Name: foodcatalogue  

Select:
Locally

Jenkins will handle analysis.

# Step 5: Configure GitLab Credentials in Jenkins

In GitLab:
Profile → Preferences → Access Tokens

Create token:
Token Name: jenkins-token  
Scopes: read_repository, write_repository  

Copy token immediately.

# Step 6: Add GitLab Credentials in Jenkins

Open Jenkins:
Manage Jenkins → Credentials → System → Global credentials → Add Credentials

Fill:
Kind: Username with Password  
Username: <GitLab Username>  
Password: <GitLab Access Token>  
ID: gitlab-creds  
Description: GitLab Credentials  

Save.

# Final Outcome

After completing this setup:

- SonarQube token created and configured in Jenkins
- Jenkins connected to SonarQube server
- SonarScanner installed in Jenkins
- GitLab authentication configured in Jenkins
- First SonarQube project created (foodcatalogue)

This setup enables Jenkins to perform automated code quality analysis, enforce quality gates, and integrate SonarQube checks into CI/CD pipelines.