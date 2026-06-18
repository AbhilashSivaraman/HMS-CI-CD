# Create First Jenkins Pipeline Job (GitLab CI Integration)

## Overview
This guide explains how to create a Jenkins Pipeline job connected to a GitLab repository using SCM, configure GitLab integration, and set basic permissions for pipeline execution.

# Step 1: Create New Pipeline Job

Open Jenkins Dashboard:

Click → New Item

Job Name:
hotel-management-ci

Select Type:
Pipeline

Click:
OK

---

# Step 2: Configure Pipeline from SCM

Scroll to Pipeline section:

Definition:
Pipeline script from SCM

SCM:
Git

---

## Repository Configuration

Repository URL:
https://gitlab.company.com/team/backend-springboot.git

Credentials:
gitlab-creds

Branch Specifier:
*/main

Script Path:
Jenkinsfile

Click:
Save

---

# Step 3: Install GitLab Plugin

Go to:

Manage Jenkins → Plugins

Install:
- GitLab Plugin

Restart Jenkins if required.

---

# Step 4: Configure GitLab in Jenkins System

Go to:

Manage Jenkins → System

Find:
GitLab section

Configure:

GitLab Host URL:
https://gitlab.company.com

Credentials:
Select → gitlab-creds

Test Connection:
Verify successful connection to GitLab

Save configuration.

---

# Step 5: Configure GitLab Project Token (Optional but Recommended)

In GitLab:

Profile → Preferences → Access Tokens

Create token:
- Name: jenkins-integration-token
- Scopes: api, read_repository, write_repository

Copy token securely.

Add in Jenkins:

Manage Jenkins → Credentials → System → Global credentials → Add Credentials

| Field | Value |
|------|------|
| Kind | Secret text |
| Secret | <GitLab Token> |
| ID | gitlab-project-token |
| Description | GitLab Project Token |

Save.

---

# Step 6: Configure Matrix-Based Security

Go to:

Manage Jenkins → Security → Configure Global Security

Enable:
Matrix-based security

---

## Set Permissions

Add users/roles and assign:

| Category | Permission |
|----------|------------|
| Overall | Read |
| Job | Read |
| Job | Build |

---

## Recommended Additional Permissions (Optional)

For CI stability:

- Job → Workspace → Read
- Job → Cancel → Build

---

# Final Validation Checklist

## Jenkins Job

- Pipeline created ✔
- SCM configured ✔
- Jenkinsfile path correct ✔

## GitLab Integration

- GitLab plugin installed ✔
- Credentials configured ✔
- Repository accessible ✔

## Security

- Matrix-based security enabled ✔
- Job read/build permissions assigned ✔

---

# Final Outcome

After completing this setup:

- Jenkins can pull code from GitLab using SCM
- Pipeline executes automatically using Jenkinsfile
- GitLab authentication is secured via Jenkins credentials
- Job permissions are controlled via matrix-based security
- CI pipeline is ready for builds, SonarQube scans, and deployments