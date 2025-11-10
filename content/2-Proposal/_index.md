---
title: "Proposal"
date: 2025-11-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Security Scan Pipeline on AWS Cloud
## Automated Source Code Security Analysis for a DevSecOps Pipeline

### 1. Executive Summary
The Security Scan & Deployment Pipeline project aims to automate the entire build–scan–deploy lifecycle of an application on AWS Cloud, integrating directly with GitHub to implement a practical DevSecOps workflow.

Objectives:

- Detect security vulnerabilities and code smells early using Amazon CodeGuru Reviewer.
- Automatically build and produce artifacts on new commits.
- Store artifacts securely in Amazon S3.
- Automatically deploy applications to Amazon EC2 via AWS CodeDeploy.

This pipeline shortens release time, improves security posture, and ensures consistency across the team's DevSecOps processes.

### 2. Problem Statement
Current issues:

- Manual build and code checks can miss security defects.
- Application deployments to EC2 are not automated, which is time-consuming and error-prone.
- There is no centralized system to track artifacts or security scan reports.

Proposed solution:

Build an AWS CodePipeline connected to GitHub via webhook to automatically trigger the pipeline on each commit. The flow will be:

1. CodePipeline receives commit events from GitHub.
2. CodeBuild builds the source and runs security analysis using Amazon CodeGuru Reviewer.
3. Build artifacts and scan results are uploaded to Amazon S3.
4. CodeDeploy pulls artifacts from S3 and deploys them to EC2.
5. The entire process is observable via the AWS Console and CloudWatch Logs.

Benefits and ROI:

- Reduce manual deployment time by 60–70%.
- Improve security through automated CodeGuru analysis.
- Ensure a closed-loop CI/CD process, reducing misconfiguration risk.
- Lower operational costs through automation.

### 3. Solution Architecture
The pipeline is designed as a DevSecOps Serverless Hybrid architecture, combining AWS services to provide end-to-end automation.

Overview diagram:

![DevOps / Pipeline Engineer](/images/2-Proposal/diagram.png)

AWS services used:

- AWS CodePipeline: orchestrates automated pipeline stages.
- AWS CodeBuild: builds the project and runs security scans.
- Amazon CodeGuru Reviewer: analyzes source code and reports issues.
- Amazon S3: stores artifacts and scan outputs.
- AWS CodeDeploy: deploys artifacts to EC2 instances.
- Amazon EC2: runtime environment for the application.
- Amazon SNS: sends build/deploy notifications.
- CloudWatch, CloudTrail, GuardDuty, Detective, Security Hub: monitoring, investigation, and security aggregation.

### 4. Technical Deployment
Deployment stages:

1. Connect GitHub to CodePipeline: create a webhook and grant access via OIDC or a PAT; configure the pipeline to run on commits.
2. Create a CodeBuild project: environment (Ubuntu + Node.js/Python). Write a `buildspec.yml` to build and invoke CodeGuru.
3. Configure CodeDeploy: create a Deployment Group linked to EC2 instances and provide an `appspec.yml`.
4. Monitoring & security:
     - CloudWatch collects pipeline and application logs.
     - AWS CloudTrail records API activity and user actions.
     - GuardDuty detects suspicious behavior.
     - Detective helps analyze incident root causes.
     - Security Hub aggregates findings across services.
     - SNS sends pipeline status notifications to developers.

### 5. Roadmap & Milestones
- Pre-internship (September): 1 month planning — research, create IAM Roles, S3 bucket, EC2 instances, and connect GitHub.
- Internship (October–November):
    - October: configure CodePipeline and GitHub webhooks.
    - November: author `buildspec.yml` and `appspec.yml`, integrate CodeGuru, and add monitoring/security services (SNS, GuardDuty, Detective, Security Hub).
- Post-deployment: further research and improvements.

### 6. Budget Estimate
See the cost estimate on the AWS Pricing Calculator:
https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01
Or download the budget estimation file: `../attachments/budget_estimation.pdf`.

Estimated infrastructure costs:

- AWS CodePipeline: $0.40/month (2 pipelines running daily).
- AWS CodeBuild: $0.35/month (30 builds, 5 minutes each).
- Amazon S3: $0.10/month (artifacts & logs).
- AWS CodeDeploy: $0.20/month (2 deployments/month).
- Amazon EC2 (t2.micro): $0.10/month (application instance).
- CloudWatch + SNS: $0.05/month (monitoring & notifications).

Estimated total: ≈ $1.20/month, $14.40/year (assuming AWS Free Tier offsets).

### 7. Risk Assessment
Risk matrix:

- GitHub webhook failure — CodePipeline not triggered.
- Timeouts during CodeGuru analysis or CodeBuild runs.
- Missing or corrupted artifacts during deployment to EC2.
- Pipeline interruption due to IAM permission issues.
- Costs exceeding AWS Free Tier limits.

Mitigation strategies:

- Verify IAM Role configuration, OAuth tokens, and GitHub webhook triggers.
- Limit scan scope and optimize `buildspec.yml` to analyze only changed code.
- Run unit tests before building and use checksums to validate artifact integrity.
- Validate IAM policies for CodeBuild, CodeDeploy, S3, and SNS.
- Set up AWS Budgets and billing alerts via SNS email notifications.

Contingency plan:

- Manually trigger pipelines via the AWS Console or AWS CLI if automatic triggers fail.
- If timeouts occur, split the pipeline (e.g., separate build and scan stages) or run fallback jobs during off-peak hours.
- Use CodeDeploy rollback to revert to the previous artifact stored in S3 if a deployment fails.
- Create a backup IAM role with equivalent permissions to activate temporarily if the main role has issues.
- If costs spike, pause non-essential pipelines, clean up S3, and stop EC2 instances.

### 8. Expected Outcomes
Technical achievements:

- Full automation of CI/CD and security scanning.
- Continuous code and security analysis using CodeGuru.
- Seamless deployments via CodeDeploy to EC2.
- Comprehensive monitoring via CloudWatch, Detective, and Security Hub.
- Immediate email alerts through SNS.

Long-term value:

- A DevSecOps reference pattern suitable for workshops, learning, and real-world deployments.

Potential extensions:

- Automated testing.
- Deployments to ECS/EKS or AWS Lambda.
- Integrate additional scanners like Trivy, SonarQube, or Checkov for broader security coverage.
